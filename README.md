# Govee API
## **API Reference**
## **API Version 1.3**

You can always get the latest API guide through the link below: <br>
[GooveAPIReference.pdf](https://govee-public.s3.amazonaws.com/developer-docs/GoveeAPIReference.pdf)

# Table Of Contents

1. [Getting Started with the Govee API](#gettingstarted)
2. [How to Use Govee API Key](#useapikey)
3. [Rate Limiting](#ratelimiting)
4. [Govee APIs](#apis)
    1. [DeviceList](#devicelist)
    2. [DeviceControl](#devicecontrol)
    3. [DeviceState](#devicestate)
5. [F&Q](#fandq)
6. [Version History](#versionhistory)

# Getting Started with the Govee API <a id="gettingstarted">
Use the Govee API to control your devices and query your device state.
The device models supported by the current version are **`H6160, H6163,
H6104, H6109, H6110, H6117, H6159, H7022, H6086, H6089, H6182,
H6085, H7014, H5081, H6188, H6135, H6137, H6141, H6142, H6195,
H7005, H6083, H6002, H6003, H6148, H6052, H6143, H6144, H6050,
H6199, H6054, H5001, H6050, H6154, H6143, H6144, H6072, H6121,
H611A, H5080, H6062, H614C, H615A, H615B, H7020, H7021, H614D,
H611Z, H611B, H611C, H615C, H615D, H7006, H7007, H7008, H7012,
H7013, H7050, H6051, H6056, H6061, H6058, H6073, H6076, H619A,
H619C, H618A, H618C, H6008, H6071, H6075 , H614A, H614B, H614E,
H618E, H619E`** <br>
Here are a few things to know before you start.
 1. [How to use Govee API key](#useapikey)
 2. [Rate limiting](#ratelimiting)

 # How to Use Govee API Key <a id="useapikey">
 The Govee API Key is used for authentication. Requests that require
authentication will return `401 Unauthorized`. <br>
Use http header `'Govee-API-Key'` to send Govee API Key. You can
learn how to use it in the API examples.

# Rate Limiting <a id="ratelimiting">
For all requests, the rate limit allows for up to 100 requests per minute.
Requests are associated with the originating IP address. <br><br>
The returned HTTP headers of any API request show your current rate
limit status:
| Header Name | Description |
| ----------- | ----------- |
| X-RateLimit-Limit | The maximum number of requests you're permitted to make per minute. |
| X-RateLimit-Remaining | The number of requests remaining in the current rate limit window. |
| X-RateLimit-Reset | The time at which the current rate limit window resets in [**UTC epoch seconds**](http://en.wikipedia.org/wiki/Unix_time). |

# Govee APIs <a id="apis">

## DeviceList <a id="devicelist">

#### **GET /v1/devices**
Get information about all supported devices in the account.

#### Request Headers
`Govee-API-Key: {your_Govee_API_KEY}`

#### Response Body parameters
`Content-Type: application/json`

| Parameter | Type | Required | Description |
| --------- | ---- | -------- | ----------- |
| code | `number` | Yes | The `code` value will be 200 when the request process successfully. |
| message | `string` | Yes |  The message value will be "success" when the request process successfully or it will be the error message. |
| data | `object` | Yes | The response data |
| data.devices | `array` | Yes | Return your devices supported by the Govee API. |
| data.devices.model | `string` | Yes | Product Model of your device |
| data.devices.device | `string` | Yes | Mac address of your device. Use device and model to identify the target one device. |
| data.devices.controllable | `boolean` | Yes | Controllable will be true when the device support commands to control. |
| data.devices.retrievable | `boolean` | Yes | Retrievable will be true when the device support querying the current device state. |
| data.devices.supportCmds | `string[]` | Yes | Commands supported by the device |

#### Errors
| Error | HTTP Status Code | Description |
| ---- | ----------------- | ----------- |
| AuthorizationError | <center>`403`</center> | Indicates that the Govee API key is invalid. |
| InternalError | <center>`500`</center> | Indicates an internal service error. | 

#### Example

##### Sample Request
```
Request URL: https://developer-api.govee.com/v1/devices
Request Method: GET
Request Header:
Govee-API-Key: 00000000-729c-4b82-b536-000000000
```

##### Sample Response
```javascript
{
 "data": {
 "devices": [
    {
    "device": "99:E5:A4:C1:38:29:DA:7B",
    "model": "H6159",
    "deviceName": "test light",
    "controllable": true,
    "retrievable": true,
    "supportCmds": [
        "turn",
        "brightness",
        "color",
        "colorTem"
    ],
    "properties": {
        "colorTem": {
            "range": {
                "min": 2000,
                "max": 9000
            }
        }
    }
    },
    {
    "device": "34:20:03:2e:30:2b",
    "model": "H5081",
    "deviceName": "Smart Plug",
    "controllable": true,
    "retrievable": true,
    "supportCmds": [
        "turn"
    ]
    }
 ]
 },
 "message": "Success",
 "code": 200
}
```

## DeviceControl <a id="devicecontrol">
Use supported commands to control the device.

#### **PUT /v1/devices/control**
#### Request Headers
```
Content-Type: application/json
Govee-API-Key: {your_Govee_API_KEY}
```

#### Request Body parameters
| Parameter | Type | Required | Description |
| -------- | ----- | -------- | ----------- |
| device | `string` | Yes | The Mac address of the device you want to control |
| model | `string` | Yes | The Model of the device you want to control |
| cmd | `object` | Yes | Device commands |
| `cmd.name` | `string` | Yes | Device commands. Use with `cmd.value`.<br> Valid values: "turn", "brightness", "color", "colorTem" |
| `cmd.value` | `number` | Yes | Device commands. Use with `cmd.name`. |

Demos:
```javascript
{"name": "turn", value: "off"}
{"name": "brightness", value: 10}
{"name": "color", value: {"r": 255, "g": 255, "b": 255}}
{"name": "colorTem", value: 7000}
```
**When `cmd.name` is "turn":<br>**
the valid values are "on" and “off".<br>
Type: string<br><br>
**When `cmd.name` is "brightness":<br>**
the valid values are between 0 and 100, and 0 will turn off the device<br>
Type: number<br><br>
**When `cmd.name` is "color":**<br>
Type: object<br><br>
**cmd.value.r**<br>
Red value. Valid range is 0~255.<br>
Type: number<br><br>
**cmd.value.g**<br>
Green value. Valid range is 0~255.<br>
Type: number<br><br>
**cmd.value.b**<br>
Blue value. Valid range is 0~255.<br>
Type: number<br><br>
**When `cmd.name` is "colorTem":**<br>
the valid values are returned by DeviceList, there will be one more field
named “properties”
```javascript
"properties": {
    "colorTem": {
        "range": {
            "min": 2000,
            "max": 9000
        }
    }
 }
```
#### Response Body parameters
`Content-Type: application/json`

| Parameter | Type | Required | Description |
| --------- | ---- | -------- | ----------- |
| code | `number` | Yes | The `code` value will be 200 when the request process successfully. |
| message | `string` | Yes |  The message value will be "success" when the request process successfully or it will be the error message. |
| data | `object` | Yes | The response data |

#### Errors
| Error | HTTP Status Code | Description |
| ----- | ---------------- | ----------- |
| AuthorizationError | <center>`403`</center> | Indicates that the Govee API key is invalid. |
| InternalError | <center>`500`</center> | Indicates an internal service error. | 
| InvalidParameter | <center>`400`</center> | Indicates that a request parameter does not comply with the associated contraints |

#### Example

#### Sample Request 1
```
Request URL: https://developer-api.govee.com/v1/devices/control
Request Method: PUT
Request Header:
Content-Type : application/json
Govee-API-Key : 00000000-729c-4b82-b536-000000000
Request body
{
    "device": "34:20:03:15:82:ae",
    "model": "H6089",
    "cmd": {
    "name": "turn",
    "value": "on"
 }
```

#### Sample Request 2
```
Request URL: https://developer-api.govee.com/v1/devices/control
Request Method: PUT
Request Header:
Content-Type : application/json
Govee-API-Key : 00000000-729c-4b82-b536-000000000
Request body
{
    "device": "34:20:03:15:82:ae",
    "model": "H6089",
    "cmd": {
    "name": "brightness",
    "value": 50
 }
```

#### Sample Request 3
```
Request URL: https://developer-api.govee.com/v1/devices/control
Request Method: PUT
Request Header:
Content-Type : application/json
Govee-API-Key : 00000000-729c-4b82-b536-000000000
Request body:
{
    "device": "34:20:03:15:82:ae",
    "model": "H6089",
    "cmd": {
    "name": "color",
    "value": {
        "r": 255,
        "g": 255,
        "b": 255
    }
 }
```

#### Sample Request 4
```
Request URL: https://developer-api.govee.com/v1/devices/control
Request Method: PUT
Request Header:
Content-Type : application/json
Govee-API-Key : 00000000-729c-4b82-b536-000000000
Request body
{
 "device": "34:20:03:15:82:ae",
 "model": "H6089",
 "cmd": {
    "name": "colorTem",
    “value": 7000
 }
```

#### Sample Response
```javascript
{
    "code": 200,
    "message": "Success",
    "data": {}
}
```

## DeviceState <a id="devicestate">
Query the device state.

#### **GET /v1/devices/state**

### Request Headers
```
Content-Type: application/json
Govee-API-Key: {your_Govee_API_KEY}
```

### Request Query parameters
| Parameter | Type | Required | Description |
| --------- | ---- | -------- | ----------- |
| device | `string` | Yes | The Mac address of the device |
| model | `string` | Yes | The Model of the device |

### Response Body parameters
`Content-Type: application/json`
| Parameter | Type | Required | Description |
| --------- | ---- | -------- | ----------- |
| code | `string` | Yes | The code value will be 200 when the request process successfully |
| message | `string` | Yes | The message value will be "success" when the request process successfully or it will be the error message. |
| data | `object` | Yes | The response data |
| data.model | `string` | Yes | The Model of the device |
| data.device | `string` | Yes | The Mac address of the device |
| data.name | `string` | Yes | Your device name |
| data.properties | `array` | Yes | Device States |

Demo: <br>
```javascript
[
    {
        "online": "false"
    },
    {
        "powerState" : "off"
    },
    {
    "brightness" : 60
    },
    {
        "color": {
            "r": 255, 
            "g" : 255,
            "b", 255
        }
    },
    {
        "colorTem": 5000
    },
]
```
**Note**: "`online`" is implemented through the cache. Sometimes it may
return wrong state. We suggest the third-party developers to ensure that
even if online returns "false", the users are allowed to send control
commands, then even if there the cache is wrong, the users can still control
the device.

#### Errors
| Error | HTTP Status Code | Description |
| ----- | ---------------- | ----------- |
| AuthorizationError | <center>`403`</center> | Indicates that the Govee API key is invalid. |
| InternalError | <center>`500`</center> | Indicates an internal service error. | 
| InvalidParameter | <center>`400`</center> | Indicates that a request parameter does not comply with the associated contraints |

#### Example

#### Sample Request
```
Request URL: https://developer-api.govee.com/v1/devices/state?
device=06%3A7A%3AA4%3AC1%3A38%3A5A%3A2A%3A8D&model=H61
48
Request Method: GET
Request Header:
Govee-API-Key : 00000000-729c-4b82-b536-000000000
Request Query Parameters:
device: 06:7A:A4:C1:38:5A:2A:8D
model: H6148
```

#### Sample Response
```javascript
{
 "data": {
    "device": "C6:EA:B8:56:C8:C6:89:BE",
    "model": "H6188",
    "properties": [
        {
        "online": "false"
        },
        {
        "powerState": "off"
        },
        {
        "brightness": 82
        },
        {
        "color": {
            "r": 0,
            "b": 0,
            "g": 0
        }
        }
    ]
 },
 "message": "Success",
 "code": 200
}
```

# F&Q <a id="fandq">
Q: I have a Bluetooth device. It does not support Wi-Fi. Can I use the API with this device?<br>
A: No, only Wi-Fi devices support the API<br><br>
Q: When I use Govee Home App to control the brightness of the device, the
API will not display the changed status.<br>
A: The changed status can only be synchronized when the lights is
controlled via Wi-Fi, which means your phone Bluetooth needs to be turn off.
Please recheck if you have turned off your phone Bluetooth.

# Version History <a id="versionhistory">
## Version 1.3
1, Support H7050, H6051, H6056, H6061, H6058, H6073, H6076, H619A, H619C, H618A,
H618C, H6008, H6071, H6075 , H614A, H614B, H614E, H618E, H619E

## Version 1.2
1, Support H6050, H6154, H6143, H6144, H6072, H6121, H611A, H5080, H6062, H614C,
H615A, H615B, H7020, H7021, H614D, H611Z, H611B, H611C, H615C, H615D, H7006,
H7007, H7008, H7012, H7013<br>
2, The change of DeviceState:<br>
When `cmd.name` is "colorTem":<br>
the valid values are returned by DeviceList, there will be one more field named
“properties”<br>
```javascript
"properties": {
    "colorTem": {
        "range": {
            "min": 2000,
            "max": 9000
        }
    }
}
```