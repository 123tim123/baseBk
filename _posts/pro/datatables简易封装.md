---
title: datatables 简易封装  
tags: [java,html]
---
### 什么是datatables？
> Datatables是一款jquery表格插件。它是一个高度灵活的工具，可以将任何HTML表格添加高级的交互功能。   

> 详细介绍请进入官方网站查询 [datatables中文官网](http://www.datatables.club/)  

- 此篇文章主要介绍的是后台的用法已经简易的封装方法
- 让我们先看一下前端js是怎么实现的，下面是一个真实使用接口   
- 后台框架则使用的是struts+spring+jdbc 的企微dqdp框架


### 网络请求实例用法
```
	var table;
			function upSerach() {
			if (table == null) {
				initTab(); //初始化方法
			} else {
				table.ajax.reload();
			}
		}
 
		function initTab() {
		table = $('#example')
				.DataTable(
						{
							language : {
								"sProcessing" : "处理中...",
								"sLengthMenu" : "显示 _MENU_ 项结果",
								"sZeroRecords" : "没有匹配结果",
								"sInfo" : "显示第 _START_ 至 _END_ 项结果，共 _TOTAL_ 项",
								"sInfoEmpty" : "显示第 0 至 0 项结果，共 0 项",
								"sInfoFiltered" : "(由 _MAX_ 项结果过滤)",
								"sInfoPostFix" : "",
								"sSearch" : "搜索:",
								"sUrl" : "",
								"sEmptyTable" : "表中数据为空",
								"sLoadingRecords" : "载入中...",
								"sInfoThousands" : ",",
								"oPaginate" : {
									"sFirst" : "首页",
									"sPrevious" : "上页",
									"sNext" : "下页",
									"sLast" : "末页"
								},
								"oAria" : {
									"sSortAscending" : ": 以升序排列此列",
									"sSortDescending" : ": 以降序排列此列"
								}
							},
							"paging" : true,
							"lengthChange" : false,
							"searching" : false,
							"ordering" : false,
							"processing" : true,
							"serverSide" : true,
							"ajax" : {
								"url" : "${basePath}databank/databankAction!findParamList.action",
								"type" : "post",
								"dataSrc" : function(json) {
									return json.data;
								},
								"data" : function(d) {
									d.type='7';
								}
							},
							"columns" : [{
								"data" : "paramName"
							}, {
								"data" : "updateTime"
							}, {
								"data" : "paramId"
							}],
							"columnDefs" : [{
								// 定义操作列,######以下是重点########
								"targets" : 2,//操作按钮目标列
								"data" : null,
								"render" : function(data, type, row) {
									var id = row.paramId;
									var name = row.paramName;
									var status = '';
									return "<div id=\""+id+"\" status=\""+status+"\" name=\""+name+"\" class=\"ctol_list\"></div>";
								}
							}]
						});
	}

```

### 使用本地数据方法
```
//shuxin liebiao 
		function upSerach() {
			if (table == null) {
				initTab();
			} else {
              table.clear();
              table.rows.add(datawe).draw();
			}
		}


 var datawe = [
			    ];
			
		var table;
		function initTab() {
		table = $('#example')
				.DataTable(
						{
							language : {
								"sProcessing" : "处理中...",
								"sLengthMenu" : "显示 _MENU_ 项结果",
								"sZeroRecords" : "没有匹配结果",
								"sInfo" : "显示第 _START_ 至 _END_ 项结果，共 _TOTAL_ 项",
								"sInfoEmpty" : "显示第 0 至 0 项结果，共 0 项",
								"sInfoFiltered" : "(由 _MAX_ 项结果过滤)",
								"sInfoPostFix" : "",
								"sSearch" : "搜索:",
								"sUrl" : "",
								"sEmptyTable" : "表中数据为空",
								"sLoadingRecords" : "载入中...",
								"sInfoThousands" : ",",
								"oPaginate" : {
									"sFirst" : "首页",
									"sPrevious" : "上页",
									"sNext" : "下页",
									"sLast" : "末页"
								},
								"oAria" : {
									"sSortAscending" : ": 以升序排列此列",
									"sSortDescending" : ": 以降序排列此列"
								}
							},
							"paging" : true,
							"lengthChange" : false,
							"searching" : false,
							"ordering" : false,
							"processing" : true,
							"data" :datawe,
							"columns" : [{
								"data" : "id"
							}, {
								"data" : "name"
							}, {
								"data" : "shelvsStatus"
							}],
							"columnDefs" : [{
								// 定义操作列,######以下是重点########
								"targets" : 2,//操作按钮目标列
								"data" : null,
								"render" : function(data, type, row,meta) {
									var shelvsStatus = row.shelvsStatus;
									return (shelvsStatus=='1')?"待上架":(shelvsStatus=='2')?"已上架":(shelvsStatus=='3')?"下架":"";
								}
							}]
						});
	}

```


## 后台实现源码

- 首先看一下action 实现   


```  
	/** 获取参数列表
	 * 
	 * @throws Exception
	 */
	@JSONOut(catchException = @CatchException(errCode = "1005", successMsg = "操作成功", faileMsg = "操作失败"))
	public void findParamList() throws Exception {
		JqDataTable jqd = new JqDataTable();
		try {
			Map<String, Object> map = jqd.getSearch("type","is_photoType");
			jqd = iParamDataService.findParamList(jqd, map);
		} catch (Exception e) {
			e.printStackTrace();
		}
		JqDataTable.doOutList(jqd);
	}


```

> 主要的功能封装在JqDataTable 这个对象类，接下来我们来看看这个对象内的方法  



```
package aizhuwu.common.bean;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.HashMap;
import java.util.List;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.struts2.ServletActionContext;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

import aizhuwu.common.utils.VerifyUtil;

public class JqDataTable {

	private int draw;
	private int start;
	private int length;
	private int end;
	private int recordsTotal;

	private int recordsFiltered;

	private List<?> data;

	public JqDataTable() {
		HttpServletRequest request = ServletActionContext.getRequest();
		try {
			draw = Integer.parseInt(request.getParameter("draw"));
			start = Integer.parseInt(request.getParameter("start"));
			length = Integer.parseInt(request.getParameter("length"));
			end = start + length;
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	public HashMap<String, Object> getSearch(String... strings) {
		HttpServletRequest request = ServletActionContext.getRequest();
		HashMap<String, Object> map = new HashMap<>();
		for (String key : strings) {
			String param = request.getParameter(key);
			if (!VerifyUtil.isEmpty(param)) {
				map.put(key, param);
			}
		}
		return map;
	}

	public JqDataTable(int draw, int recordsTotal, int recordsFiltered, List<?> data) {
		this.draw = draw;
		this.recordsTotal = recordsTotal;
		this.recordsFiltered = recordsFiltered;
		this.data = data;
	}

	public void set(int recordsTotal, int recordsFiltered, List<?> data) {
		this.recordsTotal = recordsTotal;
		this.recordsFiltered = recordsFiltered;
		this.data = data;
	}

	public int getDraw() {
		return draw;
	}

	public void setDraw(int draw) {
		this.draw = draw;
	}

	public int getRecordsTotal() {
		return recordsTotal;
	}

	public void setRecordsTotal(int recordsTotal) {
		this.recordsTotal = recordsTotal;
	}

	public int getRecordsFiltered() {
		return recordsFiltered;
	}

	public void setRecordsFiltered(int recordsFiltered) {
		this.recordsFiltered = recordsFiltered;
	}

	public List<?> getData() {
		return data;
	}

	public void setData(List<?> data) {
		this.data = data;
	}

	public int getStart() {
		return start + 1;
	}

	public int getLength() {
		return length;
	}

	public void setStart(int start) {
		this.start = start;
	}

	public void setLength(int length) {
		this.length = length;
	}

	public int getEnd() {
		return end;
	}

	public void setEnd(int end) {
		this.end = end;
	}

	public static void doOutList(String json) throws IOException {
		HttpServletResponse response = ServletActionContext.getResponse();
		/*
		 * HttpServletResponse则会返回一个用默认的编码(既ISO-8859-1)编码的PrintWriter实例。这样就会
		 * 造成中文乱码。而且设置编码时必须在调用getWriter之前设置,不然是无效的。
		 */
		response.setContentType("text/html;charset=utf-8");
		PrintWriter out = response.getWriter();
		out.println(json);
		out.flush();
		out.close();
	}

	public static void doOutList(Object obj) throws IOException {
		Gson g = new GsonBuilder().serializeNulls().setDateFormat("yyyy-MM-dd HH:mm").create();
		doOutList(g.toJson(obj));
	}

	public static void doOutObject(Object obj) throws IOException {
		HashMap<String, Object> map = new HashMap<>();
		map.put("code", "0");
		map.put("desc", "操作成功");
		map.put("data", obj);
		Gson g = new GsonBuilder().serializeNulls().setDateFormat("yyyy-MM-dd HH:mm").create();
		doOutList(g.toJson(map));
	}

	public static void doOutObject(Object obj, String format) throws IOException {
		HashMap<String, Object> map = new HashMap<>();
		map.put("code", "0");
		map.put("desc", "操作成功");
		map.put("data", obj);
		Gson g = new GsonBuilder().serializeNulls().setDateFormat(format).create();
		doOutList(g.toJson(map));
	}

	/**
	 * 
	 * @param obj
	 * @param str
	 * @throws IOException
	 */
	public static void doOutObjectTime(Object obj, String str) throws IOException {
		HashMap<String, Object> map = new HashMap<>();
		map.put("code", "0");
		map.put("desc", "操作成功");
		map.put("data", obj);
		Gson g = new GsonBuilder().serializeNulls().setDateFormat(str).create();
		doOutList(g.toJson(map));
	}

}


```

- 1.draw 2.start 3.length 这三个参数则是datatables控件封装传输上来的字段，所以在前端是看不大这些字段的传输，可以通过谷歌浏览器开发模式查看有哪些字段上传，我们这套封装主要是试用这三个字段


- service 实现则没有做特殊逻辑处理

```
@Override
	public JqDataTable findParamList(JqDataTable jqd, Map<String, Object> map) throws Exception {
		// TODO Auto-generated method stub
		return iParamDataDAO.findParamList(jqd, map);
	}
```


- 犹豫框架是jdbc所有都是拼接sql来实现查询

```

	@Override
	public JqDataTable findParamList(JqDataTable jqd, Map<String, Object> map) throws SQLException {
		String type=(String) map.get("type");
		String is_photoType = (String) map.get("is_photoType");
		String sqlCity =VerifyUtil.isEmpty(type)?"":" and a.TYPE = '"+type+"'";
		String sqlIsPhotoType =VerifyUtil.isEmpty(is_photoType)?"":" and a.IS_PHOTO_TYPE = '"+is_photoType+"'";
		String sql ="select * FROM TB_PARAM_PARAM_DATA a where 1=1 "+sqlCity+sqlIsPhotoType+" ORDER BY a.UPDATE_TIME DESC";
		jqd = AISQLUtils.queryJqData(this, sql, jqd, ParamDataBean.class);
		return jqd;
	}
```

> 最重要的是jqd = AISQLUtils.queryJqData(this, sql, jqd, ParamDataBean.class);  
> this则为查询方法父类 sql为条件语句 jqd 为翻页对象 ParamDataBean po 对象   
> 查询语句FROM 必须大写，封装AISQLUtils 根据FROM第一字段来做分页语句拼接处理

## AISQLUtils对应封装Oracle 分页查询
```
package aizhuwu.webservicesys.utils;

import java.sql.SQLException;
import java.util.HashMap;
import java.util.List;

import org.apache.poi.ss.formula.functions.T;

import aizhuwu.common.bean.JqDataTable;
import cn.com.do1.common.framebase.dqdp.BaseDAOImpl;
import cn.com.do1.component.backmanager.orgmgr.model.TbDqdpOrganizationPO;

public class AISQLUtils {

	public static List<?> getListPage(BaseDAOImpl base, String sql, JqDataTable jq, Class<?> cls) throws SQLException {
		String baseSql = "SELECT * FROM  (  SELECT A.*,rownum RN   FROM " + "(" + sql + ") A   ) "
				+ " WHERE RN BETWEEN '" + jq.getStart() + "' AND '" + jq.getEnd() + "'  ";
		base.preparedSql(baseSql);
		return base.getList(cls);
	}

	public static int querySize(BaseDAOImpl base, String sql, JqDataTable jq) throws SQLException {
		String f = "FROM";
		String basesql = "SELECT count(*) FROM ";
		int index =sql.indexOf(f);
		String akk = basesql + sql.substring(index+f.length(), sql.length());
		return base.executeCount(akk);
	}

	/**
	 * 查询分页
	 * 
	 * @param base
	 * @param sql
	 * @param jq
	 * @param cls
	 * @return
	 * @throws SQLException
	 */
	public static JqDataTable queryJqData(BaseDAOImpl base, String sql, JqDataTable jq, Class<?> cls)
			throws SQLException {
		List<?> list = getListPage(base, sql, jq, cls);
		int size = querySize(base, sql, jq);
		jq.set(size, size, list);
		return jq;
	}
}


```

### 总结：框架方法很多种思想是精髓 好的封装方法可大大提高开发效率。