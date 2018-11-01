# ESP32


## ESP32开发工具主要步骤


#### xtensa-esp32-elf

下载xtensa-esp32-elf，并且解压到~/esp/目录下；

```
cd ~/esp/
tar -xzf xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz
```

#### esp-idf

下载esp-idf到~/esp/目录下；

```
cd ~/esp
git clone https://github.com/espressif/esp-idf.git
```

#### ~/.profile

在~/.profile文件中配置xtensa-esp32-elf和esp-idf文件到PATH和IDF_PATH


```
export PATH="$HOME/esp/xtensa-esp32-elf/bin:$PATH"
export IDF_PATH="$HOME/esp/esp-idf:$IDF_PATH"
```
