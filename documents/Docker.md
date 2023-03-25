# Docker tutorial

## What is Docker?

- 컨테이너 기반 가상화 플랫폼이다.

- 다양한 (오픈)소스를 공유할 때 코드뿐만 아니라 그 코드를 실행하는 환경까지 같이 공유할 수 있다.

- OS 전체를 담지는 않되 필요한 라이브러리를 자동으로 포함시켜 만들 수 있어 가볍다.

- 주로 한 작업의 환경을 모두 맞춰주기 위해 사용한다. (Python, torch, TF, Cuda, Cudnn...)

## Docker installation

<https://docs.docker.com/engine/install/ubuntu/>

<details>
<summary>cheat sheet</summary>
<div markdown="1">

- remove old version docker
```sh
sudo apt-get remove docker docker-engine docker.io containerd runc
```

- docker 설치 전 requirements
```sh
 sudo apt-get update
 sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

- docker 설치: 순서대로
  - apt 키 및 repo 설정
```sh
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```sh
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
  - docker 설치
```sh
sudo apt-get update
```

```sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- 설치 잘 되었는지 확인
```sh
sudo docker run hello-world
```

</div>
</details>

## sudo 없이 docker 사용하기(Optional)

```sh
# Create the docker group.
sudo groupadd docker

# Add your user to the docker group.
sudo usermod -aG docker $USER
```

## Materials

- `tensorflow/tensorflow:2.5.0-gpu-jupyter`
  - for Ubuntu 18.04

- `nvcr.io/nvidia/pytorch:21.11-py3`
  - see [NGC Support Matrix](https://docs.nvidia.com/deeplearning/frameworks/support-matrix/index.html)


## Nvidia docker

- docker container 안에서 GPU와 그래픽 드라이버를 사용할 수 있게 해 주는 라이브러리

- [installation guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker)

## Docker commands I(frequently used)

- `run` : docker image로부터 container를 만들 때 사용

  - options (frequently used)
    - modes : 실행 모드, 대표적으로는 interactive mode 와 detached mode를 사용
      - `-it` : interactive mode. 이걸 넣어서 실행시 container의 shell에 바로 연결되어 사용할 수 있음
      - `-d` : detached mode. 이 경우 container가 background process로 실행된다
      - `--rm` : 이게 입력될 경우 일회용 container 생성. 프로세스가 끝나거나 docker stop을 통해 컨테이너가 꺼질 경우 아예 삭제된다
    - `--gpus` : 컨테이너가 사용할 GPU를 지정하는 옵션. all을 사용하면 전체가 할당되고 일부만 지정하려면 숫자 사용
    - `-v` : host의 filesystem을 공유하는 옵션. 일종의 동기화가 되어 컨테이너 안/밖에서 수정하면 밖/안에서 반영이 된다
    - `-e` : 환경변수를 지정하는 옵션. bash 상에서 `${}` 로 호출되는 변수들을 지정할 수 있다
    - `-p` : container 내부/외부 포트를 연결하는 포트포워딩 옵션. 
    ```sh
    docker run -p <host port>:<container port>/<network protocol>
    ```
    - `--ipc`: host와의 shared memory 설정 관련 옵션. 개인 pc에서는 host 사용 추천
    - `--name`: 만들어진 컨테이너의 이름 지정
    - `--help`: 사용법 설명. 여기에 포함되지 않는 명령어들 리스트와 간단한 사용법 포함

- `ps` : 현재 만들어져 실행중인 docker container의 리스트 띄우기
  - `-a` : 컨테이너 중 꺼져 있는 것들까지 확인
- `image` : 현재 host가 가지고 있는 docker image들을 관리하는 명령어 셋.
  - `ls` : image들의 목록 출력하기
  - `rm` : image 삭제
  - `prune` : dangling image들 삭제. cache나 업데이트되어 태그를 빼앗긴 이미지 등이 일반적으로 해당
- `pull` : dockerhub 등의 저장소에서 docker image를 다운받는 명령어
- `stop` : 실행 중인 container의 프로세스를 종료하는 명령어. 실제 컴퓨터를 끄는 것과 유사함. stop 이후에만 container의 삭제가 가능
- `start` : stopped container의 전원을 켜는 명령어
- `restart` : stop+start
- `exec` : 실행중인 컨테이너에서 명령어를 사용하는 데 쓰임. 대신 프로세스를 새로 띄우기 때문에, exec 명령어를 통해 실행한 프로세스가 끝나도 컨테이너가 꺼지지 않음. 예시:
```sh
docker exec -it containername bash
docker exec -it containername nvidia-smi
docker exec -d containername bash -c "cd /workspace && python test.py"
```
- `attach` : 컨테이너의 shell에 현재 명령창을 연결. 이 경우 `ctrl+D` 로 escape시 컨테이너가 꺼짐 주의.
- `cp` : 컨테이너와 호스트 사이에 파일을 주고받을 때 사용. 예시:
```sh
docker cp [호스트의 파일] [컨테이너 이름]:[이동할 장소]
```
- `logs` : docker container에 기록되어 있는 로그 출력. 주로 에러가 발생하여 컨테이너가 예상치 못하게 종료되었거나 detached mode에서 발생한 로그를 확인하는 데 사용. 예시:

```sh
docker logs Container name
```

## Docker Commands II (docker image 관련)

- 개발환경을 만드는 것 보다는, image 제작과 유통에 관련한 것들

<details>
<summary>Show contents</summary>
<div markdown="1">

### Docker build

- 기본적으로는 `docker build ` 라는 명령어를 통해 제작하게 됨
  - 이를 위해 Dockerfile이라는 이름의 블루프린트 요구

  - docker build 예시
  ```sh
  docker build -t <image tag> -f Dockerfile --no-cache .
  ```

### Others

- `save`: docker image를 tar 등의 확장자로 저장해서 공유
- `load` : docker save를 통해 저장된 파일을 다시 docker image로 제작
- `push` : docker image를 저장소로 업로드. 이후 docker pull 사용 가능
- `commit` : docker container로 만든 뒤에 발생한 수정 내용들을 반영한 docker image 제작

</div>
</details>

## 실습: Docker로 개발환경 만들기 : Jupyter

<details>
<summary>Show contents</summary>
<div markdown="1">

0. 실습용 directory 만들기
```sh
mkdir /home/${USER}/tutorial_workspace
cd /home/${USER}/tutorial_workspace
```
1. Docker image 다운받기

```sh
docker pull tensorflow/tensorflow:2.5.0-gpu-jupyter
```

2. Docker run을 통해 container 만들기

```sh
docker run -it --gpus all -v /home/${USER}/tutorial_workspace:/tf -p 8888:8888 --name tutorial_1 tensorflow/tensorflow:2.5.0-gpu-jupyter jupyter notebook --ip='*' --allow-root --NotebookApp.token='password'
```

3. 인터넷 브라우저를 통해 접속

- 주소: <localhost:8888>

- remote 환경이라면, 추가적인 포트포워딩 필요 (네트워크 관리자 문의)
  - 일시적인 해결책:
```sh
ssh -L <your_port>:localhost:8888 <your_id>@<your_IP> -p <your_port>
```

4. notebook 새로 만들어서 환경 체크하기
```py
import tensorflow as tf
def environment_info():

    sys_details = tf.sysconfig.get_build_info()
    cuda_version = sys_details["cuda_version"]
    cudnn_version=sys_details["cudnn_version"]
    print('tensorflow version: %s' % tf.__version__)
    print('CUDA version: %s' % cuda_version)
    print('CuDnn version: %s' % cudnn_version)
    print('Available devices: %s'%[x.name for x in tf.config.list_physical_devices()])
    return None
environment_info()
```
</div>
</details>

## 실습: NGC framework containers로 개발환경 구축하기

<details>
<summary>Show contents</summary>
<div markdown="1">

1. docker image 다운로드
```sh
docker pull nvcr.io/nvidia/pytorch:21.11-py3
```

2. Docker run을 통해 container 만들기
```sh
docker run -it --rm --gpus all --ipc=host -v /home/${USER}/tutorial_workspace:/workspace --name tutorial_2 nvcr.io/nvidia/pytorch:21.11-py3
```

3. vscode를 통해 컨테이너에 원격 접속

- remote-ssh를 통해 원격 창을 띄운 상태에서

- Command Pallette `Ctrl+Shift_P` 
  - Remote-Containers: Attach to Running Container
  - 이후 attach 하고자 하는 컨테이너 선택: tutorial_2

4. docker 안에서 tensorboard 실행

```sh
tensorboard --logdir <tb_path>
```

5. 인터넷 창에 연결하여 tensorboard 실행해 보기

</div>
</details>

## 실습: Dataset from NAS

<details>
<summary>Show contents</summary>
<div markdown="1">


1. docker image 다운로드
```sh
docker pull [image:tag]
```

2. 데이터셋을 풀어놓는 두 가지 방법

- `Docker run` 사용 
  - 비표준 데이터 컨테이너
    
```sh
docker run -d --rm -v <데이터를 복사하려는 로컬 경로>:/home/downdir/ [image:tag]
```

  - 표준 데이터 컨테이너

```sh
docker run -d --rm -v <데이터를 복사하려는 로컬 경로>:/data_out <image_name:tag>
```

  - `docker-compose` 사용 (표준 데이터 컨테이너)
    - 아래 명령어로 원하는 곳에 빈 `docker-compose.yml` 생성
    ```sh
    touch docker-compose.yml
    ```
    - 생성된 파일에 아래 내용 채워넣기 (image 부분 채워서)
    ```yml
    version: "3"
    services:
    data_unloader:
        platform: linux/amd64
        container_name: dataset_unloader
        image: <image_name:tag>
        volumes:
        - .:/data_out
        build: 
        context: .
        dockerfile: Dockerfile
    ```
    - 아래 명령어로 docker 실행
    ```yml
    docker-compose up
    ```

</div>
</details>
