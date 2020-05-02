当服务端对http的body进行解析到map[string]interface{}时，会出现cli传递的是int类型，而服务端只能断言成float64，而不能将接收到的本该是int类型的直接断言为int
## cli
```go
func main(){
	url:="http://127.0.0.1:8335/api/v2/submit"
	myReq:= struct {
		ProductId   int   `json:"product_id"`
		Mobile     string  `json:"mobile"`
		Content    string   `json:"content"`
		Grade			float64		`form:"grade" json:"grade"`
		Image			string		`form:"image" json:"image"`
	   Longitude   float64        `json:"longitude"`
		Latitude    float64     `json:"latitude"`
	}{
		ProductId:219,
		Mobile:"15911111111",
		Content: "这个软件LOGO真丑",
		Image: "www.picture.com;www.picture.com",
		Longitude: 106.3037109375,
		Latitude: 38.5137882595,
		Grade:9.9,
	}
	reqByte,err:=json.Marshal(myReq)
	req, err := http.NewRequest("POST", url, bytes.NewReader(reqByte))
	if err != nil {
		return
	}
	//设置请求头

	req.Header.Add("Content-Type", "application/json")
	cli := http.Client{
		Timeout: 45 * time.Second,
	}
	resp, err := cli.Do(req)
	if err != nil {
		return
	}
	out, err := ioutil.ReadAll(resp.Body)

	if err != nil {
		return
	}
	fmt.Println(string(out))
}
```

## server

```go
func SubmitV2(c *gin.Context) {
	resp := &dto.Response{}
	obj:=make(map[string]interface{})
	var buf []byte
	var err error
	buf, err =ioutil.ReadAll(c. Request.Body)
	if err!=nil {
		return
	}
	err=json.Unmarshal(buf,&obj)
	if err!=nil {
		return
	}
	fmt.Println("product_id:",reflect.TypeOf(obj["product_id"]))
	fmt.Println("image:",reflect.TypeOf(obj["image"]))
	fmt.Println(obj)
	productId:=obj["product_id"].(float64)
	//注意，这里断言成int类型会出错
	c.Request.Body = ioutil.NopCloser(bytes.NewBuffer(buf))
	if !checkProduct(int(productId)){
		resp.Code = -1
		resp.Message = "xxxxxx"
		c.JSON(http.StatusOK, resp)
		return
	}
	url := config.Optional.OpinionHost + "/api/v1/submit"
	err = http_utils.PostAndUnmarshal(url, c.Request.Body, nil, resp)
	if err != nil {
		logrus.WithError(err).Errorln("Submit: error")
		resp.Code = -1
		resp.Message = "Submit"
	}
	c.JSON(http.StatusOK, resp)
}
```
打印类型，发现product_id是float64类型
原因：json中的数字类型没有对应int，解析出来都是float64
