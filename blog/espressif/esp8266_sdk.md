# ESP8266开发资料

## ESP32开发工具主要步骤


#### xtensa-lx106-elf

下载xtensa-lx106-elf，并且解压到~/esp/目录下；

```
cd ~/esp/
tar -xzf xtensa-lx106-elf-linux64-1.22.0-88-gde0bdc1-4.8.5.tar.gz
```

#### ESP8266_RTOS_SDK

下载ESP8266_RTOS_SDK到~/esp/目录下；

```
cd ~/esp
git clone https://github.com/espressif/ESP8266_RTOS_SDK.git
```

#### ~/.profile

在~/.profile文件中配置xtensa-esp32-elf和esp-idf文件到PATH和IDF_PATH


```
export PATH="$HOME/esp/xtensa-lx106-elf/bin:$PATH"
export IDF_PATH="$HOME/esp/ESP8266_RTOS_SDK:$IDF_PATH"
```


#### Arduino

<https://github.com/esp8266/Arduino.git>

## 网站

<https://github.com/esp8266>

<https://github.com/espressif>

<https://github.com/espressifapp>
