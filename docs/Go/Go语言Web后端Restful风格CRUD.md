> Go 语言搭建的一个简单的 Web 后端 Restful 风格的 CRUD 基础框架

## 主要技术

**- Gin：**使用 GO/golang 语言实现的 HTTP Web 框架。接口简洁，性能极高。截止 1.4.0 版本，包含测试代码仅 14K，其中测试代码 9K 左右，也就是说框架源码仅 5K 左右。

**- Viper：**一个完整的 Go 应用程序配置解决方案，优势在于开发项目中你不必去操心配置文件的格式而是让你腾出手来专注于项目的开发。

**- GORM：**一个使用 GO 语言编写的 ORM 框架，文档齐全，对开发者友好，支持主流的数据库。

**- Mysql**

## 目录结构

![](https://resource.lzyan.fun/PigGo/20220626201447.png)

## 配置

`cors.go` 跨域配置

```go
package config

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func CorsConfig() gin.HandlerFunc {
	return func(c *gin.Context) {
		c.Header("Access-Control-Allow-Origin", "*") // 可将将 * 替换为指定的域名
		c.Header("Access-Control-Allow-Methods", "POST, GET, OPTIONS, PUT, DELETE, UPDATE")
		c.Header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept, Authorization")
		c.Header("Access-Control-Expose-Headers", "Content-Length, Access-Control-Allow-Origin, Access-Control-Allow-Headers, Cache-Control, Content-Language, Content-Type")
		c.Header("Access-Control-Allow-Credentials", "true")
		c.Writer.Header().Set("Access-Control-Max-Age", "86400")
		if c.Request.Method == http.MethodOptions {
			c.AbortWithStatus(200)
		} else {
			c.Next()
		}
	}
}
```

`db.go ` 数据库配置

```go
package config

import (
	"fmt"
	"github.com/spf13/viper"
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
)

var DB *gorm.DB
var PORT string

func init() {
	var err error
	viper.SetConfigName("app")
	viper.SetConfigType("properties")
	viper.AddConfigPath("./")
	err = viper.ReadInConfig()
	if err != nil {
		panic(fmt.Errorf("Fatal error config file: %w \n ", err))
	}
	if err := viper.ReadInConfig(); err != nil {
		if _, ok := err.(viper.ConfigFileNotFoundError); ok {
			fmt.Println("No File")
		} else {
			fmt.Println("Find file but have err..")
		}
	}
	PORT = viper.GetString("server.port")
	url := viper.GetString("db.url")
	db := viper.GetString("db.databases")
	username := viper.GetString("db.username")
	password := viper.GetString("db.password")
	// 拼接url
	dsn := username + ":" + password + "@tcp(" + url + ")/" + db + "?charset=utf8mb4&parseTime=True&loc=Local"
	fmt.Printf(dsn)
	DB, err = gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		panic(err)
	}
}
```

`interceptor.go` 拦截器配置

```go
package config

import (
	"github.com/gin-gonic/gin"
	"log"
	"time"
)

// http请求 自定义拦截器
func HttpInterceptor() gin.HandlerFunc {

	return func(c *gin.Context) {
		t := time.Now()
		// 请求前
		c.Next()
		// 请求后
		latency := time.Since(t)
		log.Println("用时:", latency)

		// 获取发送的 status
		status := c.Writer.Status()
		log.Println("请求状态:", status)

	}

}
```

`app.properties` 配置文件

```properties
server.port=8888
db.driver=mysql
db.url=127.0.0.1:3306
db.databases=test
db.username=root
db.password=lzyan
```

## Dao 层

`employees_info.go` 定义 CRUD 的接口

```go

package dao

import (
	"context"
	"demoCRUD/pkg/entity"
)

type EmployeesInfo interface {
	// 添加员工
	AddEmployee(c context.Context, info entity.EmployeesInfo) bool

	// 查找所有员工
	FindAllEmployee(c context.Context) []entity.EmployeesInfo

	// 根据id更新员工信息
	UpdateEmployeeById(c context.Context, info entity.EmployeesInfo) bool

	// 删除员工
	DeleteEmployeeById(c context.Context, id int64) bool
}
```

`employees_info_impl.go` 接口的实现

```go
package impl

import (
	"context"
	"demoCRUD/pkg/config"
	"demoCRUD/pkg/entity"
	"gorm.io/gorm"
)

type EmployeesInfoImpl struct {
	db *gorm.DB
}

func NewEmployeesInfoImpl() *EmployeesInfoImpl {
	return &EmployeesInfoImpl{db: config.DB}
}

func (impl EmployeesInfoImpl) AddEmployee(c context.Context, info entity.EmployeesInfo) bool {
	impl.db.Save(&info)
	return true
}

func (impl EmployeesInfoImpl) FindAllEmployee(c context.Context) []entity.EmployeesInfo {
	var infos []entity.EmployeesInfo
	impl.db.Find(&infos)
	return infos
}

func (impl EmployeesInfoImpl) UpdateEmployeeById(c context.Context, info entity.EmployeesInfo) bool {
	impl.db.Model(&entity.EmployeesInfo{}).Where("id", info.Id).Updates(entity.EmployeesInfo{Name: info.Name, Age: info.Age, Number: info.Number})
	return true
}

func (impl EmployeesInfoImpl) DeleteEmployeeById(c context.Context, id string) bool {
	impl.db.Delete(&entity.EmployeesInfo{}, id)
	return true
}
```

`employees.go` 实体

```go
package entity

type EmployeesInfo struct {
	Id     int64 `json:"id"`
	Name   string `json:"name"`
	Age    int64  `json:"Age"`
	Number string `json:"Number"`
}

// 表名
func (stu EmployeesInfo) TableName() string {
	return "employees_info"
}
```

## Controller 层

`employees_controller.go` controller 实现

```go
package controller

import (
	"demoCRUD/pkg/dao/impl"
	"demoCRUD/pkg/entity"
	"encoding/json"
	"github.com/gin-gonic/gin"
	"io/ioutil"
)

type EmployeesControllerImpl struct {
	dao *impl.EmployeesInfoImpl
}

type EmployeesController interface {
	AddEmployee(c *gin.Context)
	FindALlEmployee(c *gin.Context)
	UpdateEmployee(c *gin.Context)
	DeleteEmployee(c *gin.Context)
}

func NewEmployeesControllerImpl() *EmployeesControllerImpl {
	return &EmployeesControllerImpl{dao: impl.NewEmployeesInfoImpl()}
}

func (impl EmployeesControllerImpl) AddEmployee(c *gin.Context) {
	body := c.Request.Body
	bytes, err := ioutil.ReadAll(body)
	info := entity.EmployeesInfo{}
	json.Unmarshal(bytes, &info)
	if err != nil {
		panic(err)
	}
	isOk := impl.dao.AddEmployee(c, info)
	c.JSON(200, map[string]interface{}{"code": 0, "msg": "", "data": isOk})
}

func (impl EmployeesControllerImpl) FindAllEmployee(c *gin.Context) {
	employeesInfo := impl.dao.FindAllEmployee(c)
	c.JSON(200, map[string]interface{}{"code": 0, "msg": "", "data": employeesInfo})
}

func (impl EmployeesControllerImpl) UpdateEmployee(c *gin.Context) {
	body := c.Request.Body
	bytes, err := ioutil.ReadAll(body)
	info := entity.EmployeesInfo{}
	json.Unmarshal(bytes, &info)
	if err != nil {
		panic(err)
	}
	isOk := impl.dao.UpdateEmployeeById(c, info)
	c.JSON(200, map[string]interface{}{"code": 0, "msg": "", "data": isOk})
}
func (impl EmployeesControllerImpl) DeleteEmployee(c *gin.Context) {
	id := c.Param("id")
	isOk := impl.dao.DeleteEmployeeById(c, id)
	c.JSON(200, map[string]interface{}{"code": 0, "msg": "", "data": isOk})
}
```

`router.go` 路由

```go
package web

import (
	"demoCRUD/pkg/config"
	"demoCRUD/pkg/web/controller"
	"github.com/gin-gonic/gin"
)

func RunHttp() {
	r := gin.Default()
	// 拦截器
	r.Use(config.HttpInterceptor())
	// 跨域
	r.Use(config.CorsConfig())
	// 路由组
	routerGroup := r.Group("/")
	{
		routerGroup.POST("/add/", controller.NewEmployeesControllerImpl().AddEmployee)
		routerGroup.GET("/find/", controller.NewEmployeesControllerImpl().FindAllEmployee)
		routerGroup.POST("/update/", controller.NewEmployeesControllerImpl().UpdateEmployee)
		routerGroup.POST("/delete/:id", controller.NewEmployeesControllerImpl().DeleteEmployee)
	}

	r.Run("127.0.0.1:" + config.PORT)
}

```

## 启动

`main.go` 主函数

```go
package main

import "demoCRUD/pkg/web"

func main() {
	web.RunHttp()
}
```

## 总结

项目源码：https://github.com/YCheer/DemoCRUD