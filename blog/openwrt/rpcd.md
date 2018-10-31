
---
title: "OpenWrt - rpcd"
author: "hnhkj@163.com"
date: "2016-6-16"
output: html_document
---

<https://wiki.openwrt.org/doc/techref/rpcd>


## rpcd (OpenWrt ubus RPC backend server)//Openwrt ubus RPC后端服务

The code is published under ISC and can be found via git at <git://git.openwrt.org/project/rpcd.git> or via http at <http://git.openwrt.org/?p=project/rpcd.git;a=summary>. It's included in OpenWrt since r37877.

代码是根据ISC发布，能够通过git在<git://git.openwrt.org/project/rpcd.git>,或通过http在<http at http://git.openwrt.org/?p=project/rpcd.git;a=summary>。它从r37877开始包含在了OpenWrt中。

## plugin executables //可执行插件

It is possible to expose shell script functionality over ubus by using rpcd plugin executables functionality. Executables stored in /usr/libexec/rpcd/ directory will be runned by rpcd. Lets look at the following example:

通过使用rpcd的可执行插件功能，可以通过ubus曝光shell描述符功能。可执行文件保存在`/usr/libexec/rpcd`目录下，rpcd将运行他么。让我们看下面的例子：

```
$ cat << EOF > /usr/libexec/rpcd/foo
#!/bin/sh

case "$1" in
    list)
        echo '{ "bar": { "arg1": true, "arg2": 32, "arg3": "str" }, "toto": { } }'
    ;;
    call)
        case "$2" in
            bar)
                # read the arguments
                read input;

                # optionally log the call
                logger -t "foo" "call" "$2" "$input"

                # return json object or an array
                echo '{ "hello": "world" }'
            ;;
            toto)
                # return json object or an array
                echo '[ "item1", "item2", "item3" ]'
            ;;
        esac
    ;;
esac
EOF
$ chmod +x /usr/libexec/rpcd/foo
```

This will create new ubus functions which then can be used (after restarting rpcd):

这将创建新的ubus函数，然后可以运行它们（重新启动rpcd）：

```
$ ubus list -v
...
'foo' @686f0592
    "bar":{"arg1":"Boolean","arg2":"Integer","arg3":"String"}
    "toto":{}
...
$ ubus call -S foo bar '{"arg1": true }'
{{"hello":"world"}}
$ ubus call -S foo toto
{["item1","item2","item3"]}
```

On startup rpcd will call all executables in /usr/libexec/rpcd/ with argv[1] set to "list". For a plugin, which responds with a valid list of methods and signatures, ubus method with appropriate arguments will be created. When a method provided by the plugin is about to be invoked, rpcd calls the binary with argv[1] set to "call" and argv[2] set to the invoked method name. The actual data is then sent by the ubus client via stdin.

rpcd启动将调用所有在`/usr/libexec/rpcd`下所有可执行程序，使用argv[1]设置一个'list'。对于一个插件，相应一个有效的方法列表和标签，ubus方法用一个创建的合适的参数。当一个插件提供的方法被调用，rpcd用二进制，用argv[1]设置"call"，argv[2]设置调用方法名。激活的数据被ubus客户机发送到stdin。

The method signature is a simple object containing key:value pairs. The argument type is inferred from the value. If the value is a string (regardless of the contents) it is registered as string, if the value is a bool true or false, its registered as bool, if the value is an integer, it is registered as either int8, int16, int32 or int64 depending on the value ("foo": 16 will be INT16, "foo": 64 will be INT64, "foo": 8 will be INT8 and everything else will be INT32).

方法标签是一个简单的对象，包含键：value pairs。参数类型由值推测。如果值是一个字符串（不理会内容），它的寄存器作为字符串，如果值是一个bool true/false，它的寄存器是bool，如果值是一个integer，它的寄存器既可以是int8,int16,int32，也可以是int64，依赖值（“foo”：16是INT16，“foo”：64是INT64，“foo”：8是INT8其它的是INT32）。

It is enough to issue '/etc/init.d/rpcd reload' to make it pick up new plugin executables, that way one does not lose active sessions.

执行`/etc/init.d/rpcd reload`可以开始执行新的可执行插件，这个方法不会丢掉激活的部分。