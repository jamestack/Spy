# Spy Web Crawler Framework
Spy是一个轻量级的爬虫框架，开发灵活并且运行飞快。<br/>
A lightweight web crawler,flexible and runing fast.

## 进展
beta v1.0 基本功能<br/>
beta v1.1 修正了多线程队列算法

## Demo
* 这段代码会爬行http://www.v2ex.com 的文章列表，将文章标题和链接保存在当前目录的veex.txt中。<br/>
* 经测试，在开40个线程的情况下只需要约25分钟就可以爬完v2ex上面的所有文章（约24万条记录），如果网站处理能力够强，增加线程数还可以更快。<br/>
* PS:爬数据时，线程建议不要超过50，否则网站服务器很有可能直接Down掉哦。
```Go
package main

import (
	"Spy"
	"bufio"
	"fmt"
	"os"
	"runtime"
)

func main() {
	runtime.GOMAXPROCS(runtime.NumCPU())
	num := 0
	//新建一个爬虫句柄，句柄可以动态回调
	pageList := Spy.NewSpy()
	//设置过滤器
	pageList.Filter(func(w *Spy.Response) {
		regstr := w.FindAllStringSubmatch(`\<span\sclass\=\"item\_title\"\>\<a\shref\=\"([^\"]+)\"\>([^\<\>]+)\<\/a\>\<\/span\>`)
		file, err := os.OpenFile("v2ex.txt", os.O_WRONLY|os.O_APPEND|os.O_CREATE, 0666)
		bufer := bufio.NewWriter(file)
		for _, v := range regstr {
			if err == nil {
				num++
				bufer.WriteString("http://www.v2ex.com" + v[1] + "\r\n")
				bufer.WriteString(v[2] + "\r\n\r\n")
				fmt.Println(num, v[1], v[2])
			}
		}
		bufer.Flush()
		file.Close()
	})
	//数据处理
	pageList.SaveData(func(w *Spy.Response) {

	})

	for i := 0; i < 12160; i++ {
		//添加URL,URL可以在Filter中动态添加
		Spy.Add(pageList, fmt.Sprintf("http://www.v2ex.com/recent?p=%d", i), &Spy.Option{})
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
