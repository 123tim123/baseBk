---
title: java文件下载response header设置  
tags: [java,file]
---

```
@ResponseBody
	@RequestMapping("downLoad")
	public HttpServletResponse download(HttpServletRequest request,HttpServletResponse response,String fileName) throws UnsupportedEncodingException {
//		fileName ="a.png";
		fileName ="b.mp4";
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
				OutputStream toClient = new BufferedOutputStream(response.getOutputStream());
				
				// 设置response的Header
//				response.addHeader("Content-Disposition", "attachment;filename=" + new String(filename.getBytes()));
//				response.setHeader("Access-Control-Allow-Origin","*");
//				response.setContentType("application/octet-stream;charset=UTF-8");
				
//				response.setContentType("image/jpeg");
				
				response.addHeader("Content-Type", "video/mp4");
				response.addHeader("Accept-Ranges", "bytes");
				response.addHeader("Content-Range", "bytes=0-");
				response.addHeader("Content-Length", "" + file.length());
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

#### 文件下载设置一下header
```
 //设置response的Header
response.addHeader("Content-Disposition", "attachment;filename=" + new String(filename.getBytes()));
response.setHeader("Access-Control-Allow-Origin","*");
response.setContentType("application/octet-stream;charset=UTF-8");
				
```

#### 图片浏览器静态预览（非下载图片文件）
> image格式设置成jpeg *或者png格式似乎是不支持浏览器静态预览，其他格式的话就没有尝试  
> 设置的jpeg格式并非图片文件格式，它是告诉浏览器该文件是什么类型所以所有图片设置该格式即可
```
response.setContentType("image/jpeg");
```

#### 视频浏览器静态预览（非文件下载）
> ios 浏览器播放失败，目前未找到原因。最后解决办法静态访问视频资源
```
response.addHeader("Content-Type", "video/mp4");
response.addHeader("Accept-Ranges", "bytes");
response.addHeader("Content-Range", "bytes=0-");
response.addHeader("Content-Length", "" + file.length());
```