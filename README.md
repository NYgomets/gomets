# Gomets

AI 기반 Incident Context Generator

Spring Boot 애플리케이션 장애 분석을 위한 운영 컨텍스트 수집 도구

---

## 프로젝트 소개

Gomets는 Spring Boot 애플리케이션 운영 중 발생한 장애를 분석하기 위해 필요한 정보를 자동으로 수집하고, AI가 이해하기 쉬운 형태의 Incident Report를 생성하는 데스크톱 도구이다.

Gomets는 Grafana, Prometheus, Datadog, New Relic 같은 모니터링 솔루션을 대체하려는 프로젝트가 아니다.

또한 장애를 자동으로 복구하거나 수정하는 운영 자동화 도구도 아니다.

Gomets의 목적은 장애 발생 시 여러 곳에 흩어진 운영 정보를 하나로 모아 분석 가능한 형태로 정리하는 것이다.

사용자는 생성된 Incident Report를 ChatGPT, Claude, Gemini, Ollama 등 원하는 AI에 전달하여 원인 분석에 활용할 수 있다.

---

# 왜 만들었는가

기존에는 AWS EC2 환경에서 Spring Boot 서비스를 운영했다.

처음에는 단순히 EC2 한 대만 사용했기 때문에 큰 부담이 없었다.

하지만 서비스를 운영할수록 RDS, 로그 수집, 모니터링 등 운영을 위한 부가 비용이 지속적으로 증가했다.

개인 프로젝트 규모에서는 이러한 비용이 부담스럽게 느껴졌고, 결국 운영 환경을 개인 서버(Home Server) 기반 Docker 환경으로 이전하게 되었다.

홈서버로 이전하면서 인프라 비용은 크게 줄일 수 있었지만 새로운 문제가 생겼다.

CloudWatch와 같은 관리형 서비스가 제공하던 편의성이 사라졌고, 장애가 발생하면 여러 도구를 오가며 정보를 수집해야 했다.

예를 들어 서비스에 문제가 발생하면 보통 다음과 같은 과정을 거친다.

장애 발생

↓

docker logs 확인

↓

컨테이너 상태 확인

↓

DB Connection Pool 상태 확인

↓

필요 시 JVM 상태 확인

↓

상황 정리

↓

AI에게 설명

↓

원인 분석

특히 ChatGPT, Claude 같은 AI를 활용한 문제 분석이 일상이 되면서 새로운 사실을 깨달았다.

원인 분석 자체보다 AI가 상황을 이해할 수 있도록 로그와 상태 정보를 수집하고 정리하는 데 더 많은 시간을 사용하고 있었던 것이다.

실제로 필요했던 것은 또 다른 모니터링 도구가 아니었다.

장애 분석에 필요한 정보를 한 번에 수집하고 AI가 이해하기 쉬운 형태로 정리해주는 도구였다.

Gomets는 바로 이 문제를 해결하기 위해 만들어졌다.

---

# 핵심 목표

기존 방식

장애 발생

↓

여러 도구에서 정보 수집

↓

상황 정리

↓

AI에게 설명

↓

원인 분석

Gomets

장애 발생

↓

Incident Report 생성

↓

AI에 붙여넣기

↓

원인 분석

---

# 핵심 철학

## 기계적인 수집은 자동화한다

자동 수집 대상

* 애플리케이션 상태
* JVM 상태
* HikariCP 상태
* Docker 상태
* 예외 로그
* 운영 컨텍스트

## 판단은 사용자가 수행한다

사용자가 직접 결정한다.

* 실제 장애 여부
* 원인 확정
* 수정 방향 결정
* 재배포 여부

## AI는 선택 사항이다

Gomets는 특정 AI 서비스에 종속되지 않는다.

사용자는 원하는 AI를 사용할 수 있다.

* ChatGPT
* Claude
* Gemini
* Ollama
* 기타 LLM

## 자동 복구는 하지 않는다

Gomets는 다음 기능을 제공하지 않는다.

* 자동 수정
* 자동 재배포
* 자동 재시작
* 자동 장애 복구
* 자동 스케일링

---

# 주요 기능

## 1. Incident Context Generator

Gomets의 핵심 기능.

장애 분석에 필요한 정보를 자동 수집하여 하나의 분석 컨텍스트를 생성한다.

수집 대상

### Application

* Health Status
* Active Profile
* Startup Time
* Application Version

### JVM

* Heap Used
* Heap Max
* Non Heap Memory
* Thread Count
* Peak Thread Count
* GC Count
* GC Pause Time
* Process CPU Usage

### HikariCP

* Active Connections
* Idle Connections
* Pending Connections
* Max Pool Size
* Pool Usage Rate

### Docker

* Container Status
* Running Time
* Restart Count
* Health Status

### Logs

* Exception
* Stack Trace
* Caused By Chain
* 최근 로그
* 예외 전후 컨텍스트

---

## 2. 로그 수집 및 예외 분석

Gomets는 ERROR 로그를 장애로 간주하지 않는다.

예를 들어 다음과 같은 경우도 ERROR로 기록될 수 있다.

* Broken Pipe
* Client Abort Exception
* Validation Exception
* 404 Error

따라서 ERROR는 Incident Candidate로 기록되며 실제 장애 여부는 사용자가 판단한다.

수집 정보

* Exception
* Stack Trace
* Caused By Chain
* 이전 30줄
* 이후 10줄

---

## 3. Incident Report 생성

수집된 정보를 AI가 이해하기 쉬운 형태로 정리한다.

예시

```text
=== INCIDENT REPORT ===

Timestamp:
2026-06-28 21:32:11

Application:
order-service

Health:
DOWN

Container:
order-service

Container Status:
unhealthy

Restart Count:
3

Heap:
412MB / 512MB

Threads:
241

CPU:
92%

Hikari:
Active 20/20
Pending 14

Exception:
SQLTransientConnectionException

Caused By:
Communications link failure

Recent Logs:
...

Analysis Request:

위 상황을 바탕으로
가장 가능성이 높은 원인과
추가로 확인해야 할 사항을 분석해줘.
```

생성된 Report는 한 번의 클릭으로 클립보드에 복사할 수 있다.

---

# MVP 범위

초기 버전 제공 기능

* SSH 기반 서버 접속
* Docker 상태 조회
* Spring Actuator 연동
* JVM 상태 조회
* HikariCP 상태 조회
* 로그 조회
* Incident Report 생성
* 클립보드 복사
* AI 전달용 Prompt 생성

---

# Non Goals

초기 버전에서는 지원하지 않는다.

* 자동 수정
* 자동 재배포
* 자동 재시작
* 자동 장애 복구
* Kubernetes 운영
* Heap Dump 분석
* Thread Dump 분석
* GC Log 분석
* Distributed Tracing
* APM 대체

---

# 향후 계획

## 운영 분석

* Thread Dump 수집
* GC Log 수집
* Heap Dump 수집
* JFR 수집

## 인프라

* Redis 상태 분석
* 다중 서버 지원
* 다중 컨테이너 지원

## AI

* Ollama 연동
* Incident Report 자동 요약
* 장애 유형 자동 분류
* AI Prompt Template 제공

---

# 기술 스택

## Backend

* Java 25
* Spring Boot

## Frontend

* React
* TypeScript
* Vite

## UI

* Tailwind CSS
* shadcn/ui

## SSH

* SSHJ

## Monitoring

* Spring Boot Actuator
* Micrometer

## Container

* Docker Engine API

## Storage

* SQLite

---

# 프로젝트 상태

현재 MVP 설계 단계.

Gomets의 목표는 더 많은 지표를 수집하는 것이 아니다.

장애가 발생했을 때 개발자가 AI에게 상황을 설명하기 위해 소비하는 시간을 줄이는 것이다.

즉, Gomets는 모니터링 도구가 아니라 AI 기반 장애 분석을 위한 Incident Context Generator이다.
