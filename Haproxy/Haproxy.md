利用socat对haproxy服务器进行 动态上下线

apt install socat

 ssh root@172.31.0.103 "echo "disable server  linux39-web-80-master/IP" | socat stdio /run/haproxy/admin.sock"

\# 如果开启了多进程，每个进程sockt都需要下线一次





页面状态检查

curl -s  -I -m 10 -o /dev/null  -w %{http_code}  http://${node}:8080/myapp/index.html