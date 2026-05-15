# Linear CCTV Docker Development Environments

이 저장소는 Linear CCTV 프로젝트에서 사용하는 공통 Docker 개발 환경을 관리하기 위한 공간입니다.

각 하위 폴더는 실제 애플리케이션 소스가 아니라, 해당 영역을 개발할 때 사용할 Docker 기반 개발 환경을 정의합니다. 협업자는 필요한 영역의 Dockerfile을 빌드한 뒤 컨테이너에 진입해서 동일한 OS 및 패키지 환경에서 개발할 수 있습니다.

## Directory Structure

```text
Docker-env/
├── cctv-app/
│   └── Dockerfile
├── cctv-bsp/
│   └── Dockerfile
├── vms-server/
│   └── Dockerfile
└── README.md
```

## Environment Roles

### vms-server

`vms-server`는 VMS 서버 개발을 위한 Docker 개발 환경입니다.

현재 Ubuntu 24.04 기반이며, 서버 개발에 필요한 주요 패키지를 포함합니다.

- gSOAP
- Nginx
- PostgreSQL
- GStreamer
- GStreamer RTSP Server
- OpenSSL
- C/C++ 빌드 도구

기본으로 노출하는 포트는 다음과 같습니다.

- `80`: Nginx HTTP
- `5432`: PostgreSQL
- `8554`: RTSP media server

### cctv-app

`cctv-app`은 CCTV 애플리케이션 개발을 위한 Docker 개발 환경입니다.

현재는 Ubuntu 24.04 기반 Dockerfile만 준비되어 있으며, 애플리케이션 개발에 필요한 패키지는 프로젝트 진행에 맞춰 추가합니다.

### cctv-bsp

`cctv-bsp`는 CCTV BSP 개발을 위한 Docker 개발 환경입니다.

현재는 Ubuntu 24.04 기반 Dockerfile만 준비되어 있으며, 보드 지원 패키지, 크로스 컴파일러, SDK 등은 프로젝트 진행에 맞춰 추가합니다.

## Requirements

개발자 PC에는 Docker가 설치되어 있어야 합니다.

```bash
docker --version
```

## Build Images

명령은 이 README가 있는 `Docker-env` 루트 디렉터리에서 실행합니다.

### vms-server

```bash
docker build -t linear-cctv-vms-server-dev ./vms-server
```

### cctv-app

```bash
docker build -t linear-cctv-cctv-app-dev ./cctv-app
```

### cctv-bsp

```bash
docker build -t linear-cctv-cctv-bsp-dev ./cctv-bsp
```

## Run Containers

현재 Dockerfile들은 개발 쉘 진입을 기본 목적으로 하며, 컨테이너 시작 시 자동으로 서버 프로세스를 실행하지 않습니다. 컨테이너에 진입한 뒤 필요한 서비스를 직접 실행해서 사용합니다.

### vms-server

```bash
docker run -it --rm \
  --name linear-cctv-vms-server-dev \
  -p 8080:80 \
  -p 5432:5432 \
  -p 8554:8554 \
  linear-cctv-vms-server-dev
```

컨테이너 내부에서 필요한 경우 서비스를 실행합니다.

```bash
service postgresql start
service nginx start
```

호스트 브라우저에서 Nginx를 확인할 때는 다음 주소를 사용합니다.

```text
http://localhost:8080
```

PostgreSQL은 호스트의 `5432` 포트로 연결할 수 있습니다.

```bash
psql -h localhost -p 5432 -U postgres
```

### cctv-app

```bash
docker run -it --rm \
  --name linear-cctv-cctv-app-dev \
  linear-cctv-cctv-app-dev
```

### cctv-bsp

```bash
docker run -it --rm \
  --name linear-cctv-cctv-bsp-dev \
  linear-cctv-cctv-bsp-dev
```

## Mount Local Source Code

실제 개발 소스코드를 컨테이너 안에서 함께 사용하려면 볼륨 마운트를 사용합니다.

예시는 현재 작업 디렉터리를 컨테이너의 `/workspace`에 연결합니다.

```bash
docker run -it --rm \
  --name linear-cctv-vms-server-dev \
  -p 8080:80 \
  -p 5432:5432 \
  -p 8554:8554 \
  -v "$PWD":/workspace \
  -w /workspace \
  linear-cctv-vms-server-dev
```

## Development Notes

- Dockerfile 수정 후에는 이미지를 다시 빌드해야 변경 사항이 반영됩니다.
- 공통 개발 환경에 필요한 패키지는 각 폴더의 Dockerfile에 추가합니다.
- 서비스 자동 실행이 필요해지면 `CMD` 또는 `ENTRYPOINT`를 별도 스크립트로 정리합니다.
- 여러 서비스를 함께 띄우는 방식이 필요해지면 Docker Compose 구성을 추가하는 것을 권장합니다.

## Quick Start

VMS 서버 개발 환경을 가장 빠르게 확인하려면 아래 명령을 사용합니다.

```bash
docker build -t linear-cctv-vms-server-dev ./vms-server
docker run -it --rm \
  --name linear-cctv-vms-server-dev \
  -p 8080:80 \
  -p 5432:5432 \
  -p 8554:8554 \
  linear-cctv-vms-server-dev
```
