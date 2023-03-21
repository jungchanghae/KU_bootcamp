# SSH

SSH란 원격으로 호스트 컴퓨터에 접속하기 위해 사용되는 프로토콜입니다.
기본적으로 같은 네트워크 환경에서 서버에 연결할 때 사용합니다.


#### Host (server in linux)

1. Open `terminal`

2. Install `openssh-server`

   ```
   sudo apt update
   sudo apt install openssh-server
   ```

   `sudo` should require password for authentication that is set upon your account creation.

3. Start `ssh` service

   ```
   sudo service ssh start
   ```

   By default, the port is set to 22. It is strongly recommended to change the port number into something else; usually a number larger than 1024. The details are below - Further Settings.

4. Check if the service is working properly

   ```
   sudo service ssh status
   ```

#### Port Forwarding in Router

1. Request administartor for authority. (contact to @wheresmadog or @d-h-lee)
2. Typing "192.168.0.1" in internet browser will lead to router setting.
3. Setup port forwarding rule in the router setup by which external port leads to your host's internal port linstening to SSH service. (detailed setting varies depending on its manufacture company)

#### Client (laptop or any other PC)

1. Open `terminal` or `cmd` upon your OS

2. Connect to your remote

   ```
   ssh -p {port number} {user}@{host_ip}
   ```

   - `{port number}` is the number set up in the router.
   - `{host_ip}` is the public IP of the host, not private one in LAN.



### VSCode를 사용하여 SSH 연결 방법

- 명령어 팔레트 단축키인 `ctrl + shift + p`를 입력합니다.
- Remote-SSH:Connect to Host….를 누릅니다.
- Add New SSH Host…를 클릭합니다..
- `ssh -p #### $$$$@163.152.40.134`를 입력하시면 SSH Config가 완성됩니다.
- <img width="471" alt="iShot_2023-03-19_15 56 36" src="https://user-images.githubusercontent.com/97504297/226159286-e4e829a9-6436-447b-aa74-28923099a124.png">
- 
  - 톱니바퀴 아이콘을 눌러 Config 수정이 가능합니다.
  - 십자 아이콘을 눌러 다른 SSH 추가가 가능합니다. 혹은 위의 명령어 팔레트 단축키 방식으로 하셔도 됩니다.
  - 163.152…. 오른편의 아이콘을 눌러 서버에서의 VSCode를 실행할 수 있습니다.



### Tip : Keep your SSH remote session alive

https://github.com/AIML-K/for_education/issues/9 를 참조하시면 됩니다.



### VPN

VPN은 가설 사설망으로, 고려대학교는 VPN기능을 제공합니다.

쉽게 설명하자면, 연구실 네트워크가 아닌 다른 네트워크(KoreaUnivAP, Starbucks, etc.)로는 SSH를 통해 클라이언트로 접근이 불가합니다.

하지만 고려대학교 VPN을 사용하면 외부 네트워크에서도 여러분의 클라이언트로 언제든지 접속이 가능합니다. 당신이 지구 반대편에 있어도 말이죠.



#### VPN 접속을 위한 보안신청
<img width="471" alt="iShot_2023-03-19_15 56 36" src="https://user-images.githubusercontent.com/97504297/226159272-a0242be2-b5ec-4b01-ae31-fe7170025d9d.png">

- 포탈 접속 - 정보생활 - 보안서비스 신청안내 - 보안신청서 #3을 다운 받으시고 해당신청서의 공란을 채우신 뒤 교수님께 제출하시면 됩니다.
- 포트번호는 잊기 쉬운 번호보다는 핸드폰 뒷 자리와 같이 외우기 쉬운 번호를 추천드립니다.
- 몇일 기다리시면 VPN을 이제부터 이용 가능하다는 메일이 올 것입니다.
  - 축하합니다! 이제부터 당신은 재택근무를 할 수 있습니다.



#### 기타 Tip - Public Key Setup

VPN이나 SSH를 통해 매번 서버의 비밀번호를 입력하는 것은 피곤한 일일 수도 있고, 간혹 키로거 등의 공격에 의해 보안상의 문제가 생길 수도 있습니다. 따라서 그것을 도와줄 문서를 첨부합니다.

#### Tired of inserting password every time?

For user authhentication SSH requires user password every time you connect to your host. As an alternative, you can use public key setup for the authentication.

1. Create asymmetric key pair.

   ```
   ssh-keygen -t rsa
   ```

2. Copy the public key to your host.

   - Linux/Mac

     ```
     ssh-copy-id -p {port number} {user}@{host ip}
     ```

     Equivalently,

     ```
     ssh-copy-id -p {port number} -i {path to public key} {user}@{host ip}
     ```

     `{path to public key}` is `~/.ssh/id_rsa.pub` by default, having RSA key generated on your host.

   - Windows

     ```
     type {path to public key} | ssh -p {port} {user}@{host ip} "cat >> /.ssh/authorized_keys"
     ```

     In case of Windows, the `{path to public key}` is usually `C:\Users\{user name}\.ssh`

3. Connect your host with SSH 

   ```
   ssh -p {port number} {user}@{host ip}
   ```

   For this time, the server won't require any password. Because you have your client's public key planted in the host.
