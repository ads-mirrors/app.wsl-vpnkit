# WSL-VPNKit Workaround for Script Execution Issues

This repository is a fork of [sakai135/wsl-vpnkit](https://github.com/sakai135/wsl-vpnkit). No enhancements were made to the code itself; however, this guide provides a workaround for situations where `wsl-vpnkit` may stop functioning.

## Symptoms

When you run `wsl-vpnkit` as instructed but find that it gets **stuck indefinitely**, this article offers a potential workaround to help resolve the issue.

```bash
+ VPNKIT_GATEWAY_IP=192.168.127.1
+ VPNKIT_HOST_IP=192.168.127.254
+ VPNKIT_LOCAL_IP=192.168.127.2
+ TAP_MAC_ADDR=xx:xx:xx:xx:xx:xx
+ VMEXEC_PATH=/app/wsl-vm
+ GVPROXY_PATH=/app/wsl-gvproxy.exe
+ TAP_NAME=wsltap
+ CHECK_HOST=example.com
+ CHECK_DNS=1.1.1.1
+ DEBUG=0
+ set +x
+ WSL2_TAP_NAME=eth0
+ WSL2_GATEWAY_IP=172.26.112.1
+ '[' 0 -eq 0 ]
+ set +x
```


## Cause and Solution Overview

**Cause:** Windows may block the execution of `/app/wsl-gvproxy.exe` for various reasons.

**Solution:** Ensure that the current user has permission to execute this file by moving it to a more accessible location and updating the configuration.

## Workaround Steps

1. **Copy `wsl-gvproxy.exe` to your User Profile**

Move the `wsl-gvproxy.exe` file to your user profile, typically located at `C:\Users\[yourusername]\`.

You can find the file from wsl-vpnkit distro under `/app` folder

2. **Enable Auto-Mount in Your WSL Distribution**
   
To allow wsl-vpnkit to access Windows files, ensure auto-mounting is enabled in the WSL distribution where you are running wsl-vpnkit.

Edit `/etc/wsl.conf`:

```bash
[automount]
enabled=true
```

3. **Set a New Environment Variable for `GVPROXY_PATH`**

Update the `GVPROXY_PATH` environment variable so that wsl-vpnkit can locate and run the wsl-gvproxy.exe file.

Edit `/etc/profile`:

```bash
export GVPROXY_PATH=/mnt/c/Users/[yourusername]/wsl-gvproxy.exe
```
4. **Restart WSL**

Once you've completed the steps above, restart WSL to apply the changes:

```bash
wsl.exe --shutdown
```
5. **Run `wsl-vpnkit`**

Now, when you run `wsl-vpnkit`. And seeing this, you are all set.
```bash
+ VPNKIT_GATEWAY_IP=192.168.127.1
+ VPNKIT_HOST_IP=192.168.127.254
+ VPNKIT_LOCAL_IP=192.168.127.2
+ TAP_MAC_ADDR=xx:xx:xx:xx:xx:xx
+ VMEXEC_PATH=/app/wsl-vm
+ GVPROXY_PATH=/mnt/c/Users/[yourusername]/wsl-gvproxy.exe
+ TAP_NAME=wsltap
+ CHECK_HOST=example.com
+ CHECK_DNS=1.1.1.1
+ DEBUG=0
+ set +x
+ WSL2_TAP_NAME=eth0
+ WSL2_GATEWAY_IP=172.26.112.1
+ '[' 0 -eq 0 ]
+ set +x
starting vm and gvproxy...
INFO[0000] waiting for packets...
time="2024-10-01T10:01:12-06:00" level=info msg="waiting for clients..."
time="2024-10-01T10:01:12-06:00" level=info msg="new connection from remote to 17944"
started vm and gvproxy
check: ✔️ ping success to IPv4 WSL 2 gateway / Windows host (172.26.112.1)
check: ✔️ ping success to IPv4 Windows host (192.168.127.254)
check: ✔️ ping success to IPv4 gateway (192.168.127.1)
check: ✔️ nslookup success for example.com A using 192.168.127.1
check: ✔️ nslookup success for example.com A using 172.26.112.1
check: ❌ nslookup fail for example.com A using 1.1.1.1
check: ✔️ ping success to IPv4 external host domain (example.com)
check: ✔️ ping success to IPv4 external host IP (1.1.1.1)
check: ✔️ nslookup success for example.com AAAA using 192.168.127.1
check: ✔️ nslookup success for example.com AAAA using 172.26.112.1
check: ❌ nslookup fail for example.com AAAA using 1.1.1.1
ping: bad address 'example.com'
check: ➖ ping fail to IPv6 external host (example.com)
check: ✔️ wget success for http://example.com
check: ✔️ wget success for https://example.com
```

---

## Other notes:

If you're running Windows 11 version 22H2 or later, you can resolve all network-related issues in WSL2 by switching its network mode to `mirrored`. This adjustment eliminates the need for wsl-vpnkit for network compatibility.

To apply this change, edit your `.wslconfig` file and add the following setting:

```bash
[wsl2]
networkingMode=mirrored
```
---

## Happy Coding!

I hope this guide helps you resolve some issues with `wsl-vpnkit`. Happy coding!


