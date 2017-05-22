# automake

##

* 運行`autoscan`命令
* 將`configure.scan`文件重命名爲`configure.in`，並修改`configure.in`文件
* 在`project`目錄下新建`Makefile.am`文件，並在`core`和`shell`目錄下也新建`Makefile.am`文件
* 在`project`目錄下新建`NEWS` `README` `ChangeLog` `AUTHORS`文件
* 將`/usr/share/automake-1.x/`目錄下的`depcomp`和`compile`文件拷貝到本目錄下

```
$ aclocal
$ autoconf
$ automake -a
$ ./configure
$ make
```

