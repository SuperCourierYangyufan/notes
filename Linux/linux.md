# Linux

### Vim
1. yy拷贝该行,p为粘贴
2. dd删除该行
3. : /搜索关键字 n,为下一个
4. : set nu/set nonu   开启行号/关闭行号
5. : u 撤回操作
6. G/gg 文档最尾行,文档最前面

### 基本命令
1. 关机 shutdown -h now
2. 添加用户 useradd 用户名
3. 修改密码 passwd 密码
4. 删除用户 userdel -r 用户名(-r 为删除用户时删除目录)
5. 查询用户 id 用户名
6. 切换用户 su - 用户名
7. 增加用户组 groupadd 用户组名
8. 删除用户组 groupdel 用户组名
9. 添加用户并加入用户组 useradd -g 组名 用户名
10. 修改用户的用户组 usermod -g 组名 用户名
11. 创建快捷方式 ln -s 原地址 快捷方式名
12. 当前时间 date ( date "+%Y-%m-%d %H:%M:%S")格式化输出
13. 日历 cal(当前日历) 年份数字(年份数字一年的日历)
14. 查找文件 find 位置 -name 文件名(可*)
15. 查询文字内容 grep -n(显示行号) -i(忽略大小写) 查找内容 文件地址
16. 修改权限 chmod u=rwx,g=rx,o=x 文件目录名 (u/g/o 文件所有者/同组/其他组)(r/w/x 读/写/执行 4/2/1)
17. 修改文件所有者  chown -R 新所有者 文件路径     
18. 修改文件所在组 chgrp -R 组名 文件路径


### 任务调度
1. crontab -e(编写定时)/-r(删除所有定时)/-l(查看所有定时）
2. 脚本规则 */1 8,20 10-20 * * 任务语句 (分/时/天/月/周几)(每月的10号到20号的早8点或晚8点每分钟执行一次)

### 进程管理
1. 查询进程 ps -aux|grep 进程名
2. 关闭进程 kill -9(强行) 进程名
3. 服务管理 service/systemctl(centos7后使用) [start|stop|restart|reload|status]
4. 网络情况 netstat -anp|grep 进程名

### Shell
1. 脚本以#!/bin/bash 开头,文件为.sh后缀
2. 执行脚本需要给与权限 chmod 744 脚本名
3. 设置变量 A=10 ,使用时为$A
4. 撤销变量 unset 变量名(readonly静态变量不能撤销)
5. =号两侧不能有空格
6. A=$(命令) 将命令的结果返回给A,命令需要在$()里面
7. export 变量    可将变量作为环境变量,在别的shell脚本中也可使用,之前需要让其生效 source 该文件
8. 在脚本中${0/1/2/.....} 可设置预留参数,在执行脚本时 ./hello 1 可把1带入脚本中(0为执行脚本的命令)
9. 在$[]中做运算操作
10. if语句
    ```
      //注意空格
      if [ ${1} -ge 60 ]                                       /**
      then                                                    = 字符比较     -lt小于 -le小于等于  -eq等于   
        echo "及格"                                           -gt 大于   -ge  大于等于
      elif [ ${1} -lt 60 ]                                     */
      then
        echo "不及格"
      fi  
       
    ```
11. case语句
    ```
        case ${1} in
        "0")
        echo "周日"
        ;;
        "6")
        echo "周六"
        ;;
        *)
        echo "工作日"
        ;;
        esac
    ```
12. for语句
     ```
     //循环每个元素
     for i in A B C
     do
        echo $i
     done
     //循环数字
     for((i=1;i<=100;i++))
     do
        sum=$[$sum+$i]
     done
     ehco $sum
     ```
13. while循环
    ```

    SUM=0
    i=${1}
    while [ $i -gt 0 ]
    do
        SUM=$[$SUM+$i]
        i=$[$i-1]
    done
    echo $SUM

    ```
14. 控制台输入赋予值 read -t 10 -p "请输入一个数字" NUM            （-t等待时间-p提示）
