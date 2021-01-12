# regmap

 

```
 regmap_write(struct regmap *map, int reg, int val); // 向reg写入val
 regmap_raw_write(struct regmap *map, int reg, void *val, size_t val_len);  // 向reg写入指定长度的数据，数据存放在val中
 regmap_read(struct regmap *map, int reg, int *val); // 读取reg的数据到val中
 regmap_raw_read(struct regmap *map, int reg, void *val, size_t val_len); // 读取reg中指定长度的数据
 regmap_bulk_read(struct regmap *map, int reg, void *val, size_t val_count); // 读取从reg开始之后val_count个寄存器的数据到val中
 regmap_update_bits(struct regmap *map, int reg, int mask, int val); // 更新reg寄存器中mask指定的位
 regcache_cache_bypass(arizona->regmap, true); // 设置读写寄存器不通过cache模式而是bypass模式，读写立即生效，一般在audio等确保时序性驱动中用到
 ```