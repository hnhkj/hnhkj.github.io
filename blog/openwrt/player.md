# player

## player source code

copy from chinese book `Linux high level project design`(second)

## player content

player use alsa sound card driver.

## note

#### need to install alsa-libs.

from link <http://www.alsa-project.org/main/index.php/Main_Page> to download specation versino lib.

you need to check your pc's alsa driver version.

```
$ aplay --version
aplay: version 1.1.0 by Jaroslav Kysela <perex@perex.cz>
```
download `alsa-lib-1.1.0.tar.bz2` from ftp://ftp.alsa-project.org.

```
$ tar xvf alsa-lib-1.1.0.tar.bz2
$ cd alsa-lib-1.1.0
$ ./configure
$ make
$ make install
```

#### compile player

compile player need to link alsa-libs. add `-lasound` end of `gcc`.
```
$ gcc -o player main.c -lasound
``` 

#### eclipse c/c+=

this IDE can compile player code. we need to add player source to compiler environment.
