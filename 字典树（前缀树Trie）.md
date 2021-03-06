[leetcode208](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

字典树统计单词个数

https://www.cnblogs.com/xujian2014/p/5614724.html

原理就是每个单词这样一层一层嵌套下去，前缀一样的，那前面的节点就是同一个。
![](https://raw.githubusercontent.com/YuCheng12345/picgoImgs/master/1562309718(1).jpg)


```java
package com.yc.t1;

import java.io.*;
import java.util.HashMap;
import java.util.Map;

public class Trie
{
    private int SIZE = 26;
    private TrieNode root;// 字典树的根

    class TrieNode // 字典树节点
    {
        private int num;// 有多少单词通过这个节点,即由根至该节点组成的字符串模式出现的次数
        private TrieNode[] son;// 所有的儿子节点
        private boolean isEnd;// 是不是最后一个节点
        private char val;// 节点的值

        TrieNode()
        {
            num = 1;
            son = new TrieNode[SIZE];
            isEnd = false;
        }
    }

    Trie() // 初始化字典树
    {
        root = new TrieNode();
    }

    // 建立字典树
    public void insert(String str) // 在字典树中插入一个单词
    {
        if (str == null || str.length() == 0)
        {
            return;
        }
        TrieNode node = root;
        char[] letters = str.toCharArray();//将目标单词转换为字符数组
        for (int i = 0, len = str.length(); i < len; i++)
        {
            int pos = letters[i] - 'a';
            if (node.son[pos] == null)  //如果当前节点的儿子节点中没有该字符，则构建一个TrieNode并复值该字符
            {
                node.son[pos] = new TrieNode();
                node.son[pos].val = letters[i];
            }
            else   //如果已经存在，则将由根至该儿子节点组成的字符串模式出现的次数+1
            {
                node.son[pos].num++;
            }
            node = node.son[pos];
        }
        node.isEnd = true;
    }

    // 计算单词前缀的数量
    public int countPrefix(String prefix)
    {
        if(prefix==null||prefix.length()==0)
        {
            return -1;
        }
        TrieNode node=root;
        char[]letters=prefix.toCharArray();
        for(int i=0,len=prefix.length(); i<len; i++)
        {
            int pos=letters[i]-'a';
            //由于是前缀，在还没遍历完就为null说明不存在这样的前缀，就所以这里为null就返回
            if(node.son[pos]==null)
            {
                return 0;
            }
            else
            {
                node=node.son[pos];
            }
        }
        return node.num;
    }

    // 打印指定前缀的单词
    public String hasPrefix(String prefix)
    {
        if (prefix == null || prefix.length() == 0)
        {
            return null;
        }
        TrieNode node = root;
        char[] letters = prefix.toCharArray();
        for (int i = 0, len = prefix.length(); i < len; i++)
        {
            int pos = letters[i] - 'a';
            //由于是前缀，在还没遍历完就为null说明不存在这样的前缀，就所以这里为null就返回
            if (node.son[pos] == null)
            {
                return null;
            }
            else
            {
                node = node.son[pos];
            }
        }
        preTraverse(node, prefix);
        return null;
    }

    // 遍历经过此节点的单词.
    public void preTraverse(TrieNode node, String prefix)
    {
        if (!node.isEnd)
        {
            for (TrieNode child : node.son)
            {
                if (child != null)
                {
                    preTraverse(child, prefix + child.val);
                }
            }
            return;
        }
        System.out.println(prefix);
    }

    // 在字典树中查找一个完全匹配的单词.
    public boolean has(String str)
    {
        if(str==null||str.length()==0)
        {
            return false;
        }
        TrieNode node=root;
        char[]letters=str.toCharArray();
        for(int i=0,len=str.length(); i<len; i++)
        {
            int pos=letters[i]-'a';
            if(node.son[pos]!=null)
            {
                node=node.son[pos];
            }
            else
            {
                return false;
            }
        }
        //走到这一步，表明可能完全匹配，可能部分匹配，如果最后一个字符节点为末端节点，则是完全匹配，否则是部分匹配
        return node.isEnd;
    }

    // 前序遍历字典树.
    public void preTraverse(TrieNode node)
    {
        if(node!=null)
        {
            System.out.print(node.val+"-");
            for(TrieNode child:node.son)
            {
                preTraverse(child);
            }
        }
    }

    public TrieNode getRoot()
    {
        return this.root;
    }

    public static void main(String[]args) throws IOException {
        Trie tree=new Trie();
        String[] dictionaryData= {"hello","student","computer","sorry","acm","people","experienced","who","reminds","everyday","almost"};
        //构建字典
        for(String str:dictionaryData)
        {
            tree.insert(str);
        }
        //绝度路径
        String filePath="F:\\IdeaProjects\\Nowcoder\\src\\main\\java\\com\\yc\\t1\\sourceFile.txt";
        File file=new File(filePath);
        if(file.isFile() && file.exists())
        {
            InputStreamReader read = new InputStreamReader(new FileInputStream(file));
            BufferedReader bufferedReader = new BufferedReader(read);
            String lineTxt = null;
            Map<String,Integer> countMap=new HashMap<String,Integer>();
            while((lineTxt = bufferedReader.readLine())!= null)
            {
                if(tree.has(lineTxt))
                {
                    if(countMap.containsKey(lineTxt))
                    {
                        countMap.put(lineTxt, countMap.get(lineTxt)+1);
                    }
                    else
                    {
                        countMap.put(lineTxt, 1);
                    }
                }
                else
                {
                    System.out.println(lineTxt+" 不在字典中！");
                }
            }
            for(String s:countMap.keySet())
            {
                System.out.println(s+"出现的次数 "+countMap.get(s));
            }
            read.close();
        }
    }
}
```



文件sourceFile.txt
```
computer
hello
acm
computer
experienced
computer
hello
eclipse
student
hello
hello
hello
hello
hello
experienced
```

输出
```
eclipse 不在字典中！
computer出现的次数 3
student出现的次数 1
acm出现的次数 1
hello出现的次数 7
experienced出现的次数 2
```
