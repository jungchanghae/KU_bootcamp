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
- `ssh -p #### $$$$@ adress IP`를 입력하시면 SSH Config가 완성됩니다.


### VPN

VPN은 가설 사설망으로, 고려대학교는 VPN기능을 제공합니다.

쉽게 설명하자면, 연구실 네트워크가 아닌 다른 네트워크(KoreaUnivAP, Starbucks, etc.)로는 SSH를 통해 클라이언트로 접근이 불가합니다.

하지만 고려대학교 VPN을 사용하면 외부 네트워크에서도 여러분의 클라이언트로 언제든지 접속이 가능합니다. 당신이 지구 반대편에 있어도 말이죠.


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
