# MAC

## change terminal prefix

```bash
export PS1='#'
```

## change colorful output for ls

```bash
alias ls='ls -G'
```

## install shadowsocks proxy

```bash
brew install shadowsocks-libev
```

Configure shadowsocks `shadowsocks-libev.json` like

```json
{
    "server":"xxx.xxx.xxx.xxx",
    "server_port":1111,
    "local_port":1111,
    "password":"xxxxx",
    "timeout":600,
    "method":"aes-256-cfbX"
}
```

`Wi-Fi` name might be different on your MAC.

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

## no password for sudo

```bash
whoami => find you

sudo visudo

root            ALL = (ALL) ALL
%admin          ALL = (ALL) ALL
user_name       ALL = (ALL) NOPASSWD:ALL

Note: Those are tabs after the username and group name.
```

## too many open files

We might see such error on MAC if we open something which might need open quite lot files.

```bash
Too many open files
```

You can check limit with **launchctl limit** and we can see maxfiles is quite smaller and not enough for using. With some search, one solution can be below:

`Create plist file`

```bash
sudo vim /Library/LaunchDaemons/limit.maxfiles.plist
```

`Contents`

```xml
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

```bash
-rw-r--r--  1 root  wheel   536B Sep 21 20:55 limit.maxfiles.plist
```

You need to reboot your MAC.