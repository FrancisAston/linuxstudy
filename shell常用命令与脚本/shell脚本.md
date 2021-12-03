su - wang -c "python.sh"	# 已wang身份用运行脚本，无法登录的用户无法使用

sudo -u sshd "echo"	# 这个才是正解



man 
man -k 关键字	# 可以进行搜索
man 2 intro	# man 可以选择需要查看的章节
info	# 也可以查看帮助信息



ls -F 	# 能够区分文件夹、文件，显示*表示可执行文件
ls -R 	# 递归显示

ls -s --time=atime 	# 查看最近访问时间，ls默认显示的是修改时间

file 文件名	# 查看文件类型，

cat -n |-b 	# 显示行号
cat -T  	#  使用^I替换tab制表符

less | more	# 用于分页查看

du -c -h -s 	# 查看当前目录文件占用磁盘情况





nc命令：将数据发送给ip：端口

删除当下文件名中的空格

```
#!/bin/bash
for file in *; do
old_file_name=${file}
#echo $old_file_name
new_file_name=`echo ${old_file_name} | tr -d " "`
mv "${old_file_name}" "${new_file_name}"
done

```



