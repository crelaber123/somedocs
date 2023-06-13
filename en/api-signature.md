

**Preparation**

When generating the signature (`X-Signature`) in an API request, you need to provide the `AccessKeyId` and `AccessKeySecret`, which can be obtained from your console account.

**Constructing the Signature String**

To generate the signature, follow these steps:

1. Put all the parameters and their values into a map and sort them in ascending order by the key values.

2. Concatenate all the parameters to form the signature plaintext.

3. Sign the plaintext using SHA1.

   notice：If the interface requires a file upload request (e.g., image or video), the file stream is not involved in the signature. Convert the file into a file stream format and request it separately.

**Step 1:** Get the `AccessKeySecret` from [console](https://console.uspeedo.com) .

```
MjI3YmYyMjItNmM4Mi00ZGM5LWEwNDQtN2EzZjM0Yzk2OWE1
```

**Step 2:** Get the request body from the request and sort it in ascending order based on the ASCII code of the first character (in ascending alphabetical order). If the characters are the same, sort them based on the ASCII code of the second character, and so on.

```json
{
    "Action": "DescribeUHostInstance",
    "Limit": 10
}
```

**Step 3:** Combine the sorted parameters and their corresponding values into the format `parameter=parametervalue`. Append `AccessKeySecret` at the end of this signature string. The resulting string is the unsigned string.

```
ActionDescribeUHostInstanceLimit10MjI3YmYyMjItNmM4Mi00ZGM5LWEwNDQtN2EzZjM0Yzk2OWE1
```

**Calculate the Signature Value**

Encode the unsigned string using SHA1 to generate the request signature.

```
59b1c17e357b8433b502014e271e0615a0908513
```

**Set HTTP Headers**

API requests require the signature information to be passed through the HTTP header. It must include the following four parameters:

- `X-Signature`: The signature value
- `X-Timestamp`: The timestamp (within five minutes)
- `X-Nonce`: A random string
- `X-Access-Key-Id`: The `AccessKeyId` of the console account