---
title: java随笔
tags:
  - jave
  - 工具类
  - 随笔
date: 2016-08-17 18:21:23
updated: 2016-08-17 18:21:23
categories: java
---
### 2016-08-17 18:21:23
``listObj.subList(int fromIndex, int toIndex);``
对子list做的任何改动都会反映在原listObj上。
相反，在原listObj上做的非结构性改动也会反映在子list上。所谓的结构性改动就是指改变list大小。


###### 常用方法：
```
/*
 * 格式化数字
 * @param number
 * @return
 */
public static String formatNumbers(Object number){
    if(isBlankEmpty(number)){
        return "";
    }
    DecimalFormat format = (DecimalFormat) NumberFormat.getPercentInstance();                      
    format.applyPattern("####,###,###,###,##0.00");
    String retStr = number.toString();
    try{
        retStr = format.format(Double.parseDouble(number.toString()));
        if("-0.00".equalsIgnoreCase(retStr)){
            retStr = "0.00";
        }
    }catch (Exception e){}
    return retStr;
}

/**
 * 使用Velocity进行字符串占位符替换
 * @param content 带占位符的字符串（占位符关键字${} 如：哈哈哈${a},jfdkjfd${b}）
 * @param contextParams 值
 * @return
 */
public static String replaceStr(String content, Map<String, Object> contextParams){
    if(isBlankEmpty(content) || contextParams == null || contextParams.isEmpty()){
        return content;
    }
    VelocityContext context = new VelocityContext(contextParams);
    StringWriter writer = new StringWriter();
    Velocity.evaluate(context, writer, "", content);
    return writer.toString();
}
```
<!--more-->
```
/**
 * 生成随机字符串
 * @param base 随机的字符范围，默认是英文小写和数字
 * @param length 字符串长度
 * @return
 */
public static String getRandomString(String base, int length) { //length表示生成字符串的长度                          
    if(isBlankEmpty(base)){
        base = "abcdefghijklmnopqrstuvwxyz0123456789";
    }
    Random random = new Random();
    StringBuffer sb = new StringBuffer();
    for (int i = 0; i < length; i++) {
        int number = random.nextInt(base.length());
        sb.append(base.charAt(number));
    }
    return sb.toString();
}

/**
 * 全角转半角:
 * @param fullStr
 * @return
 */
public static String full2Half(String fullStr) {
    if(isBlankEmpty(fullStr)){
        return fullStr;
    }
    char[] c = fullStr.toCharArray();
    for (int i = 0; i < c.length; i++) {
        if (c[i] >= 65281 && c[i] <= 65374) {
            c[i] = (char) (c[i] - 65248);
        } else if (c[i] == 12288) { // 空格
            c[i] = (char) 32;
        }
    }
    return new String(c);
}

/**
 * 半角转全角
 * @param halfStr
 * @return
 */
public static String half2Full(String halfStr) {
    if(isBlankEmpty(halfStr)){
        return halfStr;
    }
    char[] c = halfStr.toCharArray();
    for (int i = 0; i < c.length; i++) {
        if (c[i] == 32) {
            c[i] = (char) 12288;
        } else if (c[i] < 127) {
            c[i] = (char) (c[i] + 65248);
        }
    }
    return new String(c);
}

/**
 * 是否手机号码
 * @param str
 * @return
 */
public static boolean isMobile(String str)
{
    if(isBlankEmpty(str)){
        return false;
    }
    try{
        if(!isBlankEmpty(str)){
            str = str.replaceAll(" ","");
        }
        Matcher ma;
        Pattern pa = Pattern.compile("^(((13[0-9]{1})|(15[0-9]{1})|(18[0-9]{1})|(17[0-9]{1})|(14[0-9]{1}))+\\d{8})$");                     
        ma = pa.matcher(str);
        boolean tmp = ma.matches();
        if(tmp && str.length() == 11){
            return true;
        }
    }catch (Exception e){}
    return false;
}

/**
 * 是否邮箱
 * @param str
 * @return
 */
public static boolean isEmail(String str)
{
    if(isBlankEmpty(str)){
        return false;
    }
    try{
        Matcher ma;
        Pattern pa = Pattern.compile("^\\w+((-\\w+)|(\\.\\w+))*\\@[A-Za-z0-9]+((\\.|-)[A-Za-z0-9]+)*\\.[A-Za-z0-9]+$");
        ma = pa.matcher(str);
        return ma.matches();
    }catch (Exception e){}
    return false;
}

/**
 * 是否含中文
 * @param str
 * @return
 */
public static boolean isChineseChar(String str){
    boolean temp = false;
    Pattern p=Pattern.compile("[\u4e00-\u9fa5]");
    Matcher m=p.matcher(str);
    if(m.find()){
        temp =  true;
    }
    return temp;
}
```


