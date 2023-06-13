# Get Started with uSpeedo GO SDK

## Preparation

1. Obtain API Key Information

Before making API calls, you need to obtain API key information to generate the X-Signature header. You will need to provide the AccessKeyId and AccessKeySecret, which can be obtained from your console account. See the following link for obtain: [How to Obtain AccessKeyId and AccessKeySecret](https://console.uspeedo.com/dashboard).

## SDK Configuration

### 1. Installation using `go get`

```shell
go get github.com/uSpeedo/{go-sdk}
```

Tips :

a、{go-sdk} is just a placeholder. Please replace it with the specific SDK you are using when actually using it.

b、If you encounter unstable network connections, you can use a proxy server to accelerate the download. For example, you can use GOPROXY:

```shell
export GOPROXY=https://goproxy.io
```

### 2. Installation using `go mod`

Add the following import statement in your code:

```go
import _ "github.com/uSpeedo/{go-sdk}"
```

Then, execute the following command in the root directory of your project:

```go
go mod init
go mod tidy
```

### 3. Constructing the API Signature

​	see [how to create api signature](https://console.uspeedo.com/dashboard)

## Complete Example

For a comprehensive SDK documentation, please refer to the following document: [SDK Documentation](https://example.com/)

```go
package main

import (
	"encoding/json"
	"fmt"
	"github.com/uSpeedo/usms-sdk-go/private/utils"
	"time"

	"github.com/uSpeedo/usms-sdk-go/services/usms"
	"github.com/uSpeedo/usms-sdk-go/um"
	"github.com/uSpeedo/usms-sdk-go/um/auth"
	"github.com/uSpeedo/usms-sdk-go/um/config"
	"github.com/uSpeedo/usms-sdk-go/um/log"
)

func main() {
	cfg := config.NewConfig()
	cfg.LogLevel = log.DebugLevel

	credential := auth.NewCredential()
	credential.AccessKeyId = "your AccessKeyId"
	credential.AccessKeySecret = "your AccessKeySecret"

	client := usms.NewClient(&cfg, &credential)
	// send request
	req := client.NewSendBatchUSMSMessageRequest()
	req.AccountId = um.Int(0)
	req.Action = um.String("SendBatchUSMSMessage")
	req.TaskContent = []usms.SendBatchInfo{
		{
			TemplateId: "your TemplateId",
			SenderId:   "",
			Target: []usms.SendBatchTarget{
				{
					Phone: "86130xxxx1321",
				},
				{
					Phone: "86130xxxx1321",
				},
			},
		},
	}
	//add header
	req.SetNonce(utils.RandStr(10))
	req.SetAccessKeyId(credential.AccessKeyId)
	req.SetSignature(credential.CreateSign(JSONMethod(req)))
	t, _ := time.ParseDuration("-2m")
	req.SetTimestamp(time.Now().Add(t).Unix())
	resp, err := client.SendBatchUSMSMessage(req)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%+v", resp)
}

func JSONMethod(content interface{}) map[string]interface{} {
	data, _ := json.Marshal(&content)
	m := make(map[string]interface{})
	_ = json.Unmarshal(data, &m)
	return m
}
```