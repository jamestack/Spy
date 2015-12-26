# Spy Web Crawler Framework
Spy是一个分布式Web爬虫框架，轻量级、开发灵活并且运行飞快。<br/>
A distributed web crawler,lightweight flexible and runing fast.

## 原理
Spy负责维护一个双向链表组成的任务队列，Spy的Master节点会自动分配任务给各个不同主机上的子节点。<br/>
额具体弄成什么样还没想好，反正我要把他写成分布式滴~哈哈好高大上<br/>

## 进展
现在这个还是单机版滴，具体分布式怎么实现我还在探索中，不过我会不断更新滴，共勉共勉。

## Demo
```Go
package main

import (
    "github.com/JamesWone/Spy"
	"fmt"
)

func main() {
	//新建一个爬虫句柄，句柄可以在Filter和SaveData中动态回调
	pageList := Spy.NewSpy()
	var n int = 0
	//设置过滤器
	pageList.Filter(func(w *Spy.Response) {
		title := w.FindAllStringSubmatch(`(?:\/t\/[\d]+\#reply[\d]+\"\>)([^\<\>]+)(?:\<\/a\>)`)
		for _, v := range title {
			//保存数据，这些数据是可以通过Spy.Add()返回的句柄动态回调的
			w.Data["title"] = v
		}
		//匹配URL
		url := w.FindAllString(`((http|ftp|https)://)(([a-zA-Z0-9\._-]+\.[a-zA-Z]{2,6})|([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}))(:[0-9]{1,4})*(/[a-zA-Z0-9\&%_\./-~-]*)?`)
		var body []Spy.Response
		for i, v := range url {
			//将URL加入队列中
			body[i] := Spy.Add(pageContent, v, &Spy.Option{Method: "Post", Data:map[string][]string{}, Cookie:"", Header:map[string]string{}})
		}
		for n, page := range body {
      w.Data["content"] = page.Data["Content"]		    
		}
	})
	//数据后期处理
	pageList.SaveData(func(w *Spy.Response) {
	    //convert data type
        //update data to mysql..
	})
	
	pageContent := Spy.NewSpy()
	//......
	//......
	//......
	//......
	
	for i := 0; i < 12126; i++ {
		//添加URL进队列,URL可以在Filter中动态添加
		Spy.Add(pageList, fmt.Sprintf("http://www.v2ex.com/recent?p=1%d", i), &Spy.Option{})
	}
	//开始爬数据，设置最大线程
	Spy.Run(40)
}

```
### 关于作者
鄙人姓王，16届学生，外号“脚本小王子”。。（咳咳，这个不提也罢~）这会正在成都某公司实习混荡。<br/>
初入贵圈，请多多关照啊~
### 为什么要写爬虫
本人对Web可是真爱啊，写个PHP、JS什么的完全不在话下啊。。（为什么要提这个。。）<br/>
写爬虫当然是为了更深刻的理解web开发啦~才不是你们想的那么邪恶呢~逃2333
