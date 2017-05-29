---
title: android均匀显示textview 
tags: [android,view]
---
# 均匀分布textview
> 许多情况下，我们需要把文字均匀分布而且要保持几个空间的宽度一致。并且Android里又没有这种控或者属性风格之类
        可以用空格来实现，但是空格会对不齐，字体大小改变后就不准确，基本无法实现功能。
        在这里我给大家分享一个我自己写的控件，可以原理很简单是用画笔和画板将文字画出来，先看效果图如下，
文章后面有控件下载地址需要的童鞋可以下载使用

![这里写图片描述](http://img.blog.csdn.net/20160808184517629)

使用非常简单，与textview使用方法一致

![这里写图片描述](http://img.blog.csdn.net/20160808184629511)

只需settext()即可

他的原理就是先获取文字数量，用控件的宽度来除以文字数量为宽度差，然后对应画出文字，降文字转化为数组，对应数组指针乘以宽度减去文字本身的宽度的一半，第一个文字和最后一个文字进行处理。

```
public class LineTextView extends TextView {
 private Context context;
 private int viewWidth;
 private int viewHight;
 private int oneWidth;
 private int oneHight;
 private int num;
 private Paint p;
 private String str;
 private String[] texts;
 private int textSize = 15;
 public LineTextView(Context context) {
  super(context);
  this.context = context;
  init();
 }
 public LineTextView(Context context, AttributeSet attrs) {
  super(context, attrs);
  this.context = context;
  init();
 }
 @Override
 protected void onDraw(Canvas canvas) {
  super.onDraw(canvas);
  init();
  drawText(canvas);
 }
 private void drawText(Canvas canvas) {
  if (num == 0)
   return;
  int lineWidth = viewWidth / num;
  int drawY = (viewHight - oneHight) / 2 + oneHight;
  for (int i = 0; i < num; i++) {
   if (i == num - 1) {// 最后一个
    canvas.drawText(texts[i],
      viewWidth - oneWidth - dip2px(context, 2), drawY, p);
   } else if (i != 0 && i != num - 1) {//
    canvas.drawText(texts[i], i * lineWidth + lineWidth / 2
      - oneWidth / 2, drawY, p);
   } else {// 第一个
    canvas.drawText(texts[i], i * lineWidth + dip2px(context, 2),
      drawY, p);
   }
  }
 }
 private void init() {
  this.setPadding(0, 0, 0, 0);
  viewWidth = this.getWidth();
  viewHight = this.getHeight();
 }
 public void setText(String str) {
  if (str == null)
   return;
  p = new Paint();
  p.setColor(context.getResources().getColor(R.color.m_black));
  p.setTextSize(dip2px(context, textSize));
  this.str = str;
  num = str.length();
  int width = ce("汉").width();
  this.oneWidth = width;
  this.oneHight = ce(str).height();
  String[] strs = new String[num];
  for (int i = 0; i < num; i++) {
   strs[i] = str.substring(i, i + 1);
  }
  this.texts = strs;
  this.invalidate();
 }
 public void setTestSize(int size) {
  this.textSize = size;
  if (str != null)
   setText(str);
 }
 public Rect ce(String str) {
  Rect rect = new Rect();
  p.getTextBounds(str, 0, 1, rect);
  return rect;
 }
 /**
  * 根据手机的分辨率�? dp 的单�? 转成�? px(像素)
  */
 public static int dip2px(Context context, float dpValue) {
  final float scale = context.getResources().getDisplayMetrics().density;
  return (int) (dpValue * scale + 0.5f);
 }
```
下载地址

http://pan.baidu.com/s/1eRZh8ki