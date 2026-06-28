# Gomets

개인 개발자를 위한 Spring Boot 운영 및 장애 분석 콘솔

Gomets는 Spring Boot 애플리케이션 운영 중 발생하는 장애의 원인 분석을 돕기 위한 데스크톱 도구이다.

Gomets는 장애를 자동으로 수정하지 않는다.

대신 로그, JVM 상태, 데이터베이스 상태, Docker 상태를 자동으로 수집하여 사용자가 빠르게 원인을 파악할 수 있도록 지원한다.

AI는 선택 사항이다.

사용자는 생성된 Incident Report를 ChatGPT, Claude, Gemini, Ollama 등 원하는 AI에 전달하여 분석할 수 있다.

---

## 핵심 목표

반복적인 장애 분석 준비 작업을 자동화한다.

기존

장애 발생
→ 로그 확인
→ JVM 상태 확인
→ DB 상태 확인
→ Docker 상태 확인
→ AI에게 설명
→ 원인 분석

Gomets

장애 발생
→ Incident Report 생성
→ AI 분석
→ 원인 파악

---

## 핵심 철학

기계적인 수집은 자동화한다.

* 로그 수집
* JVM 상태 수집
* DB 상태 수집
* Docker 상태 수집
* Incident Report 생성

판단은 사용자가 수행한다.

* 장애 여부 판단
* 원인 확정
* 수정 방향 결정
* 재배포 여부 결정

자동 수정, 자동 재배포, 자동 복구는 지원하지 않는다.

---

## 주요 기능

### 1. Spring Boot 상태 조회

수집 정보

* Health Status
* Active Profile
* Startup Time

---

### 2. JVM 상태 조회

수집 정보

* Heap Used
* Heap Max
* Non Heap Memory
* Thread Count
* Peak Thread Count
* GC Count
* GC Pause Time
* Process CPU Usage

---

### 3. HikariCP 상태 조회

수집 정보

* Active Connections
* Idle Connections
* Pending Connections
* Max Pool Size

---

### 4. Docker 상태 조회

수집 정보

* Container Status
* Restart Count
* Health Status

---

### 5. 로그 분석

수집 정보

* Exception
* Stack Trace
* Caused By Chain
* 이전 30줄
* 이후 10줄

ERROR는 장애로 간주하지 않는다.

ERROR는 Incident Candidate로 기록된다.

---

### 6. Incident Report 생성

자동 포함

* Timestamp
* Host
* Container
* Exception
* JVM 상태
* DB 상태
* Docker 상태
* 관련 로그

생성된 Incident Report는 클립보드로 즉시 복사 가능하다.

---

## MVP 범위

* SSH 접속
* Docker 상태 조회
* Spring Actuator 연동
* JVM 상태 조회
* HikariCP 상태 조회
* 로그 조회
* Incident Report 생성
* 클립보드 복사

---

## 명시적 비목표 (Non Goals)

지원하지 않음

* 자동 수정
* 자동 재배포
* 자동 재시작
* 자동 장애 복구
* Kubernetes 운영
* Heap Dump 분석
* Thread Dump 분석
* GC Log 분석

---

## 향후 계획

* Thread Dump 수집
* GC Log 수집
* Heap Dump 수집
* Redis 상태 분석
* 다중 서버 지원
* Ollama 연동
* JFR 분석
