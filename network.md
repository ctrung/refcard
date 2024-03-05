# Check port standard usage

```
grep -w '80/tcp' /etc/services
http            80/tcp          www www-http    # WorldWideWeb HTTP

grep -w '443/tcp' /etc/services
https           443/tcp                         # http protocol over TLS/SSL

grep -w '8080/tcp' /etc/services
webcache        8080/tcp        http-alt        # WWW caching service

grep -w '22/tcp' /etc/services
ssh             22/tcp                          # The Secure Shell (SSH) Protocol
```

# Check which app is using a port (Windows)

```sh
netstat -aon | findstr PORT_NUMBER
tasklist | findstr PID               # PID retrieved from previous netstat command
```

# List open ports

```
# -t : All TCP ports
# -u : All UDP ports
# -l : Display listening server sockets
# -p : Show the PID and name of the program to which each socket belongs
# -n : Don’t resolve names
# | grep LISTEN : Only display open ports by applying grep command filter.

sudo netstat -tulpn | grep LISTEN
```

The ss command is used to dump socket statistics. It allows showing information similar to netstat. It can display more TCP and state information than other tools.
```
ss -tulpn
```

```
lsof -i -P -n | grep LISTEN
```

The nmap command is an open source tool for network exploration and security auditing.
```
sudo nmap -sT -O localhost
sudo nmap -sU -O 192.168.2.254 ##[ list open UDP ports ]##
sudo nmap -sT -O 127.0.0.1 ##[ list open TCP ports ]##
sudo nmap -sTU -O 192.168.2.24
```


The open port doesn’t mean anyone from outside can access those ports. Remember to check eg. corporate network rules, firewall rules, etc...

On Linux server we list or dump firewall rules using the following syntax :
```
sudo iptables -S
sudo ip6tables -S # IPv6
```
