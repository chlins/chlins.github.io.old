---
title: Golang闭包之坑
date: 2017-10-25 15:51:16
tags: Golang
---

#### Golang 踩过的一个小坑

刚学习go的时候遇到过一个问题，在上面浪费了好多时间，其实也就是个非常简单的问题，只是当时理解不够到位，所以特此贴出帮助新手免得踩坑。。。

直接上代码：

```go
package main

import (
	"fmt"
     "time"
)

func main() {
  for i := 0; i < 10 i++ {
    go func() {
      fmt.Println(i) 
    }()
  }
  time.Sleep(1 * time.Second)
}
```

运行之前理所当然的认为只是开了10个goroutine来执行，结果输出了10个10，本来预期输出的是0--9，后来才知道原来这是闭包的一个坑点，如果在循环中使用goroutine，一定要特别注意采用显示的变量调用。修正：

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    var wg sync.WaitGroup
    for i := 0; i < 10; i++ {
      wg.Add(1)
      go func(i int) {
        defer wg.Done()
        fmt.Println(i)
      }(i)
    }
    wg.Wait()
}
```






