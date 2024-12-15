---
layout: post
title: Ubuntu setting
marp: true
theme: gaia
size: 16:9
_class: lead
backgroundColor: #fdfdfd
backgroundImage: url('https://marp.app/assets/hero-background.svg')
---

# Ubuntu setting

**Install Ubuntu desktop 20.04**

---
<!-- paginate: true -->

## 기본 설정

- Ubuntu System Update and Upgrade
```bash
$ sudo apt update
$ sudo apt full-upgrade -y
```

---

- 기본 package 설치
Ubuntu 시스템에서 네트워크 관리 및 CAN 통신을 위한 도구와 라이브러리를 설치하는 데 사용

```bash
$ sudo apt install net-tools         # 네트워크 관련 도구 설치
$ sudo apt install openssh-server    # SSH 서버 설치
$ sudo apt install libzmq3-dev       # ZeroMQ 개발 라이브러리 설치
$ sudo apt install libsocketcan-dev  # SocketCAN 개발 라이브러리 설치
$ sudo apt install can-utils         # CAN 프로토콜 유틸리티 설치
```

---

- sudo 암호 입력 생략
```bash
$ sudo vi /etc/sudoers.d/$USER
```

- file 내용
```
$USER ALL=(ALL:ALL) NOPASSWD: ALL
```

---

- vcan 자동 실행

```
$ sudo vi /etc/systemd/system/vcan.service
```
file 내용은 다음 페이지 참조

- systemd service 활성화
```
$ sudo systemctl enable vcan.service
```

---

```
[Unit]
Description=Create multiple vcan interfaces
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ip link add dev vcan0 type vcan
ExecStart=/sbin/ip link set up vcan0
ExecStart=/sbin/ip link add dev vcan1 type vcan
ExecStart=/sbin/ip link set up vcan1
# 추가적인 vcan 인터페이스를 생성하려면 아래와 같이 추가
# ExecStart=/sbin/ip link add dev vcan2 type vcan
# ExecStart=/sbin/ip link set up vcan2
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

---

## Lenovo ThinkCentre TINY Neo 50Q Gen4
- **Intel Wi-Fi-6 AX201.11ax**

- Wi-Fi 설정

```bash
$ lspci -nnk | grep 0280 -A3
```
```
00:14.3 Network controller [0280]: Intel Corporation Device [8086:51f1] (rev 01)
	Subsystem: Intel Corporation Device [8086:0074]
00:15.0 Serial bus controller [0c80]: Intel Corporation Device [8086:51e8] (rev 01)
	Subsystem: Lenovo Device [17aa:333d]
```

---

```bash
$ sudo lshw -C network
```
```
  *-network UNCLAIMED       
       description: Network controller
       product: Intel Corporation
       vendor: Intel Corporation
       physical id: 14.3
       bus info: pci@0000:00:14.3
       version: 01
       width: 64 bits
       clock: 33MHz
       capabilities: pm msi pciexpress msix cap_list
       configuration: latency=0
       resources: iomemory:600-5ff memory:6001144000-6001147fff
  *-network
       description: Ethernet interface
       product: RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller
       ......
       resources: irq:18 ioport:3000(size=256) memory:50404000-50404fff memory:50400000-50403fff
```

---

## Docker

### 설치

- repository 업데이트 및 HTTPS 설치

```bash
$ sudo apt update
$ sudo apt upgrade -y

$ sudo apt install -y ca-certificates curl gnupg lsb-release
```

---

- docker GPG key 등록
```
$ sudo mkdir -p /etc/apt/keyrings

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

- repository 설정
```
$ echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

- docker 엔진 설치
```
$ sudo apt-get update

$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

- Version 확인
```
$ docker --version
Docker version 27.4.0, build bde2b89
```

---

```bash
$ sudo docker version

Client: Docker Engine - Community
 Version:           27.4.0
 API version:       1.47
 Go version:        go1.22.10
 Git commit:        bde2b89
 Built:             Sat Dec  7 10:38:58 2024
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          27.4.0
  API version:      1.47 (minimum version 1.24)
  Go version:       go1.22.10
  Git commit:       92a8393
  Built:            Sat Dec  7 10:38:58 2024
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.7.24
  GitCommit:        88bf19b2105c8b17560993bee28a01ddc2f97182
 runc:
  Version:          1.2.2
  GitCommit:        v1.2.2-0-g7cb3632
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

---

- sudo 없이 docker 명령어 사용
```bash
$ sudo usermod -aG docker $USER
```

- docker socket file 권한 설정
```bash
$ sudo chmod 666 /var/run/docker.sock
```

---

### docker-compose install

- 설치
```bash
$ sudo curl -L \
"https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" \
-o /usr/local/bin/docker-compose
```

- 권한 설정
```bash
$ sudo chmod +x /usr/local/bin/docker-compose
```

---

- 심볼릭 링크
```bash
$ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

- version 확인
```bash
$ docker-compose version
```
```
docker-compose version 1.24.1, build 4667896b
docker-py version: 3.7.3
CPython version: 3.6.8
OpenSSL version: OpenSSL 1.1.0j  20 Nov 2018
```

---

## COSMOS5

- Download
```bash
$ wget https://github.com/BallAerospace/COSMOS/archive/refs/tags/v5.0.5.tar.gz
```

- 압축 해제
```bash
$ tar xzf v5.0.5.tar.gz
```

---

- 환경 설정
```bash
$ cd COSMOS-5.0.5
$ vi .env
```

```
- COSMOS_TAG=5.0.5  # lasted -> 5.0.5 변경
- COSMOS_DEMO=0     # 삭제
```

---

- containter 생성
```bash
$ ./cosmos-control.sh build
```

build 시 오류로 중단되어도 run을 실행

- COSMOS5 실행
```bash
$ ./cosmos-control.sh run
```

- COSMOS5 정지
```bash
$ ./cosmos-control.sh stop
```

---

## UHD

- 관련 link
  - PySDR [6. USRP in Python](https://pysdr.org/content/usrp.html)
  - Ettus Research [Building and Installing UHD from source](https://files.ettus.com/manual/page_build_guide.html)
  - [USRP Hardware Driver (UHD) 설치 방법 feat.파이썬 라이브러리 경로 설정](https://m.blog.naver.com/akima95/222030723076)

---

### Installing UHD and Python API

```bash
$ sudo apt-get install git cmake libboost-all-dev libusb-1.0-0-dev \
    python3-docutils python3-mako python3-numpy python3-requests \
    python3-ruamel.yaml python3-setuptools build-essential
$ git clone https://github.com/EttusResearch/uhd.git
$ cd uhd
$ git checkout v4.6.0.0
$ cd host
$ mkdir build
$ cd build
$ cmake -DENABLE_TESTS=OFF -DENABLE_C_API=OFF -DENABLE_PYTHON_API=ON \
    -DENABLE_MANUAL=OFF ..
$ make -j8
$ sudo make install
$ sudo ldconfig
```

---

- uhd 권한 설정
```bash
$ cd uhd/host/utils
$ sudo cp uhd-usrp.rules /etc/udev/rules.d/
$ sudo udevadm control --reload-rules
$ sudo udevadm trigger
```

---

- python3 경로 설정
```bash
$ vi ~/.bashrc
```
```
export PYTHONPATH=$PYTHONPATH:/usr/local/lib/python3.8/site-packages
or
export PYTHONPATH=/usr/local/lib/python3.8/site-packages
```
파일 경로는 uhd library가 설치된 경로

- 변경된 설정 적용
```bash
$ source ~/.bashrc
```

---

- uhd image download
```bash
$ sudo /usr/local/bin/uhd_images_downloader
```

- Testing UHD Drivers and Python API
```bash
$ python3
```
```python
import uhd
usrp = uhd.usrp.MultiUSRP()
samples = usrp.recv_num_samps(10000, 100e6, 1e6, [0], 50)
print(samples[0:10])
```

---

## GomSpace ax-softmodem

- Ubuntu 20.04 이하 버전
  - ax-softmodem-2.1.1 사용
    - GLIBC, GLIBCXX version 문제로 2.1.2 사용 불가

- Ubuntu 22.04 이상 버전
  - ax-softmodem-2.1.2 사용
    - 2.1.1 version에서 frame_if 수정
