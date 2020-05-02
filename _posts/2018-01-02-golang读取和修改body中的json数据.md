直接上代码

```go
func bodyMiddleware()gin.HandlerFunc {
   return func(c *gin.Context) {
       var obj map[string]interface{}
      buf, err :=ioutil.ReadAll(c. Request.Body)
      if err!=nil {
         return
      }
      err=json.Unmarshal(buf,&obj)
      if err!=nil {
         return
      }
      if appId, ok := obj["app_id"]; ok {
         fmt.Println(appId)
            obj["customer_id"]="12345"
            buf,err = json.Marshal(&obj)
            if err!=nil {
               return
         }
      }
      c.Request.Body = ioutil.NopCloser(bytes.NewBuffer(buf))
      c.Next()
   }
}
```
这里使用的时gin，其他框架或者原生的http也是一样的方式
