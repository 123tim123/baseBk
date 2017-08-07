---
title: java poi封装设计思维
tags: [java]
---
# poi的简单封装与封装思维分享

>- 文章的目的是分享怎样提高使用poi的效率，已经提高代码的可读性、编程性、维护性   
>- 通常看到许多程序员使用poi导出execle的时候都是一个方法写下来，几乎没有使用面向对象编程这个优势，这可以能是j2e程序员长期以来开发的思维固定有关，没有使用更多设计模式与面向对象编程。

### 让我们先看一段传统j2e程序员同事使用poi导出部分代码

```
 public static HSSFWorkbook createWB(String fileName,String titleName,List list, String wbName)
			    throws NoSuchMethodException, InstantiationException, IllegalAccessException, InvocationTargetException, ParseException
		{
		  	HSSFWorkbook wb = new HSSFWorkbook();
		    HSSFSheet sheet = wb.createSheet(wbName);
	        // 设置表格默认列宽度为1个字节  
	        sheet.setDefaultColumnWidth((short) 1);   
	        // 生成一个样式  
	        HSSFCellStyle style = wb.createCellStyle();  
	        // 设置这些样式  
	        style.setFillForegroundColor(HSSFColor.SKY_BLUE.index);  
	        style.setFillPattern(HSSFCellStyle.SOLID_FOREGROUND);  
	        style.setBorderBottom(HSSFCellStyle.BORDER_THIN);  
	        style.setBorderLeft(HSSFCellStyle.BORDER_THIN);  
	        style.setBorderRight(HSSFCellStyle.BORDER_THIN);  
	        style.setBorderTop(HSSFCellStyle.BORDER_THIN);  
	        style.setAlignment(HSSFCellStyle.ALIGN_CENTER);  
	        // 生成一个字体  
	        HSSFFont font = wb.createFont();  
	        font.setColor(HSSFColor.VIOLET.index);  
	        font.setFontHeightInPoints((short) 12);  
	        font.setBoldweight(HSSFFont.BOLDWEIGHT_BOLD);  
	        // 把字体应用到当前的样式  
	        style.setFont(font); 
		    
	        List<QuestionnaireQuestionBankVO> bankVOList = list;
	        int rownum = 0;
		    HSSFRow row = sheet.createRow(rownum);
		    HSSFCell cell = null;
		    List<QuestionnaireQuestionBankVO> headList = new ArrayList<>();
		    List<QuestionnaireQuestionBankVO> bodyList = new ArrayList<>();
		    List<QuestionnaireQuestionBankVO> shootList = new ArrayList<>();
		    
		    int maxSize =60;
		    for(QuestionnaireQuestionBankVO bankVO : bankVOList){
		    	if("1".equals(bankVO.getQuestionClassify())){
		    		bodyList.add(bankVO);
		    	}else if("2".equals(bankVO.getQuestionClassify())){
		    		headList.add(bankVO);
		    	}else if("3".equals(bankVO.getQuestionClassify())){
		    		shootList.add(bankVO);
		    	}	
		    }

		    //标题
		    for(int i=0;i<maxSize;i++){
		    	cell = row.createCell(i);
		    	if(i==0){
		    		cell.setCellValue(titleName);
		    	}
		    }
		    sheet.addMergedRegion(new CellRangeAddress(0,0,0,maxSize-1));
		    
		    //表头
		    int initHeadNum=0;
		    for(QuestionnaireQuestionBankVO headVO : headList){
		    	if(initHeadNum<2){
		    		if(initHeadNum<1){
		    			row = sheet.createRow(++rownum);
		    		}
		    	    for(int a=initHeadNum*30;a<(initHeadNum+1)*30;a++){
		    	    	cell = row.createCell(a);
		    	    	if(a==0 || a==30){
		    	    		String value = headVO.getQuestionTitle();//题目名称	    	    		
			    	    	if(!"3".equals(headVO.getQuestionType())){//单选多选
			    	    		List<QuestionnaireQuestionOptionVO> optionList = headVO.getQuestionnaireQuestionOptionVO();
			    	    		for(QuestionnaireQuestionOptionVO optionVO : optionList){
			    	    			value+="   □  " + optionVO.getOptionName();
			    	    		}
			    	    	}
			    	    	 cell.setCellValue(value);
		    	    	}      
				    }  
		    	}  
		        sheet.addMergedRegion(new CellRangeAddress(rownum,rownum,initHeadNum*30,(initHeadNum+1)*30-1));
	    	    initHeadNum++;
	    	    if(initHeadNum>=2){
	    	    	initHeadNum=0;
	    	    }
		    }
		    
		    //题目
		    for(QuestionnaireQuestionBankVO bodyVO : bodyList){
		    	String questionType = bodyVO.getQuestionType();
		    	String isrequired = bodyVO.getIsRequired();
		    	
		    	if("1".equals(questionType)){
		    		questionType="单选题";
		    	}else if("2".equals(questionType)){
		    		questionType="多选题";
		    	}else if("3".equals(questionType)){
		    		questionType="填空题";
		    	}
		    	
		    	if("0".equals(isrequired)){
		    		isrequired="";
		    	}else if("1".equals(isrequired)){
		    		isrequired="*";
		    	}
		    	
		    	if("3".equals(bodyVO.getQuestionType())){
		    		row = sheet.createRow(++rownum);
		    		for(int i=0;i<60;i++){
		    			cell = row.createCell(i);
		    			String value="";
		    			if(i==0){
		    				value = bodyVO.getQuestionTitle() + " " + isrequired + " " + "【" + questionType + "】";//题目名称
		    			}
		    			 cell.setCellValue(value+"\n");
		    		}
		    		sheet.addMergedRegion(new CellRangeAddress(rownum,rownum,0,59));
		    	}else{
		    		String groupingLayoutPc = bodyVO.getGroupingLayoutPc();//分组布局
		    		String optionLayoutPc = bodyVO.getOptionLayoutPc();//选项布局
		    		List groupName= new ArrayList<>();//分组名称数组
		    		List<QuestionnaireQuestionOptionVO> optionVOList = bodyVO.getQuestionnaireQuestionOptionVO();
		    		for(QuestionnaireQuestionOptionVO optionVO : optionVOList){
		    			Boolean result = true;
		    			String groupingName = optionVO.getGroupingName()==null?"":optionVO.getGroupingName();
		    			for(Object s : groupName){	    				
		    				if(groupingName.equals(s)){
		    					result=false;
			    			}
			    		}
		    			if(result){
		    				groupName.add(groupingName);
		    			}
		    		}

		    		row = sheet.createRow(++rownum);
		    		String value="";
    				value = bodyVO.getQuestionTitle() + " " + isrequired + " " + "【" + questionType + "】";//题目名称  \"b\nc\"
    				value+="\n";
    				if("1".equals(groupingLayoutPc)){
    					if("1".equals(optionLayoutPc)){
    						if(!"".equals(groupName.get(0)) && groupName.get(0)!="" && groupName.size()>0){
    							value += initValue(optionVOList,groupName,1);
    						}else{
    							int num11=0;
    							for(QuestionnaireQuestionOptionVO option11 : optionVOList){
    								if(num11==0){
										value+= "   □  " + option11.getOptionName();
										num11++;
									}else{
										value+="\n"+ "   □  " + option11.getOptionName();
									}
    							}
    						}	
    					}else if("2".equals(optionLayoutPc)){
    						if(!"".equals(groupName.get(0)) && groupName.get(0)!="" && groupName.size()>0){
    							value += initValue(optionVOList,groupName,2);
    						}else{
    							int num11=0;
    							for(QuestionnaireQuestionOptionVO option11 : optionVOList){
    								if(num11==0){
    									value+= "   □  " + option11.getOptionName();
    								}else{
    									if(num11%2!=0){
    										value+= "   □  " + option11.getOptionName();
    									}else{
    										value+="\n"+ "   □  " + option11.getOptionName();
    									}
    								}
    								num11++;
    							}
    						}
    					}else if("4".equals(optionLayoutPc)){    						
    						if(!"".equals(groupName.get(0)) && groupName.get(0)!="" && groupName.size()>0){
    							value += initValue(optionVOList,groupName,4);
    						}else{
    							int num11=0;
    							for(QuestionnaireQuestionOptionVO option11 : optionVOList){
    								if(num11==0){
    									value+= "   □  " + option11.getOptionName();
    								}else{
    									if(num11%4!=0){
    										value+= "   □  " + option11.getOptionName();
    									}else{
    										value+="\n"+ "   □  " + option11.getOptionName();
    									}
    								}
    								num11++;
    							}
    						}
    					}
    				}else if("2".equals(groupingLayoutPc)){ 
						if(groupName.size()<2){
							value += initValue(optionVOList,groupName,Integer.parseInt(optionLayoutPc));
						}else{
							List<List<String>> lists= new ArrayList<List<String>>();
    						for(Object name : groupName){
    							List<String> optionList = new ArrayList<String>();
    							for(QuestionnaireQuestionOptionVO option21 : optionVOList){   								
    								if(name.equals(option21.getGroupingName())){
    									optionList.add(option21.getOptionName());
    								}
    							}
    							lists.add(optionList);
    						}
							if("1".equals(optionLayoutPc)){
								value += initValue2(optionVOList,groupName,lists,1);
							}else if("2".equals(optionLayoutPc)){
	    						value += initValue2(optionVOList,groupName,lists,2);
	    					}else if("4".equals(optionLayoutPc)){
	    						value += initValue2(optionVOList,groupName,lists,4);
	    					}
						}
    				}	
		    		for(int i=0;i<60;i++){
		    			cell = row.createCell(i);
		    			if(i==0){
		    				cell.setCellValue(value);
			    		}	
		    		}
		    		sheet.addMergedRegion(new CellRangeAddress(rownum,rownum,0,59));
		    	}
		    }

```
### 封装代码
> 代码量非常多很多复用的没有复用到，代码可读性差，维护性也差。代码量也相当多  
> 再看看加上面向编程

<img src = "/images/a1.png"></img> 

> - 使用代码
> - 主要是ExecleHelper为封装工具使用建造者模式创建execle文件 再导出

```
.setTitle(tbWjQuestionnairePO.getQuestionnaireTitle())
//为添加固定模板的title 看一下内部代码

```
### BaseEBean父类  

>  以上使用代码封装在一个basebean中做唯一个类封装


```
public class BaseEBean {
    public HSSFWorkbook wb;
    public HSSFSheet sheet;
    public int index;//行数指针

    public void addIndex() {
        index++;
    }

    /**
     * 获取当前行
     *
     * @return
     */
    public HSSFRow getHSSFRow() {
        return sheet.createRow(index);
    }

    /**
     * 获取下一行
     *
     * @return
     */
    public HSSFRow getAddHSSFRow() {
        addIndex();
        return getHSSFRow();
    }

    /**
     * 合并单元格
     *
     * @param b1r 第一表格个行
     * @param b2r 第二表格个行
     * @param b1c 第一个表格列
     * @param b2c 第二个表格列
     */
    public void hbTabl(int b1r, int b2r, int b1c, int b2c) {
        CellRangeAddress range = new CellRangeAddress(b1r, b2r, b1c, b2c);
        sheet.addMergedRegion(range);
    }


    public void setInit(HSSFWorkbook wb, HSSFSheet sheet, int index) {
        this.wb = wb;
        this.sheet = sheet;
        this.index = index;
    }

    public HSSFWorkbook build() {
        return wb;
    }

    /**
     * 添加内容
     *
     * @param row0
     * @param y
     * @param content
     */
    public HSSFCell setContentY(HSSFRow row0, int y, String content) {
        HSSFCell cell0 = row0.createCell(y);
        cell0.setCellValue(content);
        return cell0;
    }

    /**
     * 添加内容
     *
     * @param row0
     * @param y
     * @param content
     */
    public HSSFCell setContentY(HSSFRow row0, int y, String content, HSSFCellStyle style) {
        HSSFCell cell0 = row0.createCell(y);
        cell0.setCellValue(content);
        cell0.setCellStyle(style);
        return cell0;
    }

    /**
     * 添加合并空行
     * @param start
     * @param end
     */
    public void addKong(int start, int end) {
        addIndex();
        hbTabl(index, index, start, end);
    }
}

```
### ExecleHelper 构造类

> 构造类创建HSSFWorkbook 对象


```
public class ExecleHelper extends BaseEBean {
    private ExecleHelper execleTopicHelper;

    public ExecleHelper() {
        wb = new HSSFWorkbook();
        creaTab("表格1");
    }

    public static ExecleHelper creator() {
        return new ExecleHelper();
    }

    public HSSFWorkbook build() {
        return wb;
    }

    public void addIndex() {
        index++;
    }

    /**
     * 创建表
     *
     * @param name
     * @return
     */
    public ExecleHelper creaTab(String name) {
        sheet = wb.createSheet(name);
        return this;
    }

    /**
     * 设置标题
     *
     * @param title
     * @return
     */
    public ExecleHelper setTitle(String title) {
        HSSFRow row0 = getHSSFRow();
        hbTabl(0, 0, 0, 7);
        setContentY(row0, 0, title, ExecleStyleHelper.getTitileStyle(wb));
        return this;
    }

    /**
     * 设置标题
     *
     * @param title
     * @return
     */
    public ExecleHelper setTitle(String title, int b1r, int b2r, int b1c, int b2c) {
        HSSFRow row0 = sheet.createRow(index);
        hbTabl(b1r, b2r, b1c, b2c);
        setContentY(row0, 0, title, ExecleStyleHelper.getTitileStyle(wb));
        return this;
    }


    public <T extends BaseEBean> T addEBean(T baseEInterface) {
        baseEInterface.setInit(wb, sheet, index);
        return (T) baseEInterface;
    }

}
```
### 使用不同模板

> .addEBean(new TopicEBean())
> 在工具类中添加你所需要的bean模板

```
  public <T extends BaseEBean> T addEBean(T baseEInterface) {
        baseEInterface.setInit(wb, sheet, index);
        return (T) baseEInterface;
    }

```
### 模板
> 每一个不同execle样式放入不同的EBean对象
> 将逻辑写在EBean对象中
> 代码简洁已读，维护修改性强

```
public class TopicEBean extends BaseEBean {

    public TopicEBean addData(List<QuestAnalyzeVO> list) {
        for (QuestAnalyzeVO questAnalyzeVO : list) {
            addItem(questAnalyzeVO);
        }
        return this;
    }

    private void addItem(QuestAnalyzeVO questAnalyzeVO) {
        HSSFRow hssfRow = getAddHSSFRow();
        hbTabl(index, index, 0, 3);
        setContentY(hssfRow, 0, questAnalyzeVO.getQuestionTitle(),ExecleStyleHelper.getBar(wb));
        hbTabl(index, index, 4, 5);
        setContentY(hssfRow, 4, getType(questAnalyzeVO.getQuestionType()),ExecleStyleHelper.getBar(wb));
        hbTabl(index, index, 6, 7);
        setContentY(hssfRow, 6, getIsBt(questAnalyzeVO.getIsRequired()),ExecleStyleHelper.getBar(wb));
        HSSFRow hssfRow2 = getAddHSSFRow();
        hbTabl(index, index, 0, 3);
        setContentY(hssfRow2, 0, "标签名：");
        hbTabl(index, index, 4, 7);
        setContentY(hssfRow2, 4, "小计：");
        List<QuestAnalyzeItemVO> list = questAnalyzeVO.getItemVOList();
        for (QuestAnalyzeItemVO questAnalyzeItemVO : list) {
            addItemTopic(questAnalyzeItemVO);
        }
        addKong(0,7);
    }

    private void addItemTopic(QuestAnalyzeItemVO questAnalyzeItemVO) {
        HSSFRow hssfRow = getAddHSSFRow();
        hbTabl(index, index, 0, 3);
        setContentY(hssfRow, 0, questAnalyzeItemVO.getOptionName());
        hbTabl(index, index, 4, 7);
        setContentY(hssfRow, 4, questAnalyzeItemVO.getFiiNum());
    }


    public String getType(String type) {
        if ("1".equals(type)) {
            return "【单选】";
        }
        if ("2".equals(type))
            return "【多选】";
        return "";
    }


    public String getIsBt(String isbt) {
        String str = isbt == null ? "否" : isbt.equals("1") ? "是" : "否";
        return "是否必填：" + str;
    }
}


```

### 填写样式

```
public class ExecleStyleHelper {

    public static HSSFCellStyle getTitileStyle(HSSFWorkbook workbook) {
        // 设置字体
        HSSFFont headfont = workbook.createFont();
        headfont.setFontName("黑体");
        headfont.setFontHeightInPoints((short) 22);// 字体大小
        headfont.setBoldweight(HSSFFont.BOLDWEIGHT_BOLD);// 加粗
        // 另一个样式
        HSSFCellStyle headstyle = workbook.createCellStyle();
        headstyle.setFont(headfont);
        headstyle.setAlignment(HSSFCellStyle.ALIGN_CENTER);// 左右居中
        headstyle.setVerticalAlignment(HSSFCellStyle.VERTICAL_CENTER);// 上下居中
        headstyle.setLocked(true);
        headstyle.setWrapText(true);// 自动换行
        return headstyle;
    }

    /**
     * bar
     * @param workbook
     * @return
     */
    public static HSSFCellStyle getBar(HSSFWorkbook workbook) {
        // Sheet样式
        HSSFCellStyle sheetStyle = workbook.createCellStyle();
        // 背景色的设定
//        sheetStyle.setFillBackgroundColor(HSSFColor.ROYAL_BLUE.index)
//        sheetStyle.setBorderBottom(HSSFCellStyle.BORDER_THIN); //下边框
//        sheetStyle.setBorderLeft(HSSFCellStyle.BORDER_THIN);//左边框
//        sheetStyle.setBorderTop(HSSFCellStyle.BORDER_THIN);//上边框
//        sheetStyle.setBorderRight(HSSFCellStyle.BORDER_THIN);//右边框;
        // 前景色的设定
        sheetStyle.setFillForegroundColor(HSSFColor.PALE_BLUE.index);
        // 填充模式
        sheetStyle.setFillPattern(HSSFCellStyle.SOLID_FOREGROUND);
        return sheetStyle;
    }
}
```

### 总结
> 以上为部分代码设计模式，还有很大的设计空间，需要童鞋们发挥自己的想象里  
> 考虑好自己封装设计代码目的是什么。总结一下主要围绕这几点  
> - 提高码代码速度减少工作量（偷懒）将大量服用封装成对象。万物皆为对象
> - 提高代码效率 减少重复工作将功能作为模块各做各自功能
> - 提高代码可读性在代码上提高别人对代码的可读性即使没有备注
> - 维护性，当二次开发或者修改某些功能时我只需要把模块的东西替换不需要一行行去看，牵一发而动全身   
> - 掌握这几点再复杂的模板都能导出  



### 拓展思维
> 编程思维最重要  
> 比如更提升封装，可以将execle中封装成一个模板  
> 我需要获取长宽多少的模板什么背景  
> 直接将这些封装起来构造出来  
> 通过适配器将数据与模板分离
> 将数据在适配器中填入模板中

### 导出结果

<img src = "/images/a2.png"></img>   
<img src = "/images/a3.png"></img>