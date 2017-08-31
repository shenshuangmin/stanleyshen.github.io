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