# BASH 字符串

## 字符串大写

```bash
$ lowerCase="hello"
$ echo ${lowerCase} 
hello

$ echo ${lowerCase^^}
HELLO
```

## 字符串作为变量名

```bash
$ hello="variable hello"
$ variableName="hello"

$ echo ${hello}
variable hello

$ echo ${variableName}
hello

$ echo ${!variableName}
variable hello
```
