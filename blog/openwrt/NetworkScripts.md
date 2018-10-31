---
title: "OpenWrt - Network scripts"
author: "hnhkj@163.com"
date: "2016-6-12"
output: html_document
---

<https://wiki.openwrt.org/doc/devel/network-scripting>

## Network scripts

netifd can (probably) bring up a wired, static ip configuration without shell scripts. For everything else (PPPoE or 3G) it needs protocol handlers implemented as sets of shell functions.

#### Writing protocol handlers

Each protocol handler is a shell script in /lib/netifd/proto/. It is run (or maybe sourced) when netifd daemon starts. Changes made to the scripts do not take effect until netifd restarts. The name of the file usually matches option proto in /etc/config/network.

每一个协议处理器是/lib/netifd/proto/下的一个shell script。它可以在netifd deamon开始的时候运行。

To be able to access the network functions, the script needs to include the necessary shell scripts at the beginning:

```
#!/bin/sh

. /lib/functions.sh
. ../netifd-proto.sh
init_proto "$@"
}
```

Current working directory is /lib/netifd/proto/ when the handler is sourced.

At the end of the script a handler should register itself by calling add_protocol protocolname.

A handler should at least define 2 shell functions: `proto_protocolname_init_config` and `proto_protocolname_setup`.

#### init_config

The main purpose of this function is to let netifd know which parameters does this protocol have. These parameters then can be stored in /etc/config/network.

```
proto_protocolname_init_config() {
    proto_config_add_string "stringparam"
    proto_config_add_int "intparam"
    proto_config_add_boolean "boolparam"
    …
}
```

#### Setup

The setup procedure implements the actual protocol specifc configuration logic and interface bringup.

When called, one or two parameters are passed:

    1. Config, the UCI section name suitable for config_get calls
    2. Interface name (only if no_device=0)

```
proto_protocolname_setup() {
    local config="$1"
    # set up the interface
}
```

This function must be implemented by any protocol backend.

There's usually no need to call ifconfig iface up at the end of this function. If no_device=0, netifd won't even try to start our profile until the device is already up. It waits for operstate=up and carrier=1, then starts the profile.

If no_device=1, netifd will bring it up, when it receives a notification from us:

```
proto_init_update "$iface" 1
proto_send_update "$config"
}
```

However, this only works once. If someone called ifconfig iface down, netifd won't try to bring it up again (at least in BB), so just in case you may put the "up" command in the function.

#### Teardown

Protocols that need special shutdown handling, for example to kill associated daemons, may implement a stop procedure. This procedure is called when an interface is brought down before the associated UCI state is cleared.

The function is called when we do ifdown profile or when no_device=0 and netifd detects link connectivity loss.

When called, two parameters are passed:

    1. Config, the UCI section name suitable for config_get calls
    2. iface, the network device

```
proto_protocolname_teardown() {
    local config="$1"
    local iface="$2"
    # tear down the interface
}
```

This function is optional.

#### Coldplug

By default, only interfaces present in /proc/net/dev, like eth0, are brought up on boot. Protocols which use virtual interfaces must set two variables at the beginning of init_config.

```
proto_protocolname_init_config() {
    no_device=1
    available=1
    …
}
```

## API

**netifd functions**

**initialization functions**

add_protocol

init_proto

proto_config_add_boolean

proto_config_add_int

proto_config_add_string

**notification functions**

Note: some of these function exit immediately.

proto_block_restart

proto_init_update

proto_kill_command

proto_notify_error

proto_run_command

We can start a daemon that maintains the connection asynchronously by calling proto_run_command "$config" custom_script. Netifd remembers its pid. It can be killed manually by calling proto_kill_command "$config". Netifd can automatically kill it, when the profile stopped.

proto_set_available

proto_send_update

proto_set_available

**common functions**

json_add_string

json_dump

json_init

json_get_var

json_get_vars