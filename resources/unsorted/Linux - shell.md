# Linux 命令

阅读列表：[第3章 管道符、重定向与环境变量 | 《Linux就该这么学》 (linuxprobe.com)](https://www.linuxprobe.com/basic-learning-03.html)

## 系统信息

### uname

```shell
uname -m # 显示机器的处理器架构
uname -r # 显示正在使用的内核版本 
```

### date&cal&clock

```shell
date                 # 显示系统日期 
cal 2007             # 显示2007年的日历表 
date 041217002007.00 # 设置日期和时间 - 月日时分年.秒 
clock -w             # 将时间修改保存到 BIOS 
```

## 文件管理

### find

参数：

- -perm可以选择权限
- -user可是选择用户
- -group可以选择用户组
- -size可以选择大小

| 大小字母 | 文字表示      |
| -------- | ------------- |
| b        | 块（512字节） |
| c        | 字节          |
| w        | 字（2字节)    |
| k        | 千字节        |
| M        | 兆字节        |
| G        | 吉字节        |

- -type ：文件类型

| 类型参数列表 | 表示字母 |
| ------------ | -------- |
| 普通文件     | f        |
| 符号连接     | l        |
| 普通目录     | d        |
| 字符设备     | c        |
| 块设备       | b        |
| 套接字       | s        |

- -exec ：后面可再接额外的指令来处理搜寻到的结果

  { }代表的是「由 find 找到的内容」，如上图所示，找到的结果会被放置到 { } 位置中;

  -exec一直到 ; 是关键字，代表找到额外动作的开始（-exec）到结束（\），在这中间的就是找到指令内的额外动作

  因为「;」在bash的环境下是有特殊意义的，因此利用反斜线来跳脱。

```shell
# 列出当前文件夹下所有txt文件，默认递归查找，匹配多个文件，-name参数加单引号
find . -name '*.txt'
# -iname忽略大小写
find . -iname '*.txt'
# 搜索属于用户 'joby' 的文件和目录 
find . -user joby 
# 搜索文件大小大于、小于10M的文件
find . -size +10M
find . -size -10M
# 搜索在过去100天内未被使用过的执行文件
find /usr/bin -type f -atime +100  
# 搜索在10天内被创建或者修改过的文件 
find /usr/bin -type f -mtime -10 
# 搜索以 '.rpm' 结尾的文件并定义其权限 
find / -name \*.rpm -exec chmod 755 '{}' \; 
find /root/.ssh -exec ls -l {} \;
find /root -size 10M -exec rm -rf {} \;

```

### file

```shell
# 获取文件的mime type
file a.txt 
```

### whereis&which

```shell
# 显示一个二进制文件、源码或man的位置 
whereis halt 
# 显示一个二进制文件或可执行文件的完整路径 
which halt 
```



### mkdir

```shell
mkdir dir1 dir2            # 同时创建两个目录 
mkdir -p /tmp/dir1/dir2    # 创建一个目录树
```

### cp

```shell
cp file1 file2    # 复制一个文件 
cp dir/* .        # 复制一个目录下的所有文件到当前工作目录 
cp -a /tmp/dir1 . # 复制一个目录到当前工作目录 
cp -a dir1 dir2   # 复制一个目录 
```

### ln

```shell
ln -s file1 lnk1 # 创建一个指向文件或目录的软链接 
ln file1 lnk1    # 创建一个指向文件或目录的物理链接 
```



## 文本处理

### 文本查看

```shell
cat file1
more file1
less file1
head file1
tail file1
```

### >&>>（文本写入文件）

```shell
# 将a.txt中的文本写入新文件b.txt，如果b.txt存在，会覆盖原文件
cat a.txt > b.txt
# 在c.txt文件末尾插入
cat a.txt >> c.txt
```

### grep

```shell
# 使用正则查找文件a.txt中的URL
grep --only-matching http\:\/\/.* a.txt
# 查找配置文件中的配置项max_connections，显示查找结果所在行
grep --line-number "max_connections=" my.cnf
# 查找当前文件夹下包含字符串“127.0.0.1”的文件，
# -i表示不区分大小写
# -r表示递归查找，使用-r可以不传文件路径参数（默认当前路径），或者传一个文件夹路径
# -l匹配多个文件时，显示匹配的文件名，-L匹配多个文件时，显示不匹配的文件名
grep -irl "127.0.0.1"
```

### xargs

xargs用来连接两个命令，将上一个命令的输出当作下一个命令的参数。

```shell
# 删除当前文件夹下log文件
find . -name "*.log" | xargs rm
# 上面的命令如果遇到文件名里有空格或者换行符，就会出错。因为xargs识别字符段的标识是空格或者换行符，
# 所以如果一个文件名里有空格或者换行符，xargs就会把它识别成两个字符串
find . -name "*.log" -print0 | xargs -0 rm
# 获得/etc下所有以.conf结尾的文件
find /etc -name "*.conf" | xargs ls –l
# 下载url-list.txt中的url
cat url-list.txt | xargs wget –c
# 找出所有的.jpg格式的图片，并将其归档
ind / -name *.jpg -type f -print | xargs tar -cvzf images.tar.gz
# 将当前目录下所有txt文件复制到根目录
# -t 先打印命令再执行
# -i 将查找到的文件名，一行一行的赋值给{}，否则会一起赋值给{}，cp命令就会报错
find . -name '*.txt' | xargs -t -i cp {} /
```

### sed

```shell
# 将a.txt中的127.0.0.1替换为localhost
sed -i ‘s/127.0.0.1/localhost/g' a.txt
sed '/^$/d' example.txt         # 从example.txt文件中删除所有空白行 
sed '/ *#/d; /^$/d' example.txt # 从example.txt文件中删除所有注释和空白行 
sed -e '1d' example.txt         # 从文件example.txt 中排除第一行 
sed -n '/stringa1/p'            # 查看只包含词汇 "string1"的行 
sed -e 's/ *$//'                # example.txt 删除每一行最后的空白字符 
sed -e 's/stringa1//g' # example.txt 从文档中只删除词汇 "string1" 并保留剩余全部 
sed -n '1,5p;5q'                # example.txt 查看从第一行到第5行内容 
sed -n '5p;5q'                  # example.txt 查看第5行 
sed -e 's/00*/0/g'              # example.txt 用单个零替换多个零 
```

### awk

```shell
cat example.txt | awk 'NR%2==1' # 删除example.txt文件中的所有偶数行 
echo a b c | awk '{print $1}' # 查看一行第一栏 
echo a b c | awk '{print $1,$3}' # 查看一行的第一和第三栏 
```

### sort

```shell
sort file1 file2 # 排序两个文件的内容 
sort file1 file2 | uniq # 取出两个文件的并集(重复的行只保留一份) 
sort file1 file2 | uniq -u # 删除交集，留下其他的行 
sort file1 file2 | uniq -d # 取出两个文件的交集(只留下同时存在于两个文件中的文件) 
```

### comm

```shell
comm -1 file1 file2 # 比较两个文件的内容只删除 'file1' 所包含的内容 
comm -2 file1 file2 # 比较两个文件的内容只删除 'file2' 所包含的内容 
comm -3 file1 file2 # 比较两个文件的内容只删除两个文件共有的部分
```



### cut

语法：cut [参数] [文件]

参数：

-  -c ：以字符为单位进行分割
- -b ：以字节为单位进行分割
- -d ：自定义分隔符，默认为制表符
- -f ：与-d一起使用，指定显示哪个区域

```shell
# 结合grep命令，显示mysql配置文件max_connections的值
grep --line-number "max_connections=" my.cnf | cut -d= -f2
# 取一行的第3个字节或字符
who|cut -b 3
who|cut -c 3
```



### vi/vim

## 网络工具

### ip

```shell
ip addr
```

### route

### netstat

### tcpdump

### wget

```shell
# 断点续传 -c
# 指定保存文路径
wget -P /etc/software https:www.abc.com/file
```



## 进程管理

## 磁盘管理

### mount&umount

```shell
# 挂载一个叫做hda2的盘 - 确定目录 '/ mnt/hda2' 已经存在 
mount /dev/hda2 /mnt/hda2 
# 挂载一个cdrom或dvdrom
mount /dev/cdrom /mnt/cdrom  
# 挂载一个文件或ISO镜像文件
mount -o loop file.iso /mnt/cdrom  
# 挂载一个Windows FAT32文件系统
mount -t vfat /dev/hda5 /mnt/hda5  
# 挂载一个usb 捷盘或闪存设备
mount /dev/sda1 /mnt/usbdisk  
# 挂载一个windows网络共享 
mount -t smbfs -o username=user,password=pass //WinClient/share /mnt/share 
# 卸载一个叫做hda2的盘 - 先从挂载点 '/mnt/hda2' 退出 
umount /dev/hda2 
# 运行卸载操作而不写入 /etc/mtab 文件- 当文件为只读或当磁盘写满时非常有用
umount -n /mnt/hda2  
```



## 用户和权限管理

### 用户

```shell
# 创建一个属于 "admin" 用户组的用户
useradd -c "Name Surname " -g admin -d /home/user1 -s /bin/bash user1 
# 创建一个新用户
useradd user1 
# 删除一个用户 ( '-r' 排除主目录)
userdel -r user1  
# 修改用户属性
usermod -c "User FTP" -g system -d /ftp/user1 -s /bin/nologin user1  
# 修改口令 
passwd 
passwd user1 修改一个用户的口令 (只允许root执行) 

chage -E 2005-12-31 user1 设置用户口令的失效期限 
pwck 检查 '/etc/passwd' 的文件格式和语法修正以及存在的用户 
```





### 组

```shell
groupadd group_name 创建一个新用户组 
groupdel group_name 删除一个用户组 
groupmod -n new_group_name old_group_name 重命名一个用户组 

grpck 检查 '/etc/passwd' 的文件格式和语法修正以及存在的群组 
newgrp group_name 登陆进一个新的群组以改变新创建文件的预设群组 
```



### 权限修改

#### <u>chmod</u>

功能：修改不同用户对文件的权限，从左到右分别是**文件属主、属主同组用户、其他用户**的**读、写、执行**权限，1表示可以，0表示不可以，用比如：
-rwxrwxrwx 用二进制表示为111111等于十进制 777

语法：chmod ［who］ ［+ | – | =］ ［mode］ 文件名

参数：

- [who]可是下述字母中的任一个或者它们的组合
- u 表示用户（user），即文件或目录的所有者
- g 表示同组（group）用户，即与文件属主有相同组ID的所有用户
- o 表示其他（others）用户
- a 表示所有（all）用户
- 操作符号可以是
- \+ 添加某个权限
- – 取消某个权限
- = 赋予给定权限并取消其他所有权限
- 设置mode所表示的权限可用下述字母的任意组合
- r 可读。
- w 可写。
- x 可执行。
- X 只有目标文件对某些用户是可执行的或该目标文件是目录时才追加x 属性。
- s 在文件执行时把进程的属主或组ID置为该文件的文件属主。方式“u＋s”设置文件的用户ID位，“g＋s”设置组ID位。
- t 保存程序的文本到交换设备上。
- u 与文件属主拥有一样的权限。
- g 与和文件属主同组的用户拥有一样的权限。
- o 与其他用户拥有一样的权限。
- -c : 若该档案权限确实已经更改，才显示其更改动作
- -f : 若该档案权限无法被更改也不要显示错误讯息
- -v : 显示权限变更的详细资料
- -R : 对目前目录下的所有档案与子目录进行相同的权限变更(即以递回的方式逐个变更)
- –help : 显示辅助说明
- –version : 显示版本

#### <u>chgrp</u>

功能：改变文件或目录所属的组

语法：chgrp［选项］ group 文件名

参数：

- -R或–recursive 　递归处理，将指定目录下的所有文件及子目录一并处理

#### <u>chown</u>

功能：更改某个文件或目录的属主和属组

语法：chown ［选项］ 用户或组 文件

参数：

- user : 新的档案拥有者的使用者 ID
- group : 新的档案拥有者的使用者群体(group)
- -c : 若该档案拥有者确实已经更改，才显示其更改动作
- -f : 若该档案拥有者无法被更改也不要显示错误讯息
- -h : 只对于连结(link)进行变更，而非该 link 真正指向的档案
- -v : 显示拥有者变更的详细资料
- -R : 对目前目录下的所有档案与子目录进行相同的拥有者变更(即以递回的方式逐个变更)



# Shell 脚本

## shell脚本基础

### 变量

## shell脚本应用实例

### 批量转换java源码编码格式

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

   [如何在Shell脚本中逐行读取文件 (qq.com)](https://mp.weixin.qq.com/s/03hzf4jxqOn6J0j71zzqQA)

### pmp考试报名脚本

```shell
#!/bin/bash

USERNAME=zhangsan
PASSWORD=123456

# 英文报考名
FIRSTNAME=San
LASTNAME=Zhang

# 英文报考网站用户名&密码&PMIID
PMIUNAME=zhangsan@126.com
PMIUPASS=123456
PMIID=98765432

# 有效期
PMITIMEB=2020-12-20
PMITIMEE=2021-12-20

# 考场关键字
EXAM_VENUE_KW=天津光环-河东区1

# 培训机构信息
ORGAN_ID=123
ORGAN_NAME=能力有限公司

echo "####################################################"
echo "##            PMP中文报名自动化脚本               ##"
echo "####################################################"

SYSTEM_ONLY_COOKIE_NAME=481A2B9FRR771237777BGYUANY123KKK
SYSTEM_TEMP_COOKIE_NAME=98MIUNAMEEEE1146540123456JJNKKKK

PMITIMEB=$(date --date="$PMITIMEB" +%s)000
PMITIMEE=$(date --date="$PMITIMEE" +%s)000

# 定义命名管道
FD_NO=1000
FIFO_FILE=/tmp/$.$FD_NO.fifo.$(date +%N)
mkfifo $FIFO_FILE
eval "exec $FD_NO<>$FIFO_FILE"

# 定义管道通量并初始化
FLUX=8
for idx in $(seq $FLUX)
do
  echo >& $FD_NO
done


# 获取Ajax-Token
function get_ajax_token
{
  echo $(curl 'http://exam.chinapmp.cn/App_Ajax/ajaxscript;SHOW.Ajax.Exam.Login,SHOW.Ajax;.ajax?domain=exam' \
    -H 'Connection: keep-alive' \
    -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36' \
    -H 'Accept: */*' \
    -H 'Referer: http://exam.chinapmp.cn/' \
    -H 'Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-TW;q=0.6,sm;q=0.5' \
    -H "Cookie: SYSTEM_ONLY_COOKIE_NAME=$SYSTEM_ONLY_COOKIE_NAME; SYSTEM_TEMP_COOKIE_NAME=$SYSTEM_TEMP_COOKIE_NAME;" \
    --compressed \
    --insecure \
    --max-time 60 \
    -s | grep 'NET.UNEXT.Ajax.token' | grep -oE '\w{8}\.\w{32}')
}
AJAX_TOKEN=''
while [ "$AJAX_TOKEN" = '' ]
do
  read -u $FD_NO AJAX_TOKEN
  {
    echo $(get_ajax_token) >& $FD_NO
  } &
done

wait
eval "exec $FD_NO<&-; exec $FD_NO>&-;"
echo "Ajax-Token: $AJAX_TOKEN"



# 登录
function login
{
  echo $(curl 'http://exam.chinapmp.cn/App_Ajax/SHOW.Ajax.Exam.Login,SHOW.Ajax.ajax?from=http%3a%2f%2fuser.chinapmp.cn%2findex.shtml&domain=exam' \
  -H 'Connection: keep-alive' \
  -H "X-UNEXT.Ajax-Token: $AJAX_TOKEN " \
  -H 'X-UNEXT.Ajax-Method: Save' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36' \
  -H 'Content-Type: text/plain; charset=UTF-8' \
  -H 'Accept: */*' \
  -H 'Origin: http://exam.chinapmp.cn' \
  -H 'Referer: http://exam.chinapmp.cn/' \
  -H 'Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-TW;q=0.6,sm;q=0.5' \
  -H "Cookie: SYSTEM_ONLY_COOKIE_NAME=$SYSTEM_ONLY_COOKIE_NAME; SYSTEM_TEMP_COOKIE_NAME=$SYSTEM_TEMP_COOKIE_NAME;" \
  --data-raw "{\"JSON_DATA\":[{\"Name\":\"uType\",\"Value\":0},{\"Name\":\"uName\",\"Value\":\"$USERNAME\"},{\"Name\":\"uPass\",\"Value\":\"$PASSWORD\"}]}" \
  --compressed \
  --insecure \
  --max-time 60 \
  -s  | grep -oE 'http://user\.chinapmp\.cn/index\.shtml')
}


FIFO_FILE=/tmp/$.$FD_NO.fifo.$(date +%N)
mkfifo $FIFO_FILE
eval "exec $FD_NO<>$FIFO_FILE"

for idx in $(seq $FLUX)
do
  echo >& $FD_NO
done

LOGIN_SUCCESS=''
while [ "$LOGIN_SUCCESS" = '' ]
do
  read -u $FD_NO LOGIN_SUCCESS
  {
    echo $(login) >& $FD_NO
  } &
done
wait
eval "exec $FD_NO<&-; exec $FD_NO>&-;"
echo '登录成功'



# 获取考场编号&名称
function get_exam_venue
{
  echo $(curl 'http://user.chinapmp.cn/examsign;sign.shtml' \
  -H 'Connection: keep-alive' \
  -H 'Upgrade-Insecure-Requests: 1' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36' \
  -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9' \
  -H 'Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-TW;q=0.6,sm;q=0.5' \
  -H "Cookie: SYSTEM_ONLY_COOKIE_NAME=$SYSTEM_ONLY_COOKIE_NAME; SYSTEM_TEMP_COOKIE_NAME=$SYSTEM_TEMP_COOKIE_NAME;" \
  --compressed \
  --insecure \
  --max-time 60 \
  -s | grep '<option value' | grep "$EXAM_VENUE_KW" | grep -oE '[0-9]+">.*?</option>' | tail -n 1 | sed 's/">/ /' | sed 's/<\/option>//')
}

FIFO_FILE=/tmp/$.$FD_NO.fifo.$(date +%N)
mkfifo $FIFO_FILE
eval "exec $FD_NO<>$FIFO_FILE"

for idx in $(seq $FLUX)
do
  echo >& $FD_NO
done

EXAM_VENUE=''
while [ "$EXAM_VENUE" = '' ]
do
  read -u $FD_NO EXAM_VENUE
  {
    echo $(get_exam_venue) >& $FD_NO
  } &
done
wait
eval "exec $FD_NO<&-; exec $FD_NO>&-;"
EXAM_VENUE=($EXAM_VENUE)
echo "考场: ${EXAM_VENUE[0]} ${EXAM_VENUE[1]}"


# 报名
function exam_apply
{
  echo $(curl 'http://user.chinapmp.cn/App_Ajax/SHOW.Ajax.User.Examsign,SHOW.Ajax.ajax?domain=user' \
  -H 'Connection: keep-alive' \
  -H "X-UNEXT.Ajax-Token: $AJAX_TOKEN " \
  -H 'X-UNEXT.Ajax-Method: Sign' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36' \
  -H 'Content-Type: text/plain; charset=UTF-8' \
  -H 'Accept: */*' \
  -H 'Origin: http://user.chinapmp.cn' \
  -H 'Referer: http://user.chinapmp.cn/examsign;sign.shtml' \
  -H 'Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-TW;q=0.6,sm;q=0.5' \
  -H "Cookie: SYSTEM_ONLY_COOKIE_NAME=$SYSTEM_ONLY_COOKIE_NAME; SYSTEM_TEMP_COOKIE_NAME=$SYSTEM_TEMP_COOKIE_NAME;" \
  --data-raw "{\"JSON_DATA\":[{\"Name\":\"Ed\",\"Value\":10000044},{\"Name\":\"Etitle\",\"Value\":\" 2021年6月20日项目管理资格认证考试\"},{\"Name\":\"Stype\",\"Value\":101},{\"Name\":\"StypeName\",\"Value\":\"项目管理师(PMP®)\"},{\"Name\":\"Xing\",\"Value\":\"$LASTNAME\"},{\"Name\":\"Zhong\",\"Value\":\"\"},{\"Name\":\"Ming\",\"Value\":\"$FIRSTNAME\"},{\"Name\":\"Peixunjigou\",\"Value\":\"$ORGAN_ID\"},{\"Name\":\"Peixunjigouming\",\"Value\":\"$ORGAN_NAME\"},{\"Name\":\"PMIUname\",\"Value\":\"$PMIUNAME\"},{\"Name\":\"PMIUpass\",\"Value\":\"$PMIUPASS\"},{\"Name\":\"IsPMIUser\",\"Value\":false},{\"Name\":\"PMINumber\",\"Value\":\"\"},{\"Name\":\"_PMIUtimeB\",\"Value\":\"\"},{\"Name\":\"_PMIUtimeE\",\"Value\":\"\"},{\"Name\":\"PMItimeB\",\"Value\":\"/Date($PMITIMEB+0800)/\"},{\"Name\":\"PMItimeE\",\"Value\":\"/Date($PMITIMEE+0800)/\"},{\"Name\":\"Kaodian\",\"Value\":\"${EXAM_VENUE[0]}\"},{\"Name\":\"Kaodianming\",\"Value\":\"${EXAM_VENUE[1]}\"},{\"Name\":\"PMIID\",\"Value\":\"$PMIID\"}]}" \
  --compressed \
  --max-time 60 \
  -s | grep -oE '[0-9]+\.00|考点报名人数已达上限')
}


FIFO_FILE=/tmp/$.$FD_NO.fifo.$(date +%N)
mkfifo $FIFO_FILE
eval "exec $FD_NO<>$FIFO_FILE"

for idx in $(seq $FLUX)
do
  echo >& $FD_NO
done

EXAM_COST=''
while [ "$EXAM_COST" = '' ]
do
  read -u $FD_NO EXAM_COST
  {
    echo $(exam_apply) >& $FD_NO
  } &
done
wait
eval "exec $FD_NO<&-; exec $FD_NO>&-;"
if [ "$EXAM_COST" = '考点报名人数已达上限' ]; then
  echo "报考失败：考点报名人数已达上限！"
else
  echo "报考成功，考试费用：￥$EXAM_COST"
fi


rm -f $FIFO_FILE
```

