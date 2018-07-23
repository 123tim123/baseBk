---
title: java poi模板内容替换
tags: [java]
---
### execl 内容替换
> 获取execl 模板 设置模板特殊字符进行字符替换内容



```
package com.bgy.tcd.ssm.util;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.util.HashMap;
import java.util.Map;
import java.util.Map.Entry;

import org.apache.poi.hssf.usermodel.HSSFCell;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.poifs.filesystem.POIFSFileSystem;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;

import com.bgy.tcd.ssm.util.poiutils.ExecleHelper;
/**
 * 替换execl数据
 * @author Administrator
 *
 */
public class PoiTest {
	public POIFSFileSystem fs;
	HSSFWorkbook wb;
	Sheet sheet;
	Row row;

	public static void main(String[] args) throws FileNotFoundException, IOException {
		HashMap<String, Object> map = new HashMap<String, Object>();
		map.put("a_1", "aaa");
		map.put("b_2", "cccc");
		map.put("@title", "X区域XXX项目过程管控风险预警单");
		map.put("@cb_1", "a1");
		map.put("@ab_2", "a2");
		map.put("@ab_3", "a3");
		HSSFWorkbook hss = new PoiTest().replaceExcel("s1", "F:/UpFile/abc.xls", map);
		//下载execl
//		ExecleHelper.outFile(response,wb, "用户分析统计");
//		
	}

	/**
	 * 替换Excel模板中的数据
	 * 
	 * @param sheetName
	 *            Sheet名字
	 * @param modelPath
	 *            模板路径
	 * @param param
	 *            需要替换的数据
	 * @return
	 */
	public HSSFWorkbook replaceExcel(String sheetName, String modelPath, Map<String, Object> param) throws FileNotFoundException, IOException {
		// 获取所读取excel模板的对象
		try {
			File file = new File(modelPath);
			if (!file.exists()) {
				System.out.println("模板文件:" + modelPath + "不存在!");
			}
			fs = new POIFSFileSystem(new FileInputStream(file));
			wb = new HSSFWorkbook(fs);
			sheet = wb.getSheet(sheetName);
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
		replaceExcelDate(param);
		savePic("abbb.xls");
		return wb;
	}

	/**
	 * 根据 Map中的数据替换Excel模板中指定数据
	 * 
	 * @param param
	 */
	public void replaceExcelDate(Map<String, Object> param) {
		// 获取行数
		int rowNum = sheet.getLastRowNum();
		System.out.println("rowNum:"+rowNum);
		for (int i = 0; i <= rowNum; i++) {
			row = sheet.getRow(i);
			// 获取行里面的总列数
			int columnNum = 0;
			if (row != null) {
				columnNum = row.getLastCellNum();
			}
			System.out.println("columnNum:"+columnNum);
			for (int j = 0; j < columnNum; j++) {
				Cell cell =  sheet.getRow(i).getCell(j);
				if (cell != null) {
					String cellValue = cell.getStringCellValue();
					System.out.println("cellValue:"+cellValue);
					for (Entry<String, Object> entry : param.entrySet()) {
						String key = entry.getKey();
						if (key.equals(cellValue)) {
							String value = entry.getValue().toString();
							setCellStrValue(i, j, value);
						}
					}
				}else{
					System.err.println("ii:"+i+"----jjj:"+j);
				}
			}
		}
	}

	/**
	 * 设置字符串类型的数据
	 * 
	 * @param rowIndex--行值
	 *            从0开始
	 * @param cellnum--列值
	 *            从0开始
	 * @param value--字符串类型的数据
	 * 
	 */
	public void setCellStrValue(int rowIndex, int cellnum, String value) {
		Row row = sheet.getRow(rowIndex);
		Cell cell = row.getCell(cellnum);
		if(cell==null) cell = sheet.getRow(rowIndex).createCell(cellnum);
		cell.setCellValue(value);
	}
	
	private void savePic(String fileName) {

        OutputStream os = null;
        try {
            String path = "F:\\UpFile\\";
            // 2、保存到临时文件
            // 1K的数据缓冲
            byte[] bs = new byte[1024];
            // 读取到的数据长度
            int len;
            // 输出的文件流保存到本地文件

            File tempFile = new File(path);
            if (!tempFile.exists()) {
                tempFile.mkdirs();
            }
            os = new FileOutputStream(tempFile.getPath() + File.separator + fileName);
            wb.write(os);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 完毕，关闭所有链接
            try {
                os.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}


```