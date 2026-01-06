# EC2-kafka

**EC2-Kafka** 레포지토리는 **AWS EC2 환경에서 Kafka Broker 및 Zookeeper를 구동**하기 위한 설정을 포함합니다.  
Kafka는 실시간 데이터 파이프라인의 **중앙 메시징 허브** 역할을 수행하며,  
Airflow, Spark Streaming 등 다른 EC2 인스턴스들과 연동되어 데이터를 송수신합니다.

---

## Architecture Overview

- **역할**
  - Kafka Broker 운영
  - Zookeeper를 통한 Kafka 메타데이터 관리
- **배포 환경**
  - AWS EC2 (Private Subnet)
  - Docker & Docker Compose 기반 컨테이너 실행
- **연동 대상**
  - Airflow 및 scheduler (Producer)
  - Spark Streaming (Consumer / Producer)
  - 기타 내부 서비스

---

## Prerequisites

- Amazon Linux 기반 EC2
- 보안 그룹에서 Kafka Port (예: `9092`) 내부 통신 허용
- EC2 내부에서 Docker 실행 권한

---

## 실행 방법

### 1. EC2 내부에서 Git 설치 및 레포지토리 클론

```bash
sudo yum update -y
sudo yum install -y git

git clone https://github.com/DE07-Reborn/EC2-kafka.git
cd EC2-kafka
```

### 2. Docker 설치 및 서비스 활성화

```bash
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
```

### 3. Docker Compose 설치 

```bash
sudo mkdir -p /usr/local/lib/docker/cli-plugins

sudo curl -SL \
  https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-linux-x86_64 \
  -o /usr/local/lib/docker/cli-plugins/docker-compose

sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
```

### 4. Kafka 및 Zookeeper 컨테이너 실행

```bash
sudo docker compose up -d --build
```

---

## 컨테이너 구성 설명

| 컨테이너         | 역할                      |
| ------------ | ----------------------- |
| Zookeeper    | Kafka 메타데이터 및 브로커 상태 관리 |
| Kafka Broker | 메시지 송수신 및 토픽 관리         |

- Zookeeper는 Kafka 클러스터 상태를 관리합니다.
- kafka Broker는 실제 메시지를 저장하고 전달합니다.

---

## 운영 참고 사항
- kafka 서버는 단일 Broker 구성을 기준으로 설계되었습니다.
- Private Subnet 내부 통신을 전제로 되어있으며 외부 접근이 필요한 경우에는 Bastion 또는 Proxy를 통해 접근해야합니다.
- 로그 확인 :
  ```bash sudo docker compose logs -f ```









