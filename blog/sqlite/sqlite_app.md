# sqlite應用

---
title: "sqlite應用"
author: "hnhkj@163.com"
date: "2017-3-24"
output: html_document
---

## 數據庫管理

#### 創建數據庫

```
sqlite3 test.db
```

## 創建數據庫

* create table  //　創建表
* .table        //　查詢表
* alter table   // 修改表
* drop table 　　　// 刪除表

#### 創建表


* `create [temp] table table_name (column_definitions [, constraints]);`
    *　`temp` - 臨時表，這種表是臨時的——只存活與當前繪畫。一旦鏈接斷開，就會被自動銷毀。
　　　　*　`table_name` - 表名
    *　`column_definitions` - 列定義，列有５總定義，如下：l
        *　`text`
        *　`blob`
        *　`null`

```
$ sqlite3 test.db
SQLite version 3.11.0 2016-02-15 17:29:24
Enter ".help" for usage hints.
sqlite> create table contacts (id integer primary key,
   ...> name text not null collate nocase,
   ...> phone text not null default 'UNKNOWN',
   ...> unique (name,phone) );
sqlite> .schema contacts
CREATE TABLE contacts (id integer primary key,
name text not null collate nocase,
phone text not null default 'UNKNOWN',
unique (name,phone) );

```

說明：

1. `id`爲`integer`類型，限制爲主鍵。主鍵可以自動遞增；
2. `name`爲`text`類型，約束不能爲`null`，並且不區分大小寫；
3. `phone`爲`text`類型，約束不能爲`null`，默認爲`UNKNOWN`；

#### 查詢表

```
sqlite> .table
episodes        food_types      foods           foods2          foods_episodes
```
#### 修改表

* `alter table table { rename to name | add column column_def }`
    *`{}` - 表示必須從各選項中選擇一個

```
sqlite> alter table contacts
   ...> add column email text not null default '' collate nocase;
sqlite> .schema contacts
CREATE TABLE contacts (id integer primary key,
name text not null collate nocase,
phone text not null default 'UNKNOWN', email text not null default '' collate nocase,
unique (name,phone) );
sqlite> 

```

#### 刪除表

```
sqlite> drop table foods2;
```

## 數據庫查詢 

#### 關系操作

* 基本操作
　　　　* Restriction（限制）
　　　　* Projection （投影）
　　　　* Cartesian Product（卡迪兒積）
    * Union（聯合）
    * Difference（差）
　　　　* Rename（重命名）
* 附加操作
    * Intersection（交叉）
    * Natural Join（自然鏈接）
    * Assign（賦值）
* 擴展操作
　　　　* Generalized Projection（廣義投影）
　　　　* Left Outer Join（左外鏈接）
　　　　* Right Outer Join（右外鏈接）
　　　　* Full Outer Join（全外鏈接）

#### select命令與操作管道

####　過濾

* 值
* 操作符
　　　　* 二元操作符
　　　　* 邏輯操作符
　　　　* LIKE與GLOB操作符

**二元操作符**

```
sqlite> select 2=2;
2=2       
----------
1         
sqlite> select 2=1;
2=1       
----------
0         
sqlite> select 2<1;
2<1       
----------
0         
sqlite> select 2>1;
2>1       
----------
1         
```

**邏輯操作符**

```
sqlite> select * from foods where name='JujyFruit' and type_id=9;
id          type_id     name      
----------  ----------  ----------
244         9           JujyFruit
```

**LIKE操作符**

```
sqlite> select id, name from foods where name like 'J%';
id          name      
----------  ----------
156         Juice box 
236         Jucyfruit 
243         Jello with
244         JujyFruit 
245         Junior Min
370         Jambalaya

sqlite> select id, name from foods where name like '%ac%P%' and name not like '%Sch%';
id          name                
----------  --------------------
38          Pie (Blackberry) Pie
127         Guacamole Dip       
198         Macinaw peache

sqlite> select id, name from foods where name like '%ac%P%' and name not like '%Sch%';
id          name                
----------  --------------------
38          Pie (Blackberry) Pie
127         Guacamole Dip       
198         Macinaw peaches 
```

**GLOB操作符**

```
sqlite> select id, name from foods where name glob 'Pine*';
id          name      
----------  ----------
205         Pineapple 
258         Pineapple
```

* 限定和排序
    * limit
    * offset
    * order by


**limit,offset,orderby**

```
sqlite> select * from food_types order by id limit 2 offset 1;
select * from food_types order by id limit 2 offset 1;
id          name      
----------  ----------
2           Cereal    
3           Chicken/Fo

sqlite> select * from foods where name like 'B%'
   ...> order by type_id desc, name limit 10;
select * from foods where name like 'B%'
order by type_id desc, name limit 10;
id          type_id     name       
----------  ----------  -----------
382         15          Baked Beans
383         15          Baked Potat
384         15          Big Salad  
385         15          Brocolli   
362         14          Bouillabais
328         12          BLT        
327         12          Bacon Club 
326         12          Bologna    
329         12          Brisket San
274         10          Bacon 
```

#### 函數(Function)和聚合(Aggregate)

* Function//函數
　　　　* abs()　　　　//絕對值
    * upper()  //大寫
    * lower()　　//小寫
    * length()　//長度

**upper(),length(),length(),abs()函數應用**

```
sqlite> select upper('hello newman'), length('hello newman'), abs(-12);
select upper('hello newman'), length('hello newman'), abs(-12);
upper('hello newman')  length('hello newman')  abs(-12)  
---------------------  ----------------------  ----------
HELLO NEWMAN           12                      12  
```

* Aggregate//聚合
　　　　* sum()　　　　//和值
    * avg()    //平均值
    * count()  //數量
    * min()    //最小
    * max()    //最大

**count()**

```
sqlite> select count(*) from foods where type_id=1;
select count(*) from foods where type_id=1;
count(*)  
----------
47
```

**avg()**
```
sqlite> select avg(length(name)) from foods;
select avg(length(name)) from foods;
avg(length(name))
-----------------
12.5849514563107
```

* 分組(Grouping)
　　　　* group by
    * having　　　　
    * distinct - 過濾掉重復的行


**group by**

```
sqlite> select type_id from foods group by type_id;
type_id   
----------
1         
2         
3         
4         
5         
6         
7         
8         
9         
10        
11        
12        
13        
14        
15   

sqlite> select type_id, count(*) from foods group by type_id;
type_id     count(*)  
----------  ----------
1           47        
2           15        
3           23        
4           22        
5           17        
6           4         
7           60        
8           23        
9           61        
10          36        
11          16        
12          23        
13          14        
14          19        
15          32
```

**having**

```
sqlite> select type_id, count(*) from foods group by type_id having count(*)<20; 
type_id     count(*)  
----------  ----------
2           15        
5           17        
6           4         
11          16        
13          14        
14          19 
```

**distinct去掉重復**

```
sqlite> select distinct type_id from foods;
type_id   
----------
1         
2         
3         
4         
5         
6         
7         
8         
9         
10        
11        
12        
13        
14        
15
```

* 多表鏈接
　　　　* join
    * 交叉連接
    * 外連接
　　　　* 自然連接
　　　　* 語法偏好


**join**

```
sqlite> select foods.name, food_types.name
   ...> from foods, food_types
   ...> where foods.type_id=food_types.id limit 10;
name        name      
----------  ----------
Bagels      Bakery    
Bagels, ra  Bakery    
Bavarian C  Bakery    
Bear Claws  Bakery    
Black and   Bakery    
Bread (wit  Bakery    
Butterfing  Bakery    
Carrot Cak  Bakery    
Chips Ahoy  Bakery    
Chocolate   Bakery 
```


* 交叉鏈接

```
sqlite> select * from foods, food_types;
id          type_id     name        id          name      
----------  ----------  ----------  ----------  ----------
1           1           Bagels      1           Bakery    
1           1           Bagels      2           Cereal    
1           1           Bagels      3           Chicken/Fo
1           1           Bagels      4           Condiments
1           1           Bagels      5           Dairy     
1           1           Bagels      6           Dip       
1           1           Bagels      7           Drinks    
1           1           Bagels      8           Fruit     
1           1           Bagels      9           Junkfood  
1           1           Bagels      10          Meat      
1           1           Bagels      11          Rice/Pasta
1           1           Bagels      12          Sandwiches
1           1           Bagels      13          Seafood   
1           1           Bagels      14          Soup      
1           1           Bagels      15          Vegetables
2           1           Bagels, ra  1           Bakery    
2           1           Bagels, ra  2           Cereal    
2           1           Bagels, ra  3           Chicken/Fo
2           1           Bagels, ra  4           Condiments
2           1           Bagels, ra  5           Dairy     
2           1           Bagels, ra  6           Dip       
2           1           Bagels, ra  7           Drinks    
2           1           Bagels, ra  8           Fruit     
2           1           Bagels, ra  9           Junkfood  
2           1           Bagels, ra  10          Meat      
2           1           Bagels, ra  11          Rice/Pasta
2           1           Bagels, ra  12          Sandwiches
2           1           Bagels, ra  13          Seafood   
2           1           Bagels, ra  14          Soup      
2           1           Bagels, ra  15          Vegetables
3           1           Bavarian C  1           Bakery    
3           1           Bavarian C  2           Cereal    
3           1           Bavarian C  3           Chicken/Fo
3           1           Bavarian C  4           Condiments
3           1           Bavarian C  5           Dairy     
3           1           Bavarian C  6           Dip       
3           1           Bavarian C  7           Drinks    
3           1           Bavarian C  8           Fruit     
3           1           Bavarian C  9           Junkfood  
3           1           Bavarian C  10          Meat      
3           1           Bavarian C  11          Rice/Pasta
3           1           Bavarian C  12          Sandwiches

...
...

411         15          Veggie bur  12          Sandwiches
411         15          Veggie bur  13          Seafood   
411         15          Veggie bur  14          Soup      
411         15          Veggie bur  15          Vegetables
412         15          Wax Beans   1           Bakery    
412         15          Wax Beans   2           Cereal    
412         15          Wax Beans   3           Chicken/Fo
412         15          Wax Beans   4           Condiments
412         15          Wax Beans   5           Dairy     
412         15          Wax Beans   6           Dip       
412         15          Wax Beans   7           Drinks    
412         15          Wax Beans   8           Fruit     
412         15          Wax Beans   9           Junkfood  
412         15          Wax Beans   10          Meat      
412         15          Wax Beans   11          Rice/Pasta
412         15          Wax Beans   12          Sandwiches
412         15          Wax Beans   13          Seafood   
412         15          Wax Beans   14          Soup      
412         15          Wax Beans   15          Vegetables

```

* 外聯接

```
sqlite> select *
   ...> from foods left outer join foods_episodes on foods.id=foods_episodes.food_id;
id          type_id     name        food_id     episode_id
----------  ----------  ----------  ----------  ----------
1           1           Bagels      1           165       
2           1           Bagels, ra  2           154       
2           1           Bagels, ra  2           165       
3           1           Bavarian C  3           115       
4           1           Bear Claws  4           67        
4           1           Bear Claws  4           82        
4           1           Bear Claws  4           169       
5           1           Black and   5           76        
5           1           Black and   5           109       
6           1           Bread (wit  6           164       
7           1           Butterfing  7           66        
8           1           Carrot Cak  8           173       
9           1           Chips Ahoy  9           123       
10          1           Chocolate   10          76        
11          1           Chocolate   11          91        
12          1           Chocolate   12          78        
13          1           Cinnamon B  13          76        
14          1           Cinnamon S  14          66        
15          1           Cookie      15          27        
16          1           Crackers    16          136       
17          1           Cupcake     17          122       
18          1           Cupcakes    18          114       
19          1           Devils Foo  19          171       
20          1           Dinky Donu  20          18        
20          1           Dinky Donu  20          66        
21          1           Dog biscui  21          143       
22          1           Donuts      22          169       
23          1           Drakes Cof  23          29        
24          1           Entenmann'  24          173       
25          1           Kaiser Rol  25          162       
26          1           Marble Rye  26          120       
27          1           Mini Ritz   27          9         

...
...

397         15          Kreplach    397         139       
398         15          Latkas      398         139       
399         15          Lettuce (a  399         142       
400         15          Olives      400         80        
400         15          Olives      400         136       
401         15          Onion       401         66        
401         15          Onion       401         135       
402         15          Peas        402         6         
402         15          Peas        402         69        
403         15          Pea Pods    403         49        
403         15          Pea Pods    403         54        
404         15          Pickle      404         15        
405         15          Potato Sal  405         85        
405         15          Potato Sal  405         172       
406         15          Potatoes    406         162       
407         15          Radish Ros  407         164       
408         15          Roasted Po  408         14        
409         15          Salad       409         87        
409         15          Salad       409         92        
410         15          Tzimmis     410         139       
411         15          Veggie bur  411         61        
412         15          Wax Beans   412         161  

```

**語法偏好**

```
sqlite> select * from foods, food_types where foods.id=food_types.id;
id          type_id     name        id          name      
----------  ----------  ----------  ----------  ----------
1           1           Bagels      1           Bakery    
2           1           Bagels, ra  2           Cereal    
3           1           Bavarian C  3           Chicken/Fo
4           1           Bear Claws  4           Condiments
5           1           Black and   5           Dairy     
6           1           Bread (wit  6           Dip       
7           1           Butterfing  7           Drinks    
8           1           Carrot Cak  8           Fruit     
9           1           Chips Ahoy  9           Junkfood  
10          1           Chocolate   10          Meat      
11          1           Chocolate   11          Rice/Pasta
12          1           Chocolate   12          Sandwiches
13          1           Cinnamon B  13          Seafood   
14          1           Cinnamon S  14          Soup      
15          1           Cookie      15          Vegetables

sqlite> select * from foods inner join food_types on foods.id=food_types.id;
id          type_id     name        id          name      
----------  ----------  ----------  ----------  ----------
1           1           Bagels      1           Bakery    
2           1           Bagels, ra  2           Cereal    
3           1           Bavarian C  3           Chicken/Fo
4           1           Bear Claws  4           Condiments
5           1           Black and   5           Dairy     
6           1           Bread (wit  6           Dip       
7           1           Butterfing  7           Drinks    
8           1           Carrot Cak  8           Fruit     
9           1           Chips Ahoy  9           Junkfood  
10          1           Chocolate   10          Meat      
11          1           Chocolate   11          Rice/Pasta
12          1           Chocolate   12          Sandwiches
13          1           Cinnamon B  13          Seafood   
14          1           Cinnamon S  14          Soup      
15          1           Cookie      15          Vegetables

sqlite> select * from foods left outer join food_types on foods.id=food_types.id;
id          type_id     name        id          name      
----------  ----------  ----------  ----------  ----------
1           1           Bagels      1           Bakery    
2           1           Bagels, ra  2           Cereal    
3           1           Bavarian C  3           Chicken/Fo
4           1           Bear Claws  4           Condiments
5           1           Black and   5           Dairy     
6           1           Bread (wit  6           Dip       
7           1           Butterfing  7           Drinks    
8           1           Carrot Cak  8           Fruit     
9           1           Chips Ahoy  9           Junkfood  
10          1           Chocolate   10          Meat      
11          1           Chocolate   11          Rice/Pasta
12          1           Chocolate   12          Sandwiches
13          1           Cinnamon B  13          Seafood   
14          1           Cinnamon S  14          Soup      
15          1           Cookie      15          Vegetables
16          1           Crackers                          
17          1           Cupcake                           
18          1           Cupcakes                          
19          1           Devils Foo                        
20          1           Dinky Donu                        
21          1           Dog biscui                        
22          1           Donuts         

...
...

393         15          French Fri                        
394         15          Hampton To                        
395         15          Insalata G                        
396         15          Kugel                             
397         15          Kreplach                          
398         15          Latkas                            
399         15          Lettuce (a                        
400         15          Olives                            
401         15          Onion                             
402         15          Peas                              
403         15          Pea Pods                          
404         15          Pickle                            
405         15          Potato Sal                        
406         15          Potatoes                          
407         15          Radish Ros                        
408         15          Roasted Po                        
409         15          Salad                             
410         15          Tzimmis                           
411         15          Veggie bur                        
412         15          Wax Beans   


sqlite> select * from foods cross join food_types;
id          type_id     name        id          name      
----------  ----------  ----------  ----------  ----------
1           1           Bagels      1           Bakery    
1           1           Bagels      2           Cereal    
1           1           Bagels      3           Chicken/Fo
1           1           Bagels      4           Condiments
1           1           Bagels      5           Dairy     
1           1           Bagels      6           Dip       
1           1           Bagels      7           Drinks    
1           1           Bagels      8           Fruit     
1           1           Bagels      9           Junkfood  
1           1           Bagels      10          Meat      
1           1           Bagels      11          Rice/Pasta
1           1           Bagels      12          Sandwiches
1           1           Bagels      13          Seafood   
1           1           Bagels      14          Soup      
1           1           Bagels      15          Vegetables
2           1           Bagels, ra  1           Bakery    
2           1           Bagels, ra  2           Cereal    
2           1           Bagels, ra  3           Chicken/Fo
2           1           Bagels, ra  4           Condiments
2           1           Bagels, ra  5           Dairy     
2           1           Bagels, ra  6           Dip       
2           1           Bagels, ra  7           Drinks    
2           1           Bagels, ra  8           Fruit     
2           1           Bagels, ra  9           Junkfood  
2           1           Bagels, ra  10          Meat      
2           1           Bagels, ra  11          Rice/Pasta
2           1           Bagels, ra  12          Sandwiches
2           1           Bagels, ra  13          Seafood   
2           1           Bagels, ra  14          Soup      
2           1           Bagels, ra  15          Vegetables
3           1           Bavarian C  1           Bakery    
3           1           Bavarian C  2           Cereal    
3           1           Bavarian C  3           Chicken/Fo
3           1           Bavarian C  4           Condiments
3           1           Bavarian C  5           Dairy     
3           1           Bavarian C  6           Dip       
3           1           Bavarian C  7           Drinks    
3           1           Bavarian C  8           Fruit     
3           1           Bavarian C  9           Junkfood  
3           1           Bavarian C  10          Meat      
3           1           Bavarian C  11          Rice/Pasta
3           1           Bavarian C  12          Sandwiches
3           1           Bavarian C  13          Seafood   
3           1           Bavarian C  14          Soup      
3           1           Bavarian C  15          Vegetables
4           1           Bear Claws  1           Bakery    
4           1           Bear Claws  2           Cereal    
4           1           Bear Claws  3           Chicken/Fo
4           1           Bear Claws  4           Condiments
4           1           Bear Claws  5           Dairy     
4           1           Bear Claws  6           Dip       
4           1           Bear Claws  7           Drinks    
4           1           Bear Claws  8           Fruit     
4           1           Bear Claws  9           Junkfood  
4           1           Bear Claws  10          Meat      
4           1           Bear Claws  11          Rice/Pasta
4           1           Bear Claws  12          Sandwiches
4           1           Bear Claws  13          Seafood   
4           1           Bear Claws  14          Soup      
4           1           Bear Claws  15          Vegetables
5           1           Black and   1           Bakery    
5           1           Black and   2           Cereal    
5           1           Black and   3           Chicken/Fo
5           1           Black and   4           Condiments
5           1           Black and   5           Dairy     
5           1           Black and   6           Dip       
5           1           Black and   7           Drinks    
5           1           Black and   8           Fruit     
5           1           Black and   9           Junkfood  

...
...

411         15          Veggie bur  13          Seafood   
411         15          Veggie bur  14          Soup      
411         15          Veggie bur  15          Vegetables
412         15          Wax Beans   1           Bakery    
412         15          Wax Beans   2           Cereal    
412         15          Wax Beans   3           Chicken/Fo
412         15          Wax Beans   4           Condiments
412         15          Wax Beans   5           Dairy     
412         15          Wax Beans   6           Dip       
412         15          Wax Beans   7           Drinks    
412         15          Wax Beans   8           Fruit     
412         15          Wax Beans   9           Junkfood  
412         15          Wax Beans   10          Meat      
412         15          Wax Beans   11          Rice/Pasta
412         15          Wax Beans   12          Sandwiches
412         15          Wax Beans   13          Seafood   
412         15          Wax Beans   14          Soup      
412         15          Wax Beans   15          Vegetables

```

* 名稱和別名
* 子查詢
* 符合查詢
　　　　* union
    * intersect
    * except
* 條件結果

* 處理SQLite中的Null


#### SQLite中的高級SQL

* 修改數據
    * 插入記錄//insert
　　　　　　　　* 插入一行
　　　　　　　　* 插入一組行
        * 插入多行
    * 更新記錄//update
    * 刪除記錄//delete

**插入一行**

```
sqlite> insert into foods (name, type_id) values ('Chinnamon Bobka', 1);
sqlite> select * from foods where name='Chinnamon Bobka';
id          type_id     name           
----------  ----------  ---------------
413         1           Chinnamon Bobka

sqlite> insert into foods values(NULL, 1, 'Blueberry Bobka');
sqlite> select * from foods where name like '%Bobka';
id          type_id     name           
----------  ----------  ---------------
10          1           Chocolate Bobka
13          1           Cinnamon Bobka 
413         1           Chinnamon Bobka
414         1           Blueberry Bobka
```

**插入一組行**

```
sqlite> insert into foods
   ...> values(null,
   ...> (select id from food_types where name='Bakery'),
   ...> 'Blackberry Bobka');
sqlite> select * from foods where name like '%Bobka';
id          type_id     name           
----------  ----------  ---------------
10          1           Chocolate Bobka
13          1           Cinnamon Bobka 
413         1           Chinnamon Bobka
414         1           Blueberry Bobka
415         1           Blackberry Bobk

sqlite> insert into foods
   ...> select last_insert_rowid()+1, type_id, name from foods
   ...> where name='Chocolate Bobka';
sqlite> select * from foods where name like '%Bobka';
id          type_id     name           
----------  ----------  ---------------
10          1           Chocolate Bobka
13          1           Cinnamon Bobka 
413         1           Chinnamon Bobka
414         1           Blueberry Bobka
415         1           Blackberry Bobk
416         1           Chocolate Bobka

```

**插入多行**

```
sqlite> create table foods2(id int, type_id int, name text);
sqlite> insert into foods2 select * from foods;
sqlite> select count(*) from foods2;
count(*)  
----------
416  
```

```
sqlite> create table foods2 as select * from foods;
sqlite> select count(*) from foods2;
count(*)  
----------
416
```

**更新記錄**

```
sqlite> update foods set name='CHOCOLATE BOBKA'
   ...> where name='Chocolate Bobka';
sqlite> select * from foods where name like 'CHOCOLATE%';
id          type_id     name           
----------  ----------  ---------------
10          1           CHOCOLATE BOBKA
11          1           Chocolate Eclai
12          1           Chocolate Cream
222         9           Chocolates, box
223         9           Chocolate Chip 
224         9           Chocolate Cover
416         1           CHOCOLATE BOBKA

```

**刪除記錄**

```
sqlite> delete from foods where name='CHOCOLATE BOBKA';
sqlite> select * from foods where name like 'CHOCOLATE%';
id          type_id     name             
----------  ----------  -----------------
11          1           Chocolate Eclairs
12          1           Chocolate Cream P
222         9           Chocolates, box o
223         9           Chocolate Chip Mi
224         9           Chocolate Covere
```

* 數據完整性
　　　　* 域完整性
　　　　* 實體完整性
    * 引用完整性
    * 用戶定義完整性

* 外鍵約束
* 排序規則

#### 存儲類

* integer
* real
* text
* blob
* NULL


**typeof()**

```
sqlite> select typeof(3.14), typeof('3.14'),
   ...> typeof(314), typeof(x'3142'), typeof(NULL);
real|text|integer|blob|null
```

```
sqlite> create table domain(x);
sqlite> insert into domain values (3.142);
sqlite> insert into domain values ('3.142');
sqlite> insert into domain values (3142);
sqlite> insert into domain values (x'3142');
sqlite> insert into domain values (null);
sqlite> select rowid, x, typeof(x) from domain;
1|3.142|real
2|3.142|text
3|3142|integer
4|1B|blob
5||null
```

#### 視圖

視圖即虛擬表，也稱爲派生表，因爲他們的內容都派生自其他表的查詢結果。誰然視圖看起來就像基本表一樣，但是它不是基本表。基本表的內容是持久的，而視圖的內容是在使用時動態產生的。創建視圖的語法如下：

**創建視圖**

```
create view name as select-stmt;
```

**刪除視圖**

```
drop view name;
```


```
sqlite> select f.name, ft.name, e.name
   ...> from foods f
   ...> inner join food_types ft on f.type_id=ft.id
   ...> inner join foods_episodes fe on f.id=fe.food_id
   ...> inner join episodes e on fe.episode_id=e.id;
name        name        name      
----------  ----------  ----------
Bagels      Bakery      The Strike

```

```
sqlite> create view details as
   ...> select f.name as fd, ft.name as tp, e.name as ep, e.season as ssn
   ...> from foods f
   ...> inner join food_types ft on f.type_id=ft.id
   ...> inner join foods_episodes fe on f.id=fe.food_id
   ...> inner join episodes e on fe.episode_id=e.id;
sqlite> select fd as Food, ep as Episode
   ...> from details where ssn=7 and tp like 'Drinks';
Food         Episode             
-----------  --------------------
Apple Cider  The Bottle Deposit 1
Bosco        The Secret Code     
Cafe Latte   The Postponement    
Cafe Latte   The Maestro         
Champagne C  The Wig Master      
Cider        The Bottle Deposit 2
Hershey's    The Secret Code     
Hot Coffee   The Maestro         
Latte        The Maestro         
Mellow Yell  The Bottle Deposit 1
Merlot       The Rye             
Orange Juic  The Wink            
Tea          The Hot Tub         
Wild Turkey  The Hot Tub
```

**視圖的內容是動態生成的。因此，每次使用details時，基於數據庫的當前數據執行相關的SQL語句，產生結果**

#### 索引

索引是一種用來在某種條件下加速查詢的結構。



## SQLite命令

#### .show

```
sqlite> .show
        echo: off
         eqp: off
     explain: auto
     headers: off
        mode: list
   nullvalue: ""
      output: stdout
colseparator: "|"
rowseparator: "\n"
       stats: off
       width: 

```

#### .echo on
#### .headers on   // 打印表頭
#### .mode column
#### .nullvalue NULL

```
sqlite> .echo on
sqlite> .mode column
.mode column
sqlite> .headers on
.headers on
sqlite> .nullvalue NULL
.nullvalue NULL

sqlite> select *
   ...> from foods
   ...> where name='JujyFruit'
   ...> and type_id=9;
select *
from foods
where name='JujyFruit'
and type_id=9;
id          type_id     name      
----------  ----------  ----------
244         9           JujyFruit 

```





#### version

```
$ sqlite3 -version
3.11.0 2016-02-15 17:29:24 3d862f207e3adc00f78066799ac5a8c282430a5f

```

#### help

```
$ sqlite3 --help
Usage: sqlite3 [OPTIONS] FILENAME [SQL]
FILENAME is the name of an SQLite database. A new database is created
if the file does not previously exist.
OPTIONS include:
   -ascii               set output mode to 'ascii'
   -bail                stop after hitting an error
   -batch               force batch I/O
   -column              set output mode to 'column'
   -cmd COMMAND         run "COMMAND" before reading stdin
   -csv                 set output mode to 'csv'
   -echo                print commands before execution
   -init FILENAME       read/process named file
   -[no]header          turn headers on or off
   -help                show this message
   -html                set output mode to HTML
   -interactive         force interactive I/O
   -line                set output mode to 'line'
   -list                set output mode to 'list'
   -lookaside SIZE N    use N entries of SZ bytes for lookaside memory
   -mmap N              default mmap size set to N
   -newline SEP         set output row separator. Default: '\n'
   -nullvalue TEXT      set text string for NULL values. Default ''
   -pagecache SIZE N    use N slots of SZ bytes each for page cache memory
   -scratch SIZE N      use N slots of SZ bytes each for scratch memory
   -separator SEP       set output column separator. Default: '|'
   -stats               print memory stats before each finalize
   -version             show SQLite version
   -vfs NAME            use NAME as the default VFS
```


#### sqlite>.help
```
$ sqlite3 
SQLite version 3.11.0 2016-02-15 17:29:24
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite> .help
.backup ?DB? FILE      Backup DB (default "main") to FILE
.bail on|off           Stop after hitting an error.  Default OFF
.binary on|off         Turn binary output on or off.  Default OFF
.changes on|off        Show number of rows changed by SQL
.clone NEWDB           Clone data into NEWDB from the existing database
.databases             List names and files of attached databases
.dbinfo ?DB?           Show status information about the database
.dump ?TABLE? ...      Dump the database in an SQL text format
                         If TABLE specified, only dump tables matching
                         LIKE pattern TABLE.
.echo on|off           Turn command echo on or off
.eqp on|off            Enable or disable automatic EXPLAIN QUERY PLAN
.exit                  Exit this program
.explain ?on|off|auto? Turn EXPLAIN output mode on or off or to automatic
.fullschema            Show schema and the content of sqlite_stat tables
.headers on|off        Turn display of headers on or off
.help                  Show this message
.import FILE TABLE     Import data from FILE into TABLE
.indexes ?TABLE?       Show names of all indexes
                         If TABLE specified, only show indexes for tables
                         matching LIKE pattern TABLE.
.limit ?LIMIT? ?VAL?   Display or change the value of an SQLITE_LIMIT
.load FILE ?ENTRY?     Load an extension library
.log FILE|off          Turn logging on or off.  FILE can be stderr/stdout
.mode MODE ?TABLE?     Set output mode where MODE is one of:
                         ascii    Columns/rows delimited by 0x1F and 0x1E
                         csv      Comma-separated values
                         column   Left-aligned columns.  (See .width)
                         html     HTML <table> code
                         insert   SQL insert statements for TABLE
                         line     One value per line
                         list     Values delimited by .separator strings
                         tabs     Tab-separated values
                         tcl      TCL list elements
.nullvalue STRING      Use STRING in place of NULL values
.once FILENAME         Output for the next SQL command only to FILENAME
.open ?FILENAME?       Close existing database and reopen FILENAME
.output ?FILENAME?     Send output to FILENAME or stdout
.print STRING...       Print literal STRING
.prompt MAIN CONTINUE  Replace the standard prompts
.quit                  Exit this program
.read FILENAME         Execute SQL in FILENAME
.restore ?DB? FILE     Restore content of DB (default "main") from FILE
.save FILE             Write in-memory database into FILE
.scanstats on|off      Turn sqlite3_stmt_scanstatus() metrics on or off
.schema ?TABLE?        Show the CREATE statements　//綱要顯示，顯示`CREATE`聲明
                         If TABLE specified, only show tables matching
                         LIKE pattern TABLE.
.separator COL ?ROW?   Change the column separator and optionally the row
                         separator for both the output mode and .import
.shell CMD ARGS...     Run CMD ARGS... in a system shell
.show                  Show the current values for various settings
.stats on|off          Turn stats on or off
.system CMD ARGS...    Run CMD ARGS... in a system shell
.tables ?TABLE?        List names of tables
                         If TABLE specified, only list tables matching
                         LIKE pattern TABLE.
.timeout MS            Try opening locked tables for MS milliseconds
.timer on|off          Turn SQL timer on or off
.trace FILE|off        Output each SQL statement as it is run
.vfsinfo ?AUX?         Information about the top-level VFS
.vfslist               List all available VFSes
.vfsname ?AUX?         Print the name of the VFS stack
.width NUM1 NUM2 ...   Set column widths for "column" mode
                         Negative values right-justify
sqlite> 

```
