---
title: java中调用接口返回Json后进行解析 # 文章页面上的显示名称，可以任意修改，不会出现在URL中
date: 2016-05-07 16:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Java
tags:   # 文章标签，参数可省略
---
构造Json的参考博文：
http://www.cnblogs.com/lanxuezaipiao/archive/2013/05/23/3096001.html
http://blog.csdn.net/xiazdong/article/details/7059573
http://blog.csdn.net/lianqiangjava/article/details/20800723
<!--more--> 

### java中调用接口返回Json后进行解析
```java
public void DetailsFromId(String _id){
    id = _id;
    String url = "http://10.1.1.66:5656/linking?type=uri&uri=" + id;
    System.out.println(url);
    url = {  "title": {    "zh": "\u8bef\u89e3"  },   "url": "http://xlore.org/sigInfo.action?uri=/http://xlore.org/instance/4033831",   "abstract": {    "en": "",     "zh": "\u4e2d\u3002"  },   "uri": "http://xlore.org/instance/4033831",   "type": [    {      "en": "Albums by Hong Kong artists",       "zh": "\u9999\u6e2f\u97f3\u4e50\u4e13\u8f91"    },     {      "en": "2012 singles",       "zh": "2012\u5e74\u5355\u66f2"    },     {      "zh": "\u8bb8\u5ef7\u94ff\u97f3\u4e50\u4e13\u8f91"    }  ],   "related_item": [    "http://xlore.org/instance/57758",     "http://xlore.org/instance/173848"]}


    StringBuilder json = new StringBuilder();
    try {
        URL oracle = new URL(url);
        URLConnection yc = oracle.openConnection();
        BufferedReader in = new BufferedReader(new InputStreamReader(
                                    yc.getInputStream()));
        String inputLine = null;
        while ( (inputLine = in.readLine()) != null) {
            json.append(inputLine);
        }
        in.close();
    } catch (MalformedURLException e) {
    } catch (IOException e) {
    }
    System.out.println(json.toString());   //接口返回的Json内容
    

    JSONObject jsonObject = JSONObject.fromObject(json.toString());
    Map<String, Object> map = (Map<String, Object>) JSONObject.toBean(jsonObject, Map.class);


    //Map<String, JSONObject> map = (Map<String, JSONObject>) JSONObject.toBean(jsonObject, Map.class);
    
    /*JSONObject jsonObject1 = JSONObject.fromObject(map.get("title").toString());
    Map<String, Object> map1 = (Map<String, Object>) JSONObject.toBean(jsonObject, Map.class);
    
    System.out.println(map1.get("zh"));*/
    //Map<String, JSONObject> map1 = (Map<String, JSONObject>) JSONObject.toBean(temp, Map.class);
    
    //JSONObject jsonObject1 = JSONObject.fromObject(jsonObject.getString("title"));
    //System.out.println(jsonObject1.getString("en"));
    //System.out.println(jsonObject1.getString("zh"));

    Map<String, Object> map1 = (Map<String, Object>) JSONObject.toBean(jsonObject.getJSONObject("title"), Map.class);

    //System.out.println(map1.get("en"));
    //System.out.println(map1.get("zh"));
    String temp = String.valueOf(map1.get("en"));
    if(!"null".equals(temp)){
        enTitle = temp;   //当map1中不含"en"时，temp的值为"null"，注意不是null
    }
    temp = String.valueOf(map1.get("zh"));
    if(!"null".equals(String.valueOf(map1.get("zh")))){
        zhTitle = temp;
    }
    
    System.out.println(enTitle);
    System.out.println(zhTitle);
    
    //System.out.println(map.get("url"));
    this.url = String.valueOf(map.get("url"));
    System.out.println(this.url);
    
    //JSONObject jsonObject2 = JSONObject.fromObject(jsonObject.getString("abstract"));
    //System.out.println(jsonObject2.getString("en"));
    //System.out.println(jsonObject2.getString("zh"));
    Map<String, Object> map2 = (Map<String, Object>) JSONObject.toBean(jsonObject.getJSONObject("abstract"), Map.class);
    //System.out.println(map2.get("en"));
    //System.out.println(map2.get("zh"));
    temp = String.valueOf(map2.get("en"));
    if(!"null".equals(temp)){
        enAbstract = temp;
    }
    temp = String.valueOf(map2.get("zh"));
    if(!"null".equals(temp)){
        zhAbstract = temp;
    }
    System.out.println(enAbstract);
    System.out.println(zhAbstract);
    
    
    JSONArray jary = jsonObject.getJSONArray("type");
    for (int i=0;i<jary.size();i++) {
        JSONObject obj = jary.getJSONObject(i);
        Map<String, Object> map3 = (Map<String, Object>) JSONObject.toBean(obj, Map.class);
        
        
        temp = String.valueOf(map3.get("en"));
        if("null".equals(temp)){
            temp = "";
        }
        enType.add(temp);
        System.out.println(temp);
        
        temp = String.valueOf(map3.get("zh"));
        if("null".equals(temp)){
            temp = "";
        }
        zhType.add(temp);
        System.out.println(temp);
        
    }
    
    related_items.addAll(jsonObject.getJSONArray("related_item"));   //得到的Value为数组
    for(String tem: related_items)
    {
        System.out.println(tem);
    }
}
```
