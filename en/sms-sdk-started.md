# Get Started with uSpeedo sms GO SDK

## Preparation

1. Obtain API Key Information

Before making API calls, you need to obtain API key information to generate the X-Signature header. You will need to provide the AccessKeyId and AccessKeySecret, which can be obtained from your console account. See the following link for obtain: [How to Obtain AccessKeyId and AccessKeySecret](https://console.uspeedo.com/dashboard).

2. Apply for SMS Templates

You can apply for SMS templates in the International SMS/SMS Signature module of the SMS service console. See the following link for detailed steps: [How to Apply for SMS Templates](https://console.uspeedo.com/library/template).

## SDK Configuration

### 1. Installation using `go get`

```shell
go get github.com/uSpeedo/usms-sdk-go
```

Tips : If you encounter unstable network connections, you can use a proxy server to accelerate the download. For example, you can use GOPROXY:

```shell
export GOPROXY=https://goproxy.io
```

### 2. Installation using `go mod`

Add the following import statement in your code:

```go
import _ "github.com/uSpeedo/usms-sdk-go"
```

Then, execute the following command in the root directory of your project:

```go
go mod init
go mod tidy
```

### 3. Parameter Description

- Phone Numbers (PhoneNumbers): Supports both international and domestic SMS. For international SMS, use the format (86)13812345678 and prefix the international dialing code to the phone number.
- SMS Template ID (TemplateId): For the first use, you need to apply for a template in the [uSpeedo console](https://console.uspeedo.com/library/template). After the template is approved, pass the template ID to this parameter.
- SMS Template Parameters (TemplateParams): Variables can be passed into the SMS template. If the template has multiple variables, pass them accordingly.
- SMS Signature (SigContent): For the first use, you need to apply for a signature in the [uSpeedo console.]() After the signature is approved, pass it to this parameter. If a default signature exists, this parameter can be left empty.

### 4. Constructing the API Signature

​	see [how to create api signature](https://github.com/crelaber123/somedocs/blob/main/en/api-signature.md)

## Complete Example


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