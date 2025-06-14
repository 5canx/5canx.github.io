# Go管道


<!--more-->

`管道` channel：

环形队列，先进先出，多个协程读一个容器

----

   ```go
   package main
   
   import (
   	"fmt"
   	"time"
   )
   
   func main() {
   	ch := make(chan int, 1)//为0则无缓冲，但能正常写入读取
   	go func() {
   		a := <-ch
   		fmt.Printf("读出成功%d\n", a)
   	}()
   	time.Sleep(time.Second * 1)
   
   	go func() {
   		ch <- 1
   		fmt.Printf("写入成功%s", "1")
   
   	}()
   	time.Sleep(time.Second * 1)
   
   }  
   ```

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	ch := make(chan int, 100) // 缓冲 channel，容量 100
	wg := sync.WaitGroup{}
	wg.Add(2)
	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			ch <- i
		}
	}()
	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			ch <- i
		}
	}()
	wg2 := sync.WaitGroup{}
	wg2.Add(1)
	go func() {
		sum := 0
		for {
			a, ok := <-ch
			if !ok { //channel为空、被关闭、就为false
				break
			} else {
				sum += a
			}
		}
		fmt.Println("sum=", sum)
		wg2.Done()
	}()
	wg.Wait()
	close(ch)
	wg2.Wait()
}
```

---

`用管道去阻塞`

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	ch := make(chan int, 100) // 缓冲 channel，容量 100
	wg := sync.WaitGroup{}
	wg.Add(2)
	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			ch <- i
		}
	}()
	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			ch <- i
		}
	}()
	c2 := make(chan struct{}, 0)
	go func() {
		sum := 0
		for {
			a, ok := <-ch
			if !ok { //channel为空、被关闭、就为false
				break
			} else {
				sum += a
			}
		}
		fmt.Println("sum=", sum)
		c2 <- struct{}{}
	}()
	wg.Wait()
	close(ch)
	<-c2
}
```



---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://hack-scan.github.io/posts/go%E7%AE%A1%E9%81%93/  

