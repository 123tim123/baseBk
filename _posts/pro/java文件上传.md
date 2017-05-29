---
title: ajaxFileUpload文件上传   
tags: [java]
---

> 文件上传流程：  
> 前端->项目后台->文件项目->文件项目写入服务硬盘  


> 上传项目采用的是struts+spring+jdbc 的企微dqdp框架  
> 文件服务器接收项目则采用的是springmvc+sping+mybatis
> 前段主要使用的是ajaxFileUpload 插件

## 前端文件上传 

```

//判断文件类型
	function isImage(filePathName){
		if(filePathName==''){
			return false;
		}
		if(filePathName.indexOf(".png")>0){
			return true;
		}
		if(filePathName.indexOf(".jpg")>0){
			return true;
		}
		if(filePathName.indexOf(".PNG")>0){
			return true;
		}
		if(filePathName.indexOf(".JPG")>0){
			return true;
		}
		return false;
	}

<input type="file" name="upload" id="uploadInput" onchange="uploadLogo(this)" value="" class="fileIpt" />


//文件上传js targert input file
function uploadLogo(target) {
		var filePathName = $("#uploadInput").val();
		console.log(filePathName);
			if(!isImage(filePathName)) {
				alert("请选择要上传的图片文件");
				return;
			}
			//控制图片上传大小不超过5M
			var isIE = /msie/i.test(navigator.userAgent) && !window.opera; 
			var fileSize = 0;         
	       	if (isIE && !target.files) {     
	        	var filePath = target.value;     
	        	var fileSystem = new ActiveXObject("Scripting.FileSystemObject");        
	        	var file = fileSystem.GetFile (filePath);     
	        	fileSize = file.Size;    
	       	} else {    
	        	fileSize = target.files[0].size;     
	        }   
	        var size = fileSize / 1024;    
	       	if(size>5000){  
	        	alert("不能大于5M");
	         	target.value="";
	         	return;
	        }
			$.ajaxFileUpload({
				url : "${basePath}webservicesys/orgAction!upFile.action",  
				secureuri : false,
				fileElementId : "uploadInput",
				dataType : 'json',
				success : function(result) {
					console.log(result);
					if(result.code == 0) {
						imgeUrl = result.data.url;
						upDataImge(result.data.url);
					}else{
						alert(result.desc);
					}
				},
				error : function(data, status, e) {
					console.log(e);
					alert("上传文件失败");
				}
			});
		}


```

# 后台代码
### 本项目接口跳转存入文件服务器

```
/**
	 * 文件上传
	 * 
	 * @throws Exception
	 * @throws BaseException
	 */
	@JSONOut(catchException = @CatchException(errCode = "1005", successMsg = "操作成功", faileMsg = "操作失败"))
	public void upFile() throws Exception, BaseException {
		try {
			if (AssertUtil.isEmpty(upload)) {
				setActionResult("1001", "您导入的文件为空！");
			} else {
				System.out.println(upload.getName()+"-------"+upload.length());
				System.out.println(uploadFileName+"-------"+uploadContetnType);
				String mrePosen = HttPostContent.SubmitPost(upload,uploadFileName);
				if (VerifyUtil.isEmpty(mrePosen)) {
					setActionResult("1001", "文件上传远程失败");
				} else {
					FileUpBean fileUpBean = JsonUtils.parseJson2Obj(mrePosen, FileUpBean.class);
					fileUpBean.initUrl();
					System.out.println("fileLoadUrl:"+new Gson().toJson(fileUpBean));
					JqDataTable.doOutObject(fileUpBean);
				}
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

```

### 上传文件服务器
```
 // file1与file2在同一个文件夹下 filepath是该文件夹指定的路径
 	public static String SubmitPost(File file,String fileName) {
 		HttpClient httpclient = new DefaultHttpClient();
 		try {
 			HttpPost httppost = new HttpPost(ConfigPath.getInstance().getFilePath());
 			FileBody bin = new FileBody(file);
 			StringBody comment = new StringBody(fileName,Charset.forName("UTF-8"));
 			MultipartEntity reqEntity = new MultipartEntity(HttpMultipartMode.BROWSER_COMPATIBLE, null,  
                    Charset.forName("UTF-8"));
 			reqEntity.addPart("file", bin);// file1为请求后台的File upload;属性
 			reqEntity.addPart("fileName",comment);// filename1为请求后台的普通参数;属性
 			httppost.setEntity(reqEntity);
 			HttpResponse response = httpclient.execute(httppost);
 			int statusCode = response.getStatusLine().getStatusCode();
 			if (statusCode == HttpStatus.SC_OK) {
 				System.out.println("服务器正常响应.....");
 				HttpEntity resEntity = response.getEntity();
 				String data = EntityUtils.toString(resEntity, "UTF-8");
 				System.out.println("原生返回数据："+data);
 				JSONObject js = JSONObject.fromObject(data);
 				System.out.println("json:"+js);
 				System.out.println("resEntity:"+data);// httpclient自带的工具类读取返回数据
 				System.out.println("content:"+resEntity.getContent());
 				EntityUtils.consume(resEntity);
 				return data;
 			}
 		} catch (ParseException e) {
 			// TODO Auto-generated catch block
 			e.printStackTrace();
 		} catch (IOException e) {
 			// TODO Auto-generated catch block
 			e.printStackTrace();
 		} finally {
 			try {
 				httpclient.getConnectionManager().shutdown();
 			} catch (Exception ignore) {

 			}
 		}
 		return "";
 	}
```


# 文件服务器项目源码
### 接收文件接口
```
@ResponseBody
	@RequestMapping("upFile")
	public JSONObject upVersionFile(@RequestParam("file") CommonsMultipartFile[] files, HttpServletRequest request,
			HttpServletResponse response) throws IOException {
		String mfileName = request.getParameter("fileName");
		for (int i = 0; i < files.length; i++) {
			log.error("fileName---------->" + files[i].getOriginalFilename()+"---"+mfileName);
		}
		String savePath = ConfigPath.getInstance().getFilePath();
		File file = new File(savePath);
		if(!file.exists()){
			file.mkdirs();
		}
		for (int i = 0; i < files.length; i++) {
			System.out.println("fileName---------->" + files[i].getOriginalFilename());
			if (!files[i].isEmpty()) {
				int pre = (int) System.currentTimeMillis();
				try {
					String fileId = Constants.clearFileName(mfileName);
					String fileName =pre+fileId;
					FileUtils.copyInputStreamToFile(files[i].getInputStream(),
							new File(savePath,fileName));
					HashMap<String, Object> map = new HashMap<>();
					map.put("fileName", mfileName);
					map.put("fileSize", files[i].getSize());
					map.put("fileId", fileName);
					map.put("fileType", 1);
					String reData = new Gson().toJson(map);
					log.error(reData);
					return JSONObject.parseObject(reData);
				} catch (Exception e) {
					e.printStackTrace();
					log.error("上传出错");
				}
			}
		}
		return null;
	}



	/**
	 * 去除特殊符号
	 * @param fileName
	 * @return
	 */
	public static String clearFileName(String fileName){
		String newFileName = fileName.replace("=", "")
				.replace("@", "")
				.replace("#","")
				.replace("$","")
				.replace("%","")
				.replace("……","")
				.replace("^","")
				.replace("&","")
				.replace("，","")
				.replace(",","")
				.replace("。","")
				.replace("-","")
				.replace("_","")
				.replace("?","")
				.replace("（","")
				.replace("）","")
				.replace("(","")
				.replace(")","")
				.replace("*","");
		return newFileName;
	}
```


### 文件下载接口
```

	@ResponseBody
	@RequestMapping("downLoad")
	public HttpServletResponse download(HttpServletResponse response,String fileName) throws UnsupportedEncodingException {
		System.out.println("fileName:"+fileName);
		String bname = new String(fileName.getBytes("iso8859-1"),"utf-8");
		System.out.println("bname："+bname);
		try {
			InputStream inputStream = this.getClass().getClassLoader().getResourceAsStream("config.properties");
			Properties properties = new Properties();
			try {
				properties.load(inputStream);
			} catch (IOException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			}
			String savePath = properties.getProperty("FilePath");
			
			String path = savePath+"/"+bname;
			try {
				// path是指欲下载的文件的路径。
				File file = new File(path);
				// 取得文件名。
				String filename = file.getName();
				// 取得文件的后缀名。
				String ext = filename.substring(filename.lastIndexOf(".") + 1).toUpperCase();
				// 以流的形式下载文件。
				InputStream fis = new BufferedInputStream(new FileInputStream(path));
				byte[] buffer = new byte[fis.available()];
				fis.read(buffer);
				fis.close();
				// 清空response
				response.reset();
				// 设置response的Header
				response.addHeader("Content-Disposition", "attachment;filename=" + new String(filename.getBytes()));
				response.addHeader("Content-Length", "" + file.length());
				OutputStream toClient = new BufferedOutputStream(response.getOutputStream());
				response.setContentType("application/octet-stream");
				toClient.write(buffer);
				toClient.flush();
				toClient.close();
			} catch (IOException ex) {
				ex.printStackTrace();
			}
			return response;
		} catch (Exception e) {
			log.error("下载失败：-" + e.getMessage(), e);
			return response;
		}
	}

```