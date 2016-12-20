---
title: java判断txt文件的编码 # 文章页面上的显示名称，可以任意修改，不会出现在URL中
date: 2016-04-13 16:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Java
tags:   # 文章标签，参数可省略
    - 判断编码
---
在java程序中读入txt文件内的字符串，不管txt文件采用何种编码。
<!--more-->
在项目中遇到要读取txt文件中的字符串，但是如果不判断文件的编码类型就可能出现中文乱码的问题，因此查找资料后写了以下这个方法，用于输入文件的路径，然后读取文件的内容存到字符串数据中。
### 贴上代码：
```java
private static String getStringFromFile(String path){
        int len=0;
        StringBuffer str=new StringBuffer("");
        File file=new File(path);
        try {
            BufferedInputStream bin = new BufferedInputStream(new FileInputStream(path));
            byte[] b = new byte[10];
            bin.read(b, 0, b.length);
            String first = toHex(b);
            //这里可以看到各种编码的前几个字符是什么，gbk编码前面没有多余的
            String code = null;
            if (first.startsWith("EFBBBF")) {
                code = "UTF-8";
            } else if (first.startsWith("FEFF00")) {
                code = "UTF-16BE";
            } else if (first.startsWith("FFFE")) {
                code = "Unicode";
            } else if (first.startsWith("FFFE")) {
                code = "Unicode";
            } else {
                code = "GBK";
            }
            FileInputStream is=new FileInputStream(file);
            InputStreamReader isr= new InputStreamReader(is, code);
            BufferedReader in= new BufferedReader(isr);
            String line=null;
            while( (line=in.readLine())!=null )
            {
                if(len != 0)  // 处理换行符的问题
                {
                    str.append("\n"+line);
                }
                else
                {
                    str.append(line);
                }
                len++;
            }
            in.close();
            is.close();
            bin.close();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return str.toString();
    }
```