

# word 生成

[参考资料]:https://www.cnblogs.com/fqfanqi/p/6172223.html



#  word转pdf

## word文档占位符写入问题

[参考资料]:https://blog.csdn.net/hmily_hui/article/details/78800961

### word原始信息

<img src="https://raw.githubusercontent.com/lilight49/markdown_photo/master/18C74C08-EC13-4646-BA7D-A7F9012A2317.png" width=800 height=150 align="left">

```docx
${nhxm}    乡镇 ${nhm} 村            编号：  ${htbh}
甲方（农户）：                        电话：
乙方（服务组织）：                    电话： 
```

### <font color=pink>word文档另存为xml格式的文档将样式修改完成后再保存为word文档</font>

### <font color=yellow>word文档中样式会将占位符进行切分--示例将  ${htbh} 切分为如下格式</font>

```xml
<w:t>${</w:t>undefined</w:r>undefined<w:proofErr w:type="spellStart"/>undefined<w:r w:rsidR="00A602AC" w:rsidRPr="00A476C1">
<w:rPr>
    <w:rFonts w:ascii="仿宋" w:eastAsia="仿宋" w:hAnsi="仿宋"/>
    <w:sz w:val="28"/>
    <w:szCs w:val="28"/>
    <w:u w:val="single"/>
</w:rPr>
<w:t>htbh</w:t>undefined</w:r>undefined<w:proofErr w:type="spellEnd"/>undefined<w:r w:rsidR="00A602AC" w:rsidRPr="00A476C1">
<w:rPr>
    <w:rFonts w:ascii="仿宋" w:eastAsia="仿宋" w:hAnsi="仿宋"/>
    <w:sz w:val="28"/>
    <w:szCs w:val="28"/>
    <w:u w:val="single"/>
</w:rPr>
<w:t>}</w:t>
```

### <font color=yellow>修改为如下格式</font>

```xml
<w:t>${htbh}</w:t>
```

# word中动态复制表格信息

[参考资料]:https://blog.csdn.net/chen417980762/article/details/106653708/

```java
//word信息倒叙复制,倒叙添加,倒叙删除
private static void handleReportAddTable(XWPFTable table, Map<String, Object> param) throws IOException, XmlException {
    List<EnterpriseReportCrop> enterpriseReportCropList = (List<EnterpriseReportCrop>) param.get("enterpriseReportCropList");

    int listSize = enterpriseReportCropList.size ();
    int startRow = 8;
    int stride = 3;//步长--每一个列表占用的有效表格的行数

    for (int index=2; index <=listSize; index++){
      int coupyStartRow = startRow + (stride * (index-1));
      EnterpriseReportCrop enterpriseReportCrop = enterpriseReportCropList.get(index-1);

        //动态复制表格的信息
      CTRow threeCtRow = CTRow.Factory.parse(table.getRow(10).getCtRow().newInputStream());
      XWPFTableRow threeRow = new XWPFTableRow(threeCtRow, table);
      threeRow.getTableCells().get(1).getParagraphs().get(0).getRuns().get(0).setText(PriceChangeUtil.cancelEndZero(enterpriseReportCrop.getPlowWorkArea()), 0);
      threeRow.getTableCells().get(2).getParagraphs().get(0).getRuns().get(0).setText(PriceChangeUtil.cancelEndZero(enterpriseReportCrop.getGrowWorkArea()), 0);
      threeRow.getTableCells().get(3).getParagraphs().get(0).getRuns().get(0).setText(PriceChangeUtil.cancelEndZero(enterpriseReportCrop.getPreventWorkArea()), 0);
      threeRow.getTableCells().get(4).getParagraphs().get(0).getRuns().get(0).setText(PriceChangeUtil.cancelEndZero(enterpriseReportCrop.getReapWorkArea()), 0);
      threeRow.getTableCells().get(5).getParagraphs().get(0).getRuns().get(0).setText(PriceChangeUtil.cancelEndZero(enterpriseReportCrop.getOtherWorkArea()), 0);
      table.addRow(threeRow,coupyStartRow);//表格中添加一行表格信息

      CTRow twoCtRow = CTRow.Factory.parse(table.getRow(9).getCtRow().newInputStream());
      XWPFTableRow twoRow = new XWPFTableRow(twoCtRow, table);
      table.addRow(twoRow,coupyStartRow);

      CTRow onwCtRow = CTRow.Factory.parse(table.getRow(8).getCtRow().newInputStream());
      XWPFTableRow oneRow = new XWPFTableRow(onwCtRow, table);
      oneRow.getTableCells().get(0).getParagraphs().get(0).getRuns().get(0).setText(enterpriseReportCrop.getCropDicName(), 0);
      table.addRow(oneRow,coupyStartRow);
    }

  }
```



# word中动态添加段落信息

[参考资料]:https://blog.csdn.net/weixin_41565034/article/details/86300954

```java
private static void handleContractParagraph(XWPFDocument doc, FileSourceEnum fileSourceEnum, Map<String, Object> param) {
    try {
      if(fileSourceEnum.getCode().equals(10003)){//辽宁合同模板特殊处理
        int startParagraphRow = 6;//开始的段数行数
        int stride = 1;//每次增加的步长
        int insertParagraphRow = startParagraphRow + stride;

        List<Map<String, Object>> plotDetailList = (List<Map<String, Object>>) param.get("plotDetailList");

        for (Map<String, Object> plotDetailMap : plotDetailList) {//动态插入段落
          XWPFParagraph sexXwpfParagraph = doc.getParagraphs().get(insertParagraphRow);// insertParagraphRow 为光标停留的行,插入方法向光标的上一行插入段落
          XmlCursor cursor = sexXwpfParagraph.getCTP().newCursor();
          XWPFParagraph newXWPFParagraph = doc.insertNewParagraph(cursor);
          XWPFRun newXWPFRun = newXWPFParagraph.createRun();
        }

        for (Map<String, Object> plotDetailMap : plotDetailList) {
          //word动态设置段落信息
          CTP ctp = CTP.Factory.parse(doc.getParagraphs().get(6).getCTP().newInputStream());
          XWPFParagraph xwpfParagraph = new XWPFParagraph(ctp ,doc);
          xwpfParagraph.getRuns().get(2).setText(plotDetailMap.get("${contractArea}").toString(),0);
          xwpfParagraph.getRuns().get(10).setText(plotDetailMap.get("${cropDicName}").toString(),0);
          xwpfParagraph.getRuns().get(16).setText(plotDetailMap.get("${workDicName}").toString(),0);
          xwpfParagraph.getRuns().get(29).setText(plotDetailMap.get("${areaDicName}").toString(),0);

          doc.setParagraph(xwpfParagraph, insertParagraphRow);//word中替换段落信息

          insertParagraphRow++;
        }

        doc.removeBodyElement(startParagraphRow);//移除原有的模板段落
      }
    }catch (Exception e) {
      e.printStackTrace();
    }
  }
```





# linux字体库添加

[参考资料]:https://blog.csdn.net/azhegps/article/details/79385809?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~all~first_rank_v2~rank_v25-1-79385809.nonecase&utm_term=linux%E6%9F%A5%E7%9C%8B%E5%AD%97%E4%BD%93%E5%AE%89%E8%A3%85%E5%9C%A8%E5%93%AA%E9%87%8C&spm=1000.2123.3001.4430

### 字体库地址

```sh
/usr/share/fonts/pdfFons.tar.gz
```

### 字体库初始化

```sh
fc-cache -fv
```

### 查看字体库是否加载成功

```sh
fc-list
```

### 最后重新启动项目加载使用新字体



### 字体库安装

[参考资料]:https://blog.csdn.net/wlwlwlwl015/article/details/51482065

<font color=DarkOrange>在字体库找不的时候,需要安装字体库</font>

```sh
-bash: fc-list: command not found #命令找不到
```

```sh
yum -y install fontconfig #初始化字体库
```

