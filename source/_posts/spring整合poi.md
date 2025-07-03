---
title: SpringBoot整合poi实现对文档编辑并转为pdf
date : 2024-8-29 21:10:05
tags: 
- spring-boot
- poi
categories: 学习

---

## 1.引入maven依赖

```xml
     		<pdfbox.version>2.0.27</pdfbox.version>
            <poi.version>3.17</poi.version>
            <xdocreport.version>2.0.1</xdocreport.version>
            <documents4j.version>1.0.3</documents4j.version>
            <pdfbox.new.version>3.0.0-alpha2</pdfbox.new.version>  

<!-- Apache POI dependencies -->
                <dependency>
                    <groupId>org.apache.poi</groupId>
                    <artifactId>poi</artifactId>
                    <version>${poi.version}</version>
                </dependency>
                <dependency>
                    <groupId>org.apache.poi</groupId>
                    <artifactId>poi-ooxml-schemas</artifactId>
                    <version>${poi.version}</version>
                </dependency>
                <dependency>
                    <groupId>org.apache.poi</groupId>
                    <artifactId>poi-ooxml</artifactId>
                    <version>${poi.version}</version>
                </dependency>

                <!-- XDocReport dependency -->
                <dependency>
                    <groupId>fr.opensagres.xdocreport</groupId>
                    <artifactId>fr.opensagres.poi.xwpf.converter.pdf-gae</artifactId>
                    <version>${xdocreport.version}</version>
                </dependency>

                <!-- Documents4J dependencies -->
                <dependency>
                    <groupId>com.documents4j</groupId>
                    <artifactId>documents4j-local</artifactId>
                    <version>${documents4j.version}</version>
                </dependency>
                <dependency>
                    <groupId>com.documents4j</groupId>
                    <artifactId>documents4j-transformer-msoffice-word</artifactId>
                    <version>${documents4j.version}</version>
                </dependency>
                <!-- PDF to Image conversion dependencies -->
                <dependency>
                    <groupId>org.apache.pdfbox</groupId>
                    <artifactId>pdfbox</artifactId>
                    <version>${pdfbox.new.version}</version>
                </dependency>
                <dependency>
                    <groupId>org.apache.pdfbox</groupId>
                    <artifactId>fontbox</artifactId>
                    <version>${pdfbox.new.version}</version>
                </dependency>
```

## 2.编写工具类

```java
package com.wf.utils;

import cn.hutool.core.io.resource.ClassPathResource;
import com.documents4j.api.DocumentType;
import com.documents4j.api.IConverter;
import com.documents4j.job.LocalConverter;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.apache.pdfbox.Loader;
import org.apache.pdfbox.pdmodel.PDDocument;
import org.apache.pdfbox.rendering.PDFRenderer;
import org.apache.poi.xwpf.usermodel.*;

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.io.*;
import java.util.Iterator;
import java.util.List;
import java.util.Map;


/**
 * @description: 工具类
 */
@Slf4j
public class WordUtils {

    /**
     * @param path    模版路径
     * @param outPath 输出路径
     * @param dict    需要替换的信息集合
     * @Date: 2023/2/20 17:42
     * @Return: boolean
     * @description: 根据dict编译模版中的文本和表格
     */
    public static void compile(String path, String outPath, Map<String, Object> dict) throws Exception {
        ClassPathResource resource = new ClassPathResource(path);
        InputStream is = resource.getStream();
        XWPFDocument document = new XWPFDocument(is);
        if (dict != null) {
            // 替换掉表格之外的文本(仅限文本)
            WordUtils.compileText(document, dict);
            // 替换表格内的文本对象
            WordUtils.compileTable(document, dict);
        }
        File f = new File(outPath.substring(0, outPath.lastIndexOf(File.separator)));
        if (!f.exists()) {
            f.mkdirs();
        }
        FileOutputStream out = new FileOutputStream(outPath);
        document.write(out);


    }

    /***
     * @Description :替换段落文本
     * @param document docx解析对象
     * @param dict 需要替换的信息集合
     * @return void
     * @Date 2022/11/17 17:22
     */
    public static void compileText(XWPFDocument document, Map<String, Object> dict) {
        // 获取段落集合
        Iterator<XWPFParagraph> iterator = document.getParagraphsIterator();
        XWPFParagraph paragraph;
        while (iterator.hasNext()) {
            paragraph = iterator.next();
            // 判断此段落是否需要替换
            if (checkText(paragraph.getText())) {
                replaceValue(paragraph, dict);
            }
        }
    }

    /***
     * @Description :替换表格内的文字
     * @param document
     * @param dict 需要替换的信息集合
     * @return void
     * @Date 2022/11/18 11:29
     */
    public static void compileTable(XWPFDocument document, Map<String, Object> dict) {
        // 获取文件的表格
        Iterator<XWPFTable> tableList = document.getTablesIterator();
        XWPFTable table;
        List<XWPFTableRow> rows;
        List<XWPFTableCell> cells;
        // 循环所有需要进行替换的文本，进行替换
        while (tableList.hasNext()) {
            table = tableList.next();
            if (checkText(table.getText())) {
                rows = table.getRows();
                // 遍历表格，并替换模板
                for (XWPFTableRow row : rows) {
                    cells = row.getTableCells();
                    for (XWPFTableCell cell : cells) {
                        // 判断单元格是否需要替换
                        if (checkText(cell.getText())) {
                            List<XWPFParagraph> paragraphs = cell.getParagraphs();
                            for (XWPFParagraph paragraph : paragraphs) {
                                replaceValue(paragraph, dict);
                            }
                        }
                    }
                }
            }
        }
    }

    /**
     * @param paragraph word文本
     * @param dict      需要替换的信息集合
     * @Date: 2023/2/20 17:31
     * @description: 替换字符串
     */
    private static void replaceValue(XWPFParagraph paragraph, Map<String, Object> dict) {
        String nextLine;
        paragraph.setStyle("");
        List<XWPFRun> runs = paragraph.getRuns();
        for (int i = 0; i < runs.size(); i++) {
            // 读取当前行
            String readLine = runs.get(i).text();
//            System.out.println("readLine:" + readLine);
            // 如果为空 或 不包含目标字符串 则跳过
            if (StringUtils.isEmpty(readLine) || !readLine.contains("$")) continue;
            // 初始化结果集
            StringBuffer sb = new StringBuffer();

            // 循环处理当前行的模版串
            if (readLine.contains("$")) {
                // 获取模版串左侧字符串
                int left;
                if (readLine.contains("${")) {
                    left = readLine.indexOf("${");
                } else {
                    if (runs.size() < i + 1) {
                        break;
                    }
                    nextLine = runs.get(i + 1).text();
                    if (!nextLine.startsWith("{")) break;
                    readLine += nextLine;
                    paragraph.removeRun(i + 1);
                    left = readLine.indexOf("${");
                }
                sb.append(readLine.substring(0, left));
                // 获取模版串右侧
                while (runs.size() >= i + 1 && !readLine.contains("}")) {
                    nextLine = runs.get(i + 1).text();
                    readLine += nextLine;
                    paragraph.removeRun(i + 1);
                }
                int right = readLine.indexOf("}");
                if (right == -1) break;
                // 替换模版串 [如果字典中不存在,不替换]
                sb.append(dict.getOrDefault(readLine.substring(left, right + 1), readLine));

                if (right + 1 < readLine.length()) {
                    sb.append(readLine.substring(right + 1));
                }

            }
            // 如果字符串中包含$ 将颜色改为白色
            if(WordUtils.checkText(sb.toString()))
            {
                runs.get(i).setColor("ffffff");
            }else
            {
                runs.get(i).setColor("f56026");
            }
            runs.get(i).setText(sb.toString(), 0);
        }
    }


    /***
     * @Description :检查文本中是否包含指定的字符(此处为“$”)
     * @param text
     * @return boolean
     * @Date 2022/11/17 17:22
     */
    private static boolean checkText(String text) {
        return text.contains("$");
    }


    /**
     * 通过documents4j 实现word转pdf
     *
     * @param sourcePath 源文件地址 如 /root/example.doc
     * @param targetPath 目标文件地址 如 /root/example.pdf
     */
    public static void documents4jWordToPdf(String sourcePath, String targetPath) {
        File inputWord = new File(sourcePath);
        File outputFile = new File(targetPath);
        try {
            InputStream docxInputStream = new FileInputStream(inputWord);
            OutputStream outputStream = new FileOutputStream(outputFile);
            IConverter converter = LocalConverter.builder().build();
            boolean execute = converter.convert(docxInputStream)
                    .as(DocumentType.DOCX)
                    .to(outputStream)
                    .as(DocumentType.PDF).schedule().get();
            outputStream.close();
            docxInputStream.close();

            log.info("转换完毕 targetPath = {}", outputFile.getAbsolutePath());
            System.out.println("转换完毕 targetPath = " + outputFile.getAbsolutePath());
            converter.shutDown();
            return;
        } catch (Exception e) {
            log.error("[documents4J] word转pdf失败:{}", e.toString());
        }
    }

    /**
     * 将生成的 Word 文档转换为 PDF 格式
     *
     * @param wordPath Word 文档路径
     * @param pdfPath  生成的 PDF 路径
     */
    public static void convertToPDF(String wordPath, String pdfPath) {
        documents4jWordToPdf(wordPath, pdfPath);
    }


    /**
     * PDF文件转图片
     *
     * @param sourcePath PDF文件地址
     */
    public static void execute(String sourcePath) {
        File file = new File(sourcePath);
        String path = file.getAbsolutePath();
        String targetPathNoExt = path.substring(0, path.lastIndexOf("."));
        try {
            PDDocument doc = Loader.loadPDF(file);
            PDFRenderer renderer = new PDFRenderer(doc);
            int pageCount = doc.getNumberOfPages();
            for (int i = 0; i < pageCount; i++) {
                // System.out.println("当前页面" + (i + 1));
                BufferedImage image = renderer.renderImageWithDPI(i, 296);
                // BufferedImage image = renderer.renderImage(i, 2.5f);
                ImageIO.write(image, "PNG", new File(targetPathNoExt + "_" + i + ".png"));
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

## 3.编写文档模版

```docx
租房合同
1. 合同双方
出租方（甲方）
姓名/公司名称：${landlordName}
地址：${landlordAddress}
联系方式：${landlordPhone}
承租方（乙方）
姓名：${tenantName}
身份证号：${tenantIdCard}
地址：${tenantAddress}
联系方式：${tenantPhone}
2. 房屋基本情况
房屋位置 :  ${houseLocation}
房屋面积：${houseArea}
房屋结构：${houseStructure}
公共设施：${publicFacilities}
能源设施：${energyFacilities}
3. 租赁期限
开始日期：${startDate}
结束日期：${endDate}
租期总计：${totalLeasePeriod}
4. 租金及支付方式
每月租金：${monthlyRent}
支付周期：${paymentCycle}
支付方式：在线支付
5. 房屋使用规定
使用目的：乙方租赁该房屋仅用于居住用途，不得用作商业、办公或其他非法活动。
维护责任：乙方应妥善使用并维护房屋及其配套设施，如因乙方原因造成损坏，乙方需承担修复费用。
禁止行为：
不得擅自改变房屋结构或装修；
不得将房屋转租给第三方；
不得从事违法活动；
不得饲养宠物。
其他约定：
乙方应按时缴纳水、电、燃气等费用；
未经甲方同意，乙方不得擅自安装或拆除房屋内的设备设施。
6. 违约责任
违约情形：
乙方未按约定支付租金；
乙方违反使用规定；
甲方未按约定提供房屋或配套设施。
违约金计算方法：
若乙方逾期支付租金，每逾期一天需支付相当于日租金的滞纳金；
若乙方违反使用规定，需支付违约金人民币 5000 元；
若甲方未按约定提供房屋或配套设施，需退还相应租金并支付违约金人民币 5000 元。
解决争议的方式：
双方应首先通过友好协商解决争议；
如协商不成，可提交至房屋所在地人民法院诉讼解决。
7. 合同变更与解除
变更条件：
双方协商一致后可对合同进行变更；
变更内容需书面形式确认。
解除条件：
一方严重违约，经催告仍不改正；
因不可抗力导致合同无法履行。
提前终止合同的处理办法：
任何一方要求提前终止合同，需提前一个月书面通知对方；
提前终止合同的一方需支付违约金人民币 5000 元。
8. 附则
本合同自双方签字盖章之日起生效。
本合同一式两份，甲乙双方各执一份，具有同等法律效力。
其他补充条款：
甲方应在乙方入住前提供房屋钥匙及相关设施的使用说明；
乙方应遵守小区物业管理规定。
希望这些内容能帮助您更好地制定租房合同。如果有其他需求或修改，请随时告知。

```



## 4.编写测试类



```java
import cn.hutool.core.date.DatePattern;
import cn.hutool.core.date.DateUtil;
import com.wf.dto.ContractDTO;
import com.wf.dto.ContractMap;
import com.wf.utils.WordUtils;
import org.junit.jupiter.api.Test;

import java.util.HashMap;
import java.util.Map;

/**
 * @author mpk
 * @since 2024/8/29 13:23:08
 */
public class UtilsTest {

    @Test
    public void test1() throws Exception {

        ContractDTO dto = new ContractDTO();
        dto.setLandlordName("lisi");
        dto.setLandlordPhone("1234567890");
        dto.setLandlordAddress("北京");

        dto.setTenantName("张三");
        dto.setTenantPhone("1234567890");
        dto.setTenantAddress("北京");
        dto.setTenantIdCard("123456789012345678");


        dto.setHouseArea("100");
        dto.setPublicFacilities("无");
        dto.setEnergyFacilities("无");

        dto.setStartDate(DateUtil.format(DateUtil.parse("2024-08-29"), DatePattern.NORM_DATE_PATTERN));
        dto.setEndDate(DateUtil.format(DateUtil.parse("2024-08-29"), DatePattern.NORM_DATE_PATTERN));
        dto.setTotalLeasePeriod("1");



        dto.setPaymentCycle("月");
        dto.setMonthlyRent("1000");
        ContractMap map = new ContractMap(dto);
        String readPath = "template/contractTemplate.docx"; //word模板
        String outPath = "E:\\output1.docx"; //输出填充word路径
        String outPdfPath = "E:\\output1.pdf"; //输入word转pdf路径

        WordUtils.compile(readPath, outPath, map); //填充word，如果只要word转pdf这里可以不要

        // 转换操作，可单独使用
        WordUtils.convertToPDF(outPath,outPdfPath); //word转pdf
        WordUtils.execute(outPdfPath); //pdf转图片png
    }

  

}

```