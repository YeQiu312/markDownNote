## 八斗科技

### Service层的作用是什么？



### Linux的基本命令-释放内存，查看占用端口



### Tomcat下有哪些文件夹？文件夹里面放什么



### 项目是自己做的嘛？有没有在云端部署过？



### 了解哪些原生sql，原生sql的分页操作是如何执行的？



## 三和软件

### 原生sql的分页操作是如何执行的？





## CVTE笔试

#### 1、已知一算术表达式的中缀形式为 A-B*C+D/E，其前缀和后缀形式分别为 (B)

A 前缀形式:-+A*BC/DE
  后缀形式:CBA*-DE/+

B  前缀形式:+-A*BC/DE
  后缀形式:ABC*-DE/+

C  前缀形式:-+A*BC/DE
  后缀形式:ABC*-DE/+

D  +-A*BC/DE前缀形式:
  后缀形式:CBA*-DE/+



#### 2.动态规划

CVTE的软件开发部门最近在做一个文本编辑器，其中需要实现一个单词搜索功能。5搜索包括精准搜索和相似搜索，相似搜索是通过计算两个单词的差异度，如果差异度小于3，则这两个单词是相似的，可以搜索命中。单词word1和word2差异度: 由单词word1转换成单词word2需要的最小修改次数，大小写不敏感，修改操作包括如下:
(1)删除一个字符
(2)插入一个字符(3)替换一个字符请使用Java补全下面代码，实现搜索功能，其中入参text为待搜索的原文本，可以通过”"”"、"“进行分词，入参keyword是搜索关键词，方法返回相同战者相似的单词
[输入输出样例]
入参text:helloworls
入参keyword:world
输出:worls说明: worls->只需要把s更换为d，就可以得到world，因此差异度是1，小于3，因此wors和world是相似的的

入text: hello,worls
入参keyword:world
输出: worls
说明: worls->只需要把s更换为d，就可以得到world，因此差异度是1，小于3，因此----

```java
public List<string> search(string text, string keyword) {
    List<String> result = new ArrayList<>();
	//单词变小写
    String[] words = text.toLowerCase().split([ ,.]);
    for(String word: words){
        if(word.equals(keyword)){
            result.add(word);
        } else if (isSimilar(word,keyword)){
            result.add(word);
        }
    }
    
    public boolean isSimilar(string word, string keyword){
        
        
    }
}
```

