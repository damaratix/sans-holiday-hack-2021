(prech8)=
HoHo… No (Fail2Ban)
=======================

Location: Santa's Office

![cranberry](images/Fail2Ban.png)

First of all, we have to filter out the “good actions”:
```
$ cat /var/log/hohono.log | grep -ivE "success|Valid" > badactions
```

We extract all the "bad" action strings in badactions.log, and they are just 4: 
```
Login from <HOST> rejected due to unknown user name
Failed login from <HOST> for ...
<HOST> sent a malformed request
Invalid heartbeat '...' from <HOST>
```

Now, we have 3 configuration files to play with. First we setup the new rule:
```
/etc/fail2ban/jail.d/my-jail.conf
[my-jail]
enabled = true
logpath = /var/log/hohono.log
findtime = 60m
maxretry = 10
bantime = 30m
filter = my-filter
action = my-action
```

Then we define **when** it must be triggered because of the matching with the string errors we considered as "bad" in our logs
```
/etc/fail2ban/filter.d/my-filter.conf
[Definition]
failregex = [Ll]ogin from <HOST> rejected due to unknown user name
            [Ff]ailed login from <HOST> for (.+)
            <HOST> sent a malformed request
            Invalid heartbeat '(.+)' from <HOST>
```

Finally, once triggered **what** we want to do
```
/etc/fail2ban/action.d/my-action.conf
[Definition]
actionban =  /root/naughtylist add <ip>
actionunban =  /root/naughtylist del <ip>
```

Done!

```
#service fail2ban restart
#/root/naughty refresh
#/root/naughty list 
```
