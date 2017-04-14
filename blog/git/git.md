# git


## git merge

```
$ git checkout my_branch
$ git merge some_branch
```

#### 压制提交 --squash

将丢弃分支`some_branch`的历史纪录

```
$ git checkout my_branch
$ git merge some_branch --squash
```


## git pull


## git submodule

#### add submodule

```
$ git submodule add https://github.com/hnhkj/openwrt.git
```

#### add submodel branch

```
$ git submodule add https://github.com/hnhkj/gogoo.git -b master
```

#### clone submodule

```
$ git submodule init
$ git submodule update
```

#### remove submodule

```
$ mv asubmodule asubmodule_tmp
$ git submodule deinit asubmodule
$ git rm asubmodule
# Note: asubmodule (no trailing slash)
# or, if you want to leave it in your working tree
$ git rm --cached asubmodule
$ mv asubmodule_tmp asubmodule
```

maybe need follow

```
$ rm -rf .git/modules/asubmodule
```

