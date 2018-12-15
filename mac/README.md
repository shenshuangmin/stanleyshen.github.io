# change terminate prefix
```
export PS1='#'
```

# change colorful output for ls
```
alias ls='ls -G'
```



# install proxy
```
brew install shadowsocks-libev
```

Configure shadowsocks `shadowsocks-libev.json` like 

```
{
    "server":"xxx.xxx.xxx.xxx",
    "server_port":xxxx,
    "local_port":10800,  => in my case it's 10800
    "password":"xxxxx",
    "timeout":600,
    "method":"aes-256-cfb". => method like aes-256-cfb in my case, depends on yours
}
```

```bash
function proxy_start
{
  brew services start shadowsocks-libev
  sudo networksetup -setsocksfirewallproxy "Wi-Fi" 127.0.0.1 10800
}

function proxy_stop
{
  brew services stop shadowsocks-libev
  sudo networksetup -setsocksfirewallproxystate "Wi-Fi" off
}

```

# no password for sudo
```
whoami => find you

sudo visudo

root            ALL = (ALL) ALL
%admin          ALL = (ALL) ALL
user_name       ALL = (ALL) NOPASSWD:ALL

Note: Those are tabs after the username and group name. 
```

# too many open files
When starting my eclipse to launch our project, which need quite lot threads to handle many files.
And it keeps failing for error message 

```
Too many open files
```

You can check limit with **launchctl limit** and we can see maxfiles is quite smaller and not enough for using. With some search, one solution can be below:


**Create plist file**

```
sudo vim /Library/LaunchDaemons/limit.maxfiles.plist
```

**Contents**

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>limit.maxfiles</string>
    <key>ProgramArguments</key>
    <array>
      <string>launchctl</string>
      <string>limit</string>
      <string>maxfiles</string>
      <string>200000</string>
      <string>200000</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>ServiceIPC</key>
    <false/>
  </dict>
</plist>
```

And make sure its mode and owner, group

```
-rw-r--r--  1 root  wheel   536B Sep 21 20:55 limit.maxfiles.plist
```

With these change, it works fine for me after a system reboot.
