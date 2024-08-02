# Shell编程

## shell脚本基础

### >>> 变量

## shell脚本实例

### >>> 批量转换java源码编码格式

1. cd到源代码根目录，例如src/目录下

2. 将所有java文件由GBK转为UTF8格式

   ```shell
   find . -name "*.java" -exec sh -c "iconv -f GBK -t UTF8 {} > {}.utf8" \;
   ```

3. 删除所有java文件

   ```shell
   find . -name "*.java" -exec sh -c "rm {}" \;
   ```

4. 去掉剩下文件的.utf8后缀

   ```shell
   find . -name "*.utf8" | while read name;do newname=$(echo ${name%*.utf8});mv $name $n
   ewname; done
   ```

   