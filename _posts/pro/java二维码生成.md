---
title: java 二维码生成工具
tags: [java]
---
# java二维码生成

需要使用到的jar包：QRCode.jar 下边是一个链接如果链接失效请百度下载。  
这里提供一个下载：[点此下载QRCode.jar ](http://pan.baidu.com/s/1pKOutez " 点此下载QRCode.jar ") 


```
package cn.com.do1.component.util;

import com.swetake.util.Qrcode;
import jp.sourceforge.qrcode.QRCodeDecoder;
import jp.sourceforge.qrcode.exception.DecodingFailedException;

import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.util.HashMap;

/**
 * Created by Administrator on 2017/7/10 0010.
 */
public class TwoDimensionCode {
    /**
     * 生成二维码(QRCode)图片
     * @param content 存储内容
     * @param imgPath 图片路径
     */
    public void encoderQRCode(String content, String imgPath)  {
        this.encoderQRCode(content, imgPath, "png", 7);
    }

    /**
     * 生成二维码(QRCode)图片
     * @param content 存储内容
     * @param output 输出流
     */
    public void encoderQRCode(String content, OutputStream output) {
        this.encoderQRCode(content, output, "png", 7);
    }

    /**
     * 生成二维码(QRCode)图片
     * @param content 存储内容
     * @param imgPath 图片路径
     * @param imgType 图片类型
     */
    public void encoderQRCode(String content, String imgPath,  String imgType) {
        this.encoderQRCode(content, imgPath, imgType, 7);
    }

    /**
     * 生成二维码(QRCode)图片
     * @param content 存储内容
     * @param output 输出流
     * @param imgType 图片类型
     */
    public void encoderQRCode(String content, OutputStream output, String imgType) {
        this.encoderQRCode(content, output, imgType, 7);
    }

    /**
     * 生成二维码(QRCode)图片
     * @param content 存储内容
     * @param imgPath 图片路径
     * @param imgType 图片类型
     * @param size 二维码尺寸
     */
    public void encoderQRCode(String content, String imgPath,  String imgType, int size) {
        try {
            BufferedImage bufImg =this.qRCodeCommon(content, imgType, size);

            File imgFile = new File(imgPath);
            // 生成二维码QRCode图片
            ImageIO. write(bufImg, imgType , imgFile);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 生成二维码(QRCode)图片
     * @param content 存储内容
     * @param output 输出流
     * @param imgType 图片类型
     * @param size 二维码尺寸
     */
    public void encoderQRCode(String content, OutputStream output, String imgType, int size) {
        try {
            BufferedImage bufImg =this.qRCodeCommon(content, imgType, size);
            // 生成二维码QRCode图片
            ImageIO.write(bufImg, imgType, output);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 生成二维码(QRCode)图片的公共方法
     * @param content 存储内容
     * @param imgType 图片类型
     * @param size 二维码尺寸
     * @return
     */
    private BufferedImage qRCodeCommon(String content, String  imgType, int size) {
        BufferedImage bufImg = null;
        try {
            Qrcode qrcodeHandler = new Qrcode();
            // 设置二维码排错率，可选L(7%)、M(15%)、Q(25%)、H(30%)，排错率越高可存储的信息越少，但对二维码清晰度的要求越小
            qrcodeHandler.setQrcodeErrorCorrect( 'M');
            qrcodeHandler.setQrcodeEncodeMode( 'B');
            // 设置设置二维码尺寸，取值范围1-40，值越大尺寸越大，可存储的信息越大
            qrcodeHandler.setQrcodeVersion(size);
            // 获得内容的字节数组，设置编码格式
            byte[] contentBytes = content.getBytes( "utf-8");
            // 图片尺寸
            int imgSize = 67 + 12 * (size - 1);
            bufImg = new BufferedImage(imgSize, imgSize, BufferedImage.TYPE_INT_RGB );
            Graphics2D gs = bufImg.createGraphics();
            // 设置背景颜色
            gs.setBackground(Color. WHITE);
            gs.clearRect(0, 0, imgSize, imgSize);

            // 设定图像颜色> BLACK
            gs.setColor(Color. BLACK);
            // 设置偏移量，不设置可能导致解析出错
            int pixoff = 2;
            // 输出内容> 二维码
            if (contentBytes. length > 0 && contentBytes.length < 800) {
                boolean[][] codeOut = qrcodeHandler.calQrcode(contentBytes);
                for ( int i = 0; i < codeOut. length; i++) {
                    for ( int j = 0; j < codeOut. length; j++) {
                        if (codeOut[j][i]) {
                            gs.fillRect(j * 3 + pixoff, i * 3 + pixoff, 3, 3);
                        }
                    }
                }
            } else {
                throw new Exception( "QRCode content bytes  length = " + contentBytes. length + " not in [0, 800].");
            }
            gs.dispose();
            bufImg.flush();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return bufImg;
    }

    /**
     * 解析二维码（QRCode）
     * @param imgPath 图片路径
     * @return
     */
    public String decoderQRCode(String imgPath) {
        // QRCode 二维码图片的文件
        File imageFile = new File(imgPath);
        BufferedImage bufImg = null;
        String content = null;
        try {
            bufImg = ImageIO.read(imageFile);
            QRCodeDecoder decoder = new QRCodeDecoder();
            content = new String(decoder.decode(new TwoDimensionCodeImage(bufImg)), "utf-8" );
        } catch (IOException e) {
            System. out.println( "Error: " + e.getMessage());
            e.printStackTrace();
        } catch (DecodingFailedException dfe) {
            System. out.println( "Error: " + dfe.getMessage());
            dfe.printStackTrace();
        }
        return content;
    }

    /**
     * 解析二维码（QRCode）
     * @param input 输入流
     * @return
     */
    public String decoderQRCode(InputStream input) {
        BufferedImage bufImg = null;
        String content = null;
        try {
            bufImg = ImageIO. read(input);
            QRCodeDecoder decoder = new QRCodeDecoder();
            content = new String(decoder.decode( new TwoDimensionCodeImage(bufImg)), "utf-8" );
        } catch (IOException e) {
            System. out.println( "Error: " + e.getMessage());
            e.printStackTrace();
        } catch (DecodingFailedException dfe) {
            System. out.println( "Error: " + dfe.getMessage());
            dfe.printStackTrace();
        }
        return content;
    }

    public static void main(String[] args) {
        String imgPath = "F:/UpFile/iamge.png";
        HashMap<String,Object> map =new HashMap<>();
        map.put("id","1");
        map.put("name","名称");
        map.put("phone","110");
        String encoderContent = VerifyUtil.toJson(map);
        TwoDimensionCode handler = new TwoDimensionCode();
        handler.encoderQRCode(encoderContent, imgPath, "png" );
        System. out.println( "========encoder success" );
        String decoderContent = handler.decoderQRCode(imgPath);
        System. out.println( "解析结果如下：" );
        System. out.println(decoderContent);
        System. out.println( "========decoder success!!!" );
    }
}


```

```
package cn.com.do1.component.util;

import jp.sourceforge.qrcode.data.QRCodeImage;

import java.awt.image.BufferedImage;

/**
 * Created by Administrator on 2017/7/10 0010.
 */
public class TwoDimensionCodeImage implements QRCodeImage {

    BufferedImage bufImg;

    public TwoDimensionCodeImage(BufferedImage bufImg) {
        this.bufImg = bufImg;
    }

    public int getHeight() {
        return bufImg.getHeight();
    }

    public int getPixel(int x, int y) {
        return bufImg.getRGB(x, y);
    }

    public int getWidth() {
        return bufImg.getWidth();
    }
}

```

### 配合文件下载   
```
package cn.com.do1.component.wjxt.questionManage.ui;

import cn.com.do1.component.baseweb.ui.MyBaseAction;
import cn.com.do1.component.util.ConfigPath;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletResponse;
import java.io.*;

/**
 * Created by Administrator on 2017/7/10 0010.
 */
public class FilesAction extends MyBaseAction {


    @ResponseBody
    @RequestMapping("downLoad")
    public HttpServletResponse download(HttpServletResponse response, String fileName) throws UnsupportedEncodingException {
        System.out.println("fileName:"+fileName);
        String bname = new String(fileName.getBytes("iso8859-1"),"utf-8");
        System.out.println("bname："+bname);
        try {
            String savePath = ConfigPath.getInstance().getString("savaPath");
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
                response.setHeader("Access-Control-Allow-Origin","*");
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
}

```