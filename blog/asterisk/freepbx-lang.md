# freepbx中文包 

```
缺省安装的FreePbx，虽然带有汉语语言包，但是在语言选项中并没有中文选项，需要动手添加。
1.首先找到 /var/www/html/admin/views/freepbx_admin.php文件。  
2.用vi等文本编辑工具打开上面的php文件，找到lang这个下拉框的代码，添加如下代码：
	<option value="zh_CN" <?php echo ($_COOKIE['lang']=="zh_CN" ? "selected" : "")?> >Chinese</option>  
```