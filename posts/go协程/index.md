# Go协程


<!--more-->

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	go func() {
		fmt.Println("hello")
	}()
	go func() {
		fmt.Println("world")
	}()
	time.Sleep(time.Second * 2) //如果没有Sleep，主线程可能立即退出，goroutine来不及执行
}
```

`sync.Waitgroup`用于**等待一组 goroutine 完成**的同步工具，特别适用于需要阻塞主 goroutine 直到所有子 goroutine 执行完毕的场景。

```go
//完全等待并发结束
package main

import (
	"fmt"
	"sync"
)

func main() {
	wg := sync.WaitGroup{}
	wg.Add(2) //2个任务
	go func() {
		defer wg.Done()
		fmt.Println("hello")
	}()
	go func() {
		defer wg.Done()
		fmt.Println("world")
	}()
	wg.Wait()
} 
```

```go
//子协程隔离错误，不影响mian
package main

import (
	"fmt"
	"time"
)

func f1() {
	defer func() {
		recover()
	}()   //关键隔离匿名函数
	a, b := 10, 0

	a = a / b

}

func main() {
	go f1()
	time.Sleep(time.Second * 1)
	fmt.Println("over")
}

```

---

## 安全性

`锁机制` 

```go
package main

import (
	"fmt"
	"sync"
)

var (
	n    int32
	lock = sync.Mutex{}
)

func foo() {
	for i := 0; i < 10000; i++ {
    lock.Lock()
    n++
    lock.Unlock()
	}
	fmt.Printf("foo %d\n", n)
}

func main() {
	wg := sync.WaitGroup{}
	wg.Add(2) //2个任务
	go func() {
		defer wg.Done()
		foo()
	}()
	go func() {
		defer wg.Done()
		foo()
	}()
	wg.Wait()
}
```

`atomic`

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

var (
	n    int32
	lock = sync.Mutex{}
)

func foo() {
	for i := 0; i < 10000; i++ {
		atomic.AddInt32(&n, 1)
	}
	fmt.Printf("foo %d\n", n)
}

func main() {
	wg := sync.WaitGroup{}
	wg.Add(2) //2个任务
	go func() {
		defer wg.Done()
		foo()
	}()
	go func() {
		defer wg.Done()
		foo()
	}()
	wg.Wait()
}

```





---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://5canx.github.io/posts/go%E5%8D%8F%E7%A8%8B/  

