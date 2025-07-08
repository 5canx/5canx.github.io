# Go基础学习笔记

<!--more-->

## Golang的接口是一种抽象的类型

> Go语言中的接口（interface）是一组方法签名的集合，是一种抽象类型。接口定义了方法，但没有实现，而是由具体的类型（struct）实现这些方法，因此接口是一种实现多态的机制。

### 接口定义
```go
type 接口名称er interface{
    func(arg1 类型,arg2 类型2) 返回值
    func(arg3 类型3) 返回值3
}  //最好以er结尾表示  不加func也可以
```

### 结构体定义

```go
type 结构体名称 struct{
    Name 类型（string、int、bool等）
}
```

### 典型Demo

```go
package main

import (
	"fmt"
)

// Worker 接口定义了一个方法 Work
type Worker interface {
	Work()
}

// programmer 结构体代表一个程序员,定义数据类型
type programmer struct {
	Java   string
	Linux  string
	Python string
}

// Work 实现了 Worker 接口的方法
func (p *programmer) Work() {
	// 输出程序员的类型和使用的编程语言
	fmt.Printf("小李正在学习%s\n", p.Java)
	fmt.Printf("小张正在学习%s\n", p.Linux)
	fmt.Printf("小王正在学习%s\n", p.Python)
}

func main() {
	// 创建一个 programmer 实例，并初始化字段
	myProgrammer := programmer{
		Java:   "Java语言",
		Linux:  "Linux运维",
		Python: "Python语言",
	}
	var Myworker Worker = &myProgrammer // 接口变量赋值也叫实现结构体
	Myworker.Work() //调用接口方法
}
```

{{< admonition info >}}
**以 上 代 码 作 为 基 础 进 行 拓 展** 
{{< /admonition >}}

### 一个类型实现多个接口

```go
package main

import (
	"fmt"
	"time"
)

// Worker 接口定义了一个方法 Work
type Worker interface {
	Work()
}

type Stoper interface {
	Stop()
}

// programmer 结构体代表一个程序员
type programmer struct {
	Java   string
	Linux  string
	Python string
}

// Work 实现了 Worker 接口的方法
func (p *programmer) Work() { //指针接收者可以修改字段值
	// 输出程序员的类型和使用的编程语言
	fmt.Printf("小李正在学习%s\n", p.Java)
	fmt.Printf("小张正在学习%s\n", p.Linux)
	fmt.Printf("小王正在学习%s\n", p.Python)
}
func (p programmer) Stop() { //值接收者不修改字段值
	fmt.Printf("小李已经停止学习%s\n", p.Java)
	fmt.Printf("小张已经停止学习%s\n", p.Linux)
	fmt.Printf("小王已经停止学习%s\n", p.Python)
}
func main() {
	// 创建一个 programmer 实例，并初始化字段
	myProgrammer := programmer{
		Java:   "Java语言",
		Linux:  "Linux运维",
		Python: "Python语言",
	}
	x1 := programmer{} // 接口变量赋值也叫实现结构体

	var Myworker Worker = &myProgrammer // 接口变量赋值也叫实现结构体
	Myworker.Work()                     //调用接口方法
	time.Sleep(time.Second * 2)
	x1.Stop()
}
```

### 多个类型实现一个接口

```go
package main

import (
	"fmt"
)

// Worker 接口定义了一个方法 Work
type Worker interface {
	Work()
}

// programmer 结构体代表一个程序员
type programmer struct {
	Python string
}
type older struct {
	age int
}

func (p programmer) Work() {
	fmt.Printf("%s的程序员正在工作", p.Python)
	fmt.Println("") //换行
}
func (o older) Work() {
	fmt.Printf("%d岁的程序员正在工作", o.age)
}
func main() {
	// 创建一个 programmer 实例，并初始化字段
	myProgrammer := programmer{
		Python: "Python",
	}
	o := older{age: 20}
	var Myworker Worker = &myProgrammer // 接口变量赋值也叫实现结构体
	Myworker.Work()                     //调用接口方法
	o.Work()
}
```



### 接口的嵌套

```go
package main

import (
	"fmt"
)

// Worker 接口定义了一个方法 Work
type Worker interface {
	Work()
}
type Moyuer interface {
	Moyu()
}
type Peoler interface {
	Worker
	Moyuer
}
type Computer struct {
	something string
}

func (c *Computer) Work() {
	fmt.Printf("%s都要工作\n", c.something)
}
func (c *Computer) Moyu() {
	fmt.Printf("%s也要摸鱼\n", c.something)
}

func main() {
	sth := Computer{something: "程序员"}
	x := &sth
	x.Work()
	x.Moyu()
}
```



### 空接口

{{< admonition info >}}
空接口类型的变量可以存储任意类型的变量。
{{< /admonition >}}

```go
package main

import (
	"fmt"
)

func main() {
	var x interface{}
	v1 := "程序猿"
	v2 := 200
	v3 := true
	x = v1
	fmt.Printf("%T类型 : %v\n", x, x)
	x = v2
	fmt.Printf("%T类型 : %v\n", x, x)
	x = v3
	fmt.Printf("%T类型 : %v\n", x, x)
}
```

{{< admonition info >}}
因为可以存储任意类型的变量，所以我们把他当做方法中的参数使用。
{{< /admonition >}}

```go
package main

import (
	"fmt"
)

func test(w interface{}) {
	fmt.Printf("Hello %s", w)
}
func main() {
	words := "Golang"
	test(words)
} //Hello Golang
```



### 类型断言

{{< admonition info >}}
类型断言的作用就是判断空接口的值和值的类型

{{< /admonition >}}

```go
package main

import (
	"fmt"
)

func main() {
	var words interface{} = "Golang"
    switch value := words.(type) {            //语法格式为：x.(T)
	case string:
		fmt.Printf("String类型，值为%v", value)
	case int:
		fmt.Printf("Int类型，值为%v", value)
	case bool:
		fmt.Printf("Bool类型，值为%v", value)
	}

}
```

---

## 操作redis

```go
package main

import (
	"context"
	"fmt"
	"os"
	"time"

	"github.com/redis/go-redis/v9"
)

func string(ctx context.Context, client *redis.Client) {
	key := "test"
	value := "test_v"
	err := client.Set(ctx, key, value, time.Second*1).Err() //写_ttl为1秒
	checkError(err)
	time.Sleep(time.Second * 2)
	v2, err := client.Get(ctx, key).Result() //读
	checkError(err)
	fmt.Println(v2)

	client.Del(ctx, key)
}

func main() {
	cilent := redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "123456",
		DB:       0,        
	})
	ctx := context.TODO()
	string(ctx, cilent)

}
func checkError(err error) {
	{
		if err != nil {
			if err == redis.Nil {
				fmt.Println("key does not exist")
				return
			}
			fmt.Println(err)
			os.Exit(1)
		}
	}
}
```

## 网络交互

```go
package main

import (
	"context"
	"fmt"
	"log"
	"net/http"
	"net/url"
	"time"

	"github.com/gin-gonic/gin"
	"github.com/redis/go-redis/v9"
)

var rdb *redis.Client

func initRedis() {
	rdb = redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "123456",
		DB:       0,
	})

	ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
	defer cancel()
	if _, err := rdb.Ping(ctx).Result(); err != nil {
		log.Fatalf("无法连接Redis: %v", err)
	}
}

func getFieldValue(key, field string) (string, error) {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel()

	value, err := rdb.HGet(ctx, key, field).Result()
	if err == redis.Nil {
		return "", fmt.Errorf("字段不存在")
	}
	if err != nil {
		return "", fmt.Errorf("查询失败: %w", err)
	}
	return value, nil
}

func main() {
	initRedis()

	r := gin.Default()

	// 新版路由（路径参数）
	r.GET("/api/query/:key/:field", func(c *gin.Context) {
		// 获取并解码路径参数
		rawKey := c.Param("key")
		rawField := c.Param("field")

		// URL解码参数
		key, err := url.PathUnescape(rawKey)
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": "key参数解码失败",
			})
			return
		}

		field, err := url.PathUnescape(rawField)
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": "field参数解码失败",
			})
			return
		}

		// 打印解码后的参数（调试用）
		log.Printf("解码参数: key=%q, field=%q", key, field)

		// 参数有效性检查
		if key == "" || field == "" {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": "key和field不能为空",
			})
			return
		}

		value, err := getFieldValue(key, field)
		if err != nil {
			c.JSON(http.StatusNotFound, gin.H{
				"error": err.Error(),
			})
			return
		}

		c.JSON(http.StatusOK, gin.H{
			"key":   key,
			"field": field,
			"value": value,
		})
	})

	log.Println("服务启动在 :8080")
	if err := r.Run(":8080"); err != nil {
		log.Fatalf("服务启动失败: %v", err)
	}
}
http://localhost:8080/api/query/new_que/休克早期血压的变化是（%20%20%20）。
```

`变换路由`

```go
package main

import (
	"context"
	"fmt"
	"log"
	"net/http"

	"github.com/gin-gonic/gin"
	"github.com/redis/go-redis/v9"
)

var rdb *redis.Client

func initRedis() {
	rdb = redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "123456",
		DB:       0,
	})

	// 测试连接
	if _, err := rdb.Ping(context.Background()).Result(); err != nil {
		log.Fatalf("无法连接Redis: %v", err)
	}
}

func getFieldValue(key, field string) (string, error) {
	ctx := context.Background()

	// 直接获取指定键和字段的值
	value, err := rdb.HGet(ctx, key, field).Result()
	if err == redis.Nil {
		return "", fmt.Errorf("字段不存在")
	}
	if err != nil {
		return "", fmt.Errorf("查询失败: %w", err)
	}

	return value, nil
}

func main() {
	initRedis()

	r := gin.Default()

	// 精确查询接口
	r.GET("/api/query", func(c *gin.Context) {
		key := c.Query("key")     // Redis键名
		field := c.Query("field") // 字段名

		if key == "" || field == "" {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": "key和field参数不能为空",
			})
			return
		}

		value, err := getFieldValue(key, field)
		if err != nil {
			c.JSON(http.StatusNotFound, gin.H{
				"error": err.Error(),
			})
			return
		}

		c.JSON(http.StatusOK, gin.H{
			"key":   key,
			"field": field,
			"value": value, // 返回实际的字段值
		})
	})

	log.Println("服务启动在 :8080")
	r.Run(":8080")
}

```



`请求`

```go
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
	"net/url"
)

func QueryRedisField(apiURL, key, field string) (string, error) {
	// 构建请求参数
	params := url.Values{}
	params.Add("key", key)
	params.Add("field", field)

	// 发送请求
	resp, err := http.Get(apiURL + "?" + params.Encode())
	if err != nil {
		return "", fmt.Errorf("请求失败: %v", err)
	}
	defer resp.Body.Close()

	// 解析响应
	var result struct {
		Value string `json:"value"`
		Error string `json:"error"`
	}
	if err := json.NewDecoder(resp.Body).Decode(&result); err != nil {
		return "", fmt.Errorf("解析响应失败: %v", err)
	}

	if result.Error != "" {
		return "", fmt.Errorf(result.Error)
	}

	return result.Value, nil
}

func main() {
	apiURL := "http://localhost:8080/api/query"

	// 示例查询
	value, err := QueryRedisField(apiURL, "new_que", "休克早期血压的变化是（   ）。")
	if err != nil {
		fmt.Printf("查询失败: %v\n", err)
		return
	}

	fmt.Printf("查询结果: %s\n", value)
}
```



---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://5canx.github.io/posts/golang%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/  

