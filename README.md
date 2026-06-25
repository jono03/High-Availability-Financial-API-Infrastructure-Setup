# 🏦 High-Availability Financial API & Infrastructure Setup

> **동시 요청 1,000건(1,000 RPS) 상황에서의 안정성 확보를 위한 모의 금융 거래 API 및 고가용성 클라우드 인프라 구축 프로젝트**

---

## 📌 Project Overview
본 프로젝트는 화려한 신기술이나 프론트엔드 기능에 집중하기보다, 금융 IT의 본질인 **'안정성과 보안, 그리고 트래픽 제어'**를 깊게 연구하기 위해 기획된 백엔드/인프라 중심의 프로젝트입니다.

금융 보안원의 가이드라인을 참고하여 클라우드 상에서 **외부망과 내부망을 완전히 격리(VPC/Subnet 망분리)**하고, 동시 사용자 1,000명이 한꺼번에 송금 API를 연타하는 극한의 상황을 시뮬레이션하여 데이터 무결성과 시스템 가용성을 정량적으로 검증합니다.

---

## 🛠️ Tech Stack

| 분류 | 기술 스택 |
| :--- | :--- |
| **Backend** | Java, Spring Boot (or Python, FastAPI) |
| **Database** | MySQL / PostgreSQL |
| **Infrastructure** | AWS (VPC, Public/Private Subnet, EC2, ALB, RDS) |
| **DevOps & Test** | Docker, Locust (or Artillery) |

---

## 🏗️ System Architecture
[ Internet ]
                          │ (HTTPS / 443)
                          ▼
                 [ Public Subnet ]
            ┌──────────────────────────┐
            │ Application Load Balancer│
            └─────────────┬────────────┘
                          │ (Round Robin / Internal)
           ┌──────────────┴──────────────┐
           ▼                             ▼
   [ Private Subnet 1 ]          [ Private Subnet 2 ]
┌──────────────────────────┐  ┌──────────────────────────┐
│  Backend Server (EC2) A  │  │  Backend Server (EC2) B  │
│     [Docker Container]   │  │     [Docker Container]   │
└────────────┬─────────────┘  └────────────┬─────────────┘
│                             │
└──────────────┬──────────────┘
▼
[ Data Tier Subnet ]
┌──────────────────────────┐
│   Database Server (RDS)  │
└──────────────────────────┘


---

## ⚡ Key Implementation Points

### 1. 금융권 표준 망분리 인프라 (Network Isolation)
* 외부 인터넷과 직접 통신하는 구역(Public Subnet)에는 **로드 밸런서(ALB)**만 배치하여 시스템 노출을 최소화했습니다.
* 실제 API가 동작하는 애플리케이션 서버와 데이터베이스는 외부 접근이 원천 차단된 **내부망(Private Subnet)**에 격리하고, 엄격한 방화벽 규칙(Security Group)을 적용했습니다.

### 2. 동시성 제어 및 데이터 무결성 (Concurrency & Integrity)
* 동시 다발적인 송금 및 잔액 조회 요청 시 발생할 수 있는 **경쟁 상태(Race Condition)**를 방지하기 위해 DB 트랜잭션 격리 수준을 최적화했습니다.
* 비관적 락(Pessimistic Lock) 혹은 낙관적 락(Optimistic Lock) 메커니즘을 적용하여 시스템 부하 상황에서도 단 1원의 오차도 없는 데이터 정밀성을 확보했습니다.

### 3. 동시 접속자 1,000명 부하 테스트 (Load Testing)
* 실제 유저 마케팅 대신, 파이썬 기반 부하 테스트 도구인 **Locust**를 활용하여 가상 사용자 1,000명이 동시에 접속하는 임계치 테스트를 수행했습니다.
* 트래픽이 몰릴 때의 CPU 사용량, 메모리 누수, 커널 소켓 고갈 현상을 모니터링하고 시스템 병목을 해결하는 트러블슈팅 과정을 정량적으로 기록합니다.

---

## 📈 Load Test Results (Tuned Output)
*조금씩 프로젝트를 진행하면서 아래 지표들을 실제 측정 값으로 채워나갈 예정입니다.*

* **Target Traffic:** 1,000 Users (Virtual Clients) / Target 1,000 RPS
* **Average Response Time:** `[측정 예정]` ms
* **Error Rate:** `[측정 예정]` %
* **Key Troubleshooting:** * 트래픽 집중 시 발생하는 DB Connection Pool 고갈 현상 해결 과정 기재 예정
  * 로드 밸런싱 최적화를 통한 CPU 부하 분산 로그 기재 예정
