# Домашнее задание к занятию "Уязвимости и атаки на информационные системы" - `Александра Бужор`

---

### Задание 1

Скачайте и установите виртуальную машину Metasploitable: https://sourceforge.net/projects/metasploitable/.

Это типовая ОС для экспериментов в области информационной безопасности, с которой следует начать при анализе уязвимостей.

Просканируйте эту виртуальную машину, используя nmap.

Попробуйте найти уязвимости, которым подвержена эта виртуальная машина.

Сами уязвимости можно поискать на сайте https://www.exploit-db.com/.

Для этого нужно в поиске ввести название сетевой службы, обнаруженной на атакуемой машине, и выбрать подходящие по версии уязвимости.

Ответьте на следующие вопросы:
- Какие сетевые службы в ней разрешены?
- Какие уязвимости были вами обнаружены? (список со ссылками: достаточно трёх уязвимостей)
- Приведите ответ в свободной форме.

### Решение:

Какие сетевые службы разрешены:
```bash
:~$ nmap -sV 172.1.2.3
Starting Nmap 7.80 ( https://nmap.org ) at 2024-03-02 10:28 MSK
Nmap scan report for 172.1.2.3
Host is up (0.0061s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login
514/tcp  open  tcpwrapped
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.17 seconds
```

Какие уязвимости обнаружены:

#### 1. vsftpd 2.3.4 (backdoor)
[vsftpd 2.3.4 - Backdoor Command Execution](https://www.exploit-db.com/exploits/49757)

#### 2. OpenSSH 4.7p1 Debian 8ubuntu1
Хотя для этой версии OpenSSH нет прямого эксплойта, атакующий может попытаться использовать слабые или стандартные пароли для доступа.

#### 3. ProFTPD 1.3.x
[ProFTPd IAC 1.3.x - Remote Command Execution](https://www.exploit-db.com/exploits/15449)

#### 4. MySQL 5.0.x
[MySQL 5.0.x - IF Query Handling Remote Denial of Service](https://www.exploit-db.com/exploits/30020)

[MySQL 5.0.x - Single Row SubSelect Remote Denial of Service](https://www.exploit-db.com/exploits/29724)

#### 5. PostgreSQL DB 8.3.0 - 8.3.7
[PostgreSQL 8.2/8.3/8.4 - UDF for Command Execution](https://www.exploit-db.com/exploits/32849)

[PostgreSQL 8.3.6 - Conversion Encoding Remote Denial of Service](https://www.exploit-db.com/exploits/32847)

[PostgreSQL 8.3.6 - Low Cost Function Information Disclosure](https://www.exploit-db.com/exploits/7855)

---

### Задание 2

Проведите сканирование Metasploitable в режимах SYN, FIN, Xmas, UDP.

Запишите сеансы сканирования в Wireshark.

Ответьте на следующие вопросы:
- Чем отличаются эти режимы сканирования с точки зрения сетевого трафика?
- Как отвечает сервер?
- Приведите ответ в свободной форме.

### Решение:

1. **SYN сканирование** - это так называемое "полураскрытое" сканирование. Оно отправляет SYN пакеты (начало TCP соединения) на разные порты целевой машины. Если сервер отвечает SYN-ACK (знак того, что порт открыт), сканер отправляет RST (сброс), чтобы избежать полного установления соединения. Это минимизирует логирование на целевой системе.

2. **FIN сканирование** отправляет FIN пакеты, которые обычно отправляются для закрытия соединения. По стандарту TCP, если порт закрыт, то в ответ на FIN пакет должен прийти RST от целевой системы. Открытые порты, в теории, не отвечают на FIN пакеты в большинстве операционных систем, что может использоваться для обнаружения открытых портов без установления полного соединения.

3. **Xmas сканирование** работает похожим образом на FIN сканирование, но отправляет пакеты с установленными флагами FIN, PSH и URG, что делает пакеты "яркими" как рождественская елка (отсюда и название). Теория такая же: открытые порты не отвечают на такие пакеты, а закрытые отправляют RST.

4. **UDP сканирование** отличается от предыдущих тем, что оно направлено на UDP порты, а не TCP. UDP — это протокол без установления соединения, так что сканирование отправляет UDP пакеты на различные порты. Если порт закрыт, то система должна ответить сообщением о недостижимости порта (ICMP port unreachable error). Если ответа нет, сканер может предположить, что порт открыт или фильтруется.

С точки зрения сетевого трафика, эти методы сканирования создают различные типы пакетов и ожидают разные ответы, что позволяет обнаруживать открытые, закрытые или фильтруемые порты без полного установления соединения.
