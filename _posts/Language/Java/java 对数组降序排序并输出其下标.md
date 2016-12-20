---
title: java 对数组降序排序并输出其下标 # 文章页面上的显示名称，可以任意修改，不会出现在URL中
date: 2016-07-22 16:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Java
tags:   # 文章标签，参数可省略
    - 
---
将数组降序排列，并输出排序后对应的原始下标
<!--more-->
### 思路：
先将原数组的元素的值作为key,值对应的下标（可能同一个值有多个位置）的list作为value，存在一个tmp_map中；
然后用Arrays.sort对原数组进行升序排列，逆序存入另一个score_desc数组即为降序排列。
然后将score_desc数组中每个元素的对应位置用tmp_map获取并存入score_index中（值相同的输出多个不同的下标）
### 贴上代码：
```java
	HashMap<Integer, List<Integer>> tmp_map = new HashMap<Integer, List<Integer>>();
	for(int j=0; j<m; j++)
	{
        if(tmp_map.containsKey(score[j]))   //已含有此分数,list后追加
        {
        	tmp_map.get(score[j]).add(j);
        }
        else  //不含此分数，创建list
        {
        	List<Integer> score_list = new ArrayList<Integer>();
        	score_list.add(j);
        	tmp_map.put(score[j], score_list); // 将值和下标存入Map
        }
        		
    }
    Arrays.sort(score); // 升序排列
    for(int j=0; j<m; j++)
    {
    	score_desc[m-j-1] = score[j];
    }
    		
	// 查找原始下标
	int j = 0;
	while(j<m)
	{
		List<Integer> list = tmp_map.get(score_desc[j]);
		for(int k=0; k<list.size(); k++)
		{
			score_index[j] = list.get(k);
			j++;
		}
	}
```