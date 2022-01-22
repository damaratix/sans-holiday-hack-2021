(prech10)=
IMDS Exploration
=======================
Location: Frost Tower  (Door defrosting)

Well ... this time we land directly in the "executive restroom" where the name of the Troll doesn't really seem a joke ... !
Anyway, in order to obtain some further hints for the next SSRF challenge we have to play a little bit with the Cranberry
in this beatiful location. 


Well, .... Ok. It's the time to learn something about the IMDS 
you can find some useful info at <br/> [(https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)
```{note}
The instance metadata service (IMDS) is an on-instance component that code on the instance uses to securely access instance metadata 

The examples in this section use the IPv4 address of the instance metadata service: 169.254.169.254.
```
```{warning}
Although you can only access instance metadata and user data from within the instance itself, the data is not protected by authentication or cryptographic methods. Anyone who has direct access to the instance, and potentially any software running on the instance, can view its metadata. Therefore, you should not store sensitive data, such as passwords or long-lived encryption keys, as user data. 
```

This cranberry was not a challenge, it was a more didattic exploring the IMDS API to have an idea about we could do in the next challenge. 
We choose to print every instruction because it could be useful as short recap in the future. 


## Short recap
```
$ curl http://169.254.169.254
$ curl http://169.254.169.254/latest
$ curl http://169.254.169.254/latest/dynamic
$ curl http://169.254.169.254/latest/dynamic/instance-identity/document | jq
$ curl http://169.254.169.254/latest/meta-data
$ curl http://169.254.169.254/latest/meta-data/public-hostname ; echo 
$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials; echo
$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials/elfu-deploy-role; echo
$ cat gettoken.sh 
$ source gettoken.sh 
$ echo $TOKEN
$ curl -H "X-aws-ec2-metadata-token: $TOKEN"  http://169.254.169.254/latest/meta-data/placement/region; echo
```



## IMDS1
1- The Instance Metadata Service (IMDS) is a virtual server for cloud assets at the IP address 169.254.169.254. Send a couple ping packets to the server.
```
$ ping -c2 169.254.169.254
```

2- Developers can automate actions using IMDS. We'll interact with the server using the cURL tool. Run 'curl http://169.254.169.254' to access IMDS data.
```
curl http://169.254.169.254
$ latest
```

### latest
3- Different providers will have different formats for IMDS data. We're using an AWS-Compatible IMDS server that returns 'latest' as the default response. Access the 'latest' endpoint.
Run 'curl http://169.254.169.254/latest'

```
$ curl http://169.254.169.254/latest
dynamic
meta-data
```

### dynamic
4- IMDS returns two new endpoints: dynamic and meta-data. Let's start with the dynamic endpoint, which provides information about the instance itself. Repeat the request
to access the dynamic endpoint: 'curl http://169.254.169.254/latest/dynamic'.

```
$ curl http://169.254.169.254/latest/dynamic
fws/instance-monitoring
instance-identity/document
instance-identity/pkcs7
instance-identity/signature
```

5- The instance identity document can be used by developers to understand the instance details. Repeat the request, this time requesting the instance-identity/document resource:
'curl http://169.254.169.254/latest/dynamic/instance-identity/document'
 
```
curl http://169.254.169.254/latest/dynamic/instance-identity/document{
        "accountId": "PCRVQVHN4S0L4V2TE",
        "imageId": "ami-0b69ea66ff7391e80",
        "availabilityZone": "np-north-1f",
        "ramdiskId": null,
        "kernelId": null,
        "devpayProductCodes": null,
        "marketplaceProductCodes": null,
        "version": "2017-09-30",
        "privateIp": "10.0.7.10",
        "billingProducts": null,
        "instanceId": "i-1234567890abcdef0",
        "pendingTime": "2021-12-01T07:02:24Z",
        "architecture": "x86_64",
        "instanceType": "m4.xlarge",
        "region": "np-north-1"
```

6- Much of the data retrieved from IMDS will be returned in JavaScript Object Notation (JSON) format. Piping the output to 'jq' will make the content easier to read.
Re-run the previous command, sending the output to JQ: 'curl http://169.254.169.254/latest/dynamic/instance-identity/document | jq'

```
curl http://169.254.169.254/latest/dynamic/instance-identity/document | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   451  100   451    0     0   440k      0 --:--:-- --:--:-- --:--:--  440k
{
  "accountId": "PCRVQVHN4S0L4V2TE",
  "imageId": "ami-0b69ea66ff7391e80",
  "availabilityZone": "np-north-1f",
  "ramdiskId": null,
  "kernelId": null,
  "devpayProductCodes": null,
  "marketplaceProductCodes": null,
  "version": "2017-09-30",
  "privateIp": "10.0.7.10",
  "billingProducts": null,
  "instanceId": "i-1234567890abcdef0",
  "pendingTime": "2021-12-01T07:02:24Z",
  "architecture": "x86_64",
  "instanceType": "m4.xlarge",
  "region": "np-north-1"
}
```

### meta-data
7- In addition to dynamic parameters set at launch, IMDS offers metadata about the instance as well. Examine the metadata elements available:
'curl http://169.254.169.254/latest/meta-data'

```
$ curl http://169.254.169.254/latest/meta-data
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/ami
block-device-mapping/ebs0
block-device-mapping/ephemeral0
block-device-mapping/root
block-device-mapping/swap
elastic-inference/associations
elastic-inference/associations/eia-bfa21c7904f64a82a21b9f4540169ce1
events/maintenance/scheduled
events/recommendations/rebalance
hostname
iam/info
iam/security-credentials
iam/security-credentials/elfu-deploy-role
instance-action
instance-id
(cutted here ...)
```

8- By accessing the metadata elements, a developer can interrogate information about the
system. Take a look at the public-hostname element:
'curl http://169.254.169.254/latest/meta-data/public-hostname'

```
$ curl http://169.254.169.254/latest/meta-data/public-hostname
Ec2-192-0-2-54.compute-1.amazonaws.comelfu@68583ae3961f:~$ 
```

9- Many of the data elements returned won't include a trailing newline, which causes the
response to blend into the prompt. Re-run the prior command, adding '; echo' to the end of
the command. This will add a new line character to the response.

```
$ curl http://169.254.169.254/latest/meta-data/public-hostname; echo
ec2-192-0-2-54.compute-1.amazonaws.com
elfu@68583ae3961f:~$ 
```
### meta-data security credentials
10- There is a whole lot of information that can be retrieved from the IMDS server. Even AWS
Identity and Access Management (IAM) credentials! Request the endpoint
'http://169.254.169.254/latest/meta-data/iam/security-credentials' to see the instance IAM
role.

```
$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials; echo 
elfu-deploy-role
```

11- Once you know the role name, you can request the AWS keys associated with the role. Request
the endpoint 'http://169.254.169.254/latest/meta-data/iam/security-credentials/elfu-deploy-
role' to get the instance AWS keys.

```
$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials/elfu-deploy-role; echo
{
        "Code": "Success",
        "LastUpdated": "2021-12-02T18:50:40Z",
        "Type": "AWS-HMAC",
        "AccessKeyId": "AKIA5HMBSK1SYXYTOXX6",
        "SecretAccessKey": "CGgQcSdERePvGgr058r3PObPq3+0CfraKcsLREpX",
        "Token": "NR9Sz/7fzxwIgv7URgHRAckJK0JKbXoNBcy032XeVPqP8/tWiR/KVSdK8FTPfZWbxQ==",
        "Expiration": "2026-12-02T18:50:40Z"
}
```

12- So far, we've been interacting with the IMDS server using IMDSv1, which does not require
authentication. Optionally, AWS users can turn on IMDSv2 that requires authentication. This
is more secure, but not on by default.

## IMDS2
13- For IMDSv2 access, you must request a token from the IMDS server using the
X-aws-ec2-metadata-token-ttl-seconds header to indicate how long you want the token to be
used for (between 1 and 21,600 secods).
Examine the contents of the 'gettoken.sh' script in the current directory using 'cat'.

### get token
```
$ cat gettoken.sh 
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`
```

14- This script will retrieve a token from the IMDS server and save it in the environment
variable TOKEN. Import it into your environment by running 'source gettoken.sh'.

```
$ source gettoken.sh 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    44  100    44    0     0  44000      0 --:--:-- --:--:-- --:--:-- 44000
```

15- Now, the IMDS token value is stored in the environment variable TOKEN. Examine the contents
of the token by running 'echo $TOKEN'.

```
$ echo $TOKEN
Uv38ByGCZU8WP18PmmIdcpVmx00QA3xNe7sEB9Hixkk=
```

### use token
16- With the IMDS token, you can make an IMDSv2 request by adding the X-aws-ec2-metadata-token
header to the curl request. Access the metadata region information in an
IMDSv2 request: 'curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/region'

```
$ curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/region; echo 
np-north-1
```

Done!



