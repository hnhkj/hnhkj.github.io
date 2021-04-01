# Shell


## sed

```
sed "/^$/d"
sed -i "/^$/d" log.txt
```

* 删除开头是`;`的行

```
sed -i "/^;/d" log.txt
```
