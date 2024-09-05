### 需求分析与设计

![[Pasted image 20240603164511.png]]

![[Pasted image 20240603164532.png]]

![[Pasted image 20240603164550.png|575]]

### 代码开发

![[Pasted image 20240603164720.png]]

ReportController：

```java
/**  
 * 导出运营数据报表  
 * @param response  
 */  
@GetMapping("/export")  
@ApiOperation("导出运营数据报表")  
public void export(HttpServletResponse response){  
    reportService.exportBusinessData(response);  
}
```

ReportService：

```java
/**  
 * 导出运营数据报表  
 * @param response  
 */  
void exportBusinessData(HttpServletResponse response);
```

```java
/**  
 * 导出运营数据报表  
 * @param response  
 */  
public void exportBusinessData(HttpServletResponse response) {  
    //1. 查询数据库，获取营业数据---查询最近30天的运营数据  
    LocalDate dateBegin = LocalDate.now().minusDays(30);  
    LocalDate dateEnd = LocalDate.now().minusDays(1);  
  
    //查询概览数据  
    BusinessDataVO businessDataVO = workspaceService.getBusinessData(LocalDateTime.of(dateBegin, LocalTime.MIN), LocalDateTime.of(dateEnd, LocalTime.MAX));  
  
    //2. 通过POI将数据写入到Excel文件中  
    InputStream in = this.getClass().getClassLoader().getResourceAsStream("template/运营数据报表模板.xlsx");  
  
    try {  
        //基于模板文件创建一个新的Excel文件  
        XSSFWorkbook excel = new XSSFWorkbook(in);  
  
        //获取表格文件的Sheet页  
        XSSFSheet sheet = excel.getSheet("Sheet1");  
  
        //填充数据--时间  
        sheet.getRow(1).getCell(1).setCellValue("时间：" + dateBegin + "至" + dateEnd);  
  
        //获得第4行  
        XSSFRow row = sheet.getRow(3);  
        row.getCell(2).setCellValue(businessDataVO.getTurnover());  
        row.getCell(4).setCellValue(businessDataVO.getOrderCompletionRate());  
        row.getCell(6).setCellValue(businessDataVO.getNewUsers());  
  
        //获得第5行  
        row = sheet.getRow(4);  
        row.getCell(2).setCellValue(businessDataVO.getValidOrderCount());  
        row.getCell(4).setCellValue(businessDataVO.getUnitPrice());  
  
        //填充明细数据  
        for (int i = 0; i < 30; i++) {  
            LocalDate date = dateBegin.plusDays(i);  
            //查询某一天的营业数据  
            BusinessDataVO businessData = workspaceService.getBusinessData(LocalDateTime.of(date, LocalTime.MIN), LocalDateTime.of(date, LocalTime.MAX));  
  
            //获得某一行  
            row = sheet.getRow(7 + i);  
            row.getCell(1).setCellValue(date.toString());  
            row.getCell(2).setCellValue(businessData.getTurnover());  
            row.getCell(3).setCellValue(businessData.getValidOrderCount());  
            row.getCell(4).setCellValue(businessData.getOrderCompletionRate());  
            row.getCell(5).setCellValue(businessData.getUnitPrice());  
            row.getCell(6).setCellValue(businessData.getNewUsers());  
        }  
  
        //3. 通过输出流将Excel文件下载到客户端浏览器  
        ServletOutputStream out = response.getOutputStream();  
        excel.write(out);  
  
        //关闭资源  
        out.close();  
        excel.close();  
    } catch (IOException e) {  
        e.printStackTrace();  
    }  
}
```

### 测试

![[Pasted image 20240603170757.png]]