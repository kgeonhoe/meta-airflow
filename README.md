# Airflow + Spark + Zeppelin Docker 환경

이 프로젝트는 Docker를 사용하여 Apache Airflow, Apache Spark, Apache Zeppelin을 통합 환경으로 구성합니다.

## 🚀 시작하기

### 1. 프로젝트 클론 및 디렉토리 구조 생성

```bash
# 필요한 디렉토리 생성
mkdir -p {dags,logs,plugins,data,scripts,zeppelin/notebook,zeppelin/conf}

# 권한 설정 (Linux/macOS)
chmod 755 dags logs plugins data scripts
```

### 2. 환경 시작

```bash
# 컨테이너 빌드 및 시작
docker-compose up -d

# 로그 확인
docker-compose logs -f
```

### 3. 서비스 접속

- **Airflow Web UI**: http://localhost:8080
  - Username: `admin`
  - Password: `admin`
- **Spark Master UI**: http://localhost:8081
- **Zeppelin**: http://localhost:8082

## 📁 프로젝트 구조

```
.
├── docker-compose.yml          # 전체 서비스 구성
├── airflow/
│   ├── Dockerfile             # Airflow 커스텀 이미지
│   ├── requirements.txt       # Python 패키지
│   └── entrypoint.sh          # 초기화 스크립트
├── dags/                      # Airflow DAG 파일들
│   └── spark_example_dag.py   # 예제 DAG
├── scripts/                   # Spark 작업 스크립트
│   └── spark_job.py          # 예제 Spark 작업
├── data/                      # 공유 데이터 폴더
├── logs/                      # Airflow 로그
├── plugins/                   # Airflow 플러그인
├── zeppelin/
│   ├── notebook/             # Zeppelin 노트북
│   └── conf/                 # Zeppelin 설정
└── README.md
```

## 🔧 주요 기능

### 공유 볼륨
- `./data`: 모든 컨테이너에서 접근 가능한 데이터 폴더
- `./scripts`: Spark 작업 스크립트 폴더
- 컨테이너 간 파일 공유가 자동으로 이루어집니다

### Airflow에서 Spark 작업 실행
1. DAG에서 `SparkSubmitOperator` 사용
2. 스크립트는 `/scripts` 폴더에 저장
3. 데이터는 `/data` 폴더에서 공유

### 예제 워크플로우
1. Python으로 샘플 데이터 생성
2. Spark로 데이터 처리
3. 결과를 공유 폴더에 저장

## 📊 사용 예제

### Airflow DAG 실행
1. Airflow UI에서 `spark_example_dag` 찾기
2. DAG 활성화
3. 수동 실행 또는 스케줄 대기

### Zeppelin에서 Spark 사용
```scala
%spark
val df = spark.read.option("header", "true").csv("/opt/zeppelin/data/sample_data.csv")
df.show()
```

## 🛠 개발 가이드

### 새로운 Spark 작업 추가
1. `/scripts` 폴더에 Python 파일 생성
2. DAG에서 `SparkSubmitOperator`로 실행
3. 데이터는 `/data` 폴더 사용

### 커스텀 패키지 추가
1. `airflow/requirements.txt`에 패키지 추가
2. 컨테이너 재빌드: `docker-compose build airflow-webserver`

## 🔍 트러블슈팅

### 컨테이너 상태 확인
```bash
docker-compose ps
```

### 로그 확인
```bash
# 특정 서비스 로그
docker-compose logs spark-master
docker-compose logs airflow-webserver

# 실시간 로그
docker-compose logs -f
```

### 데이터베이스 초기화
```bash
docker-compose down -v
docker-compose up -d
```

## 📋 요구사항

- Docker & Docker Compose
- 최소 4GB RAM 권장
- 포트 8080, 8081, 8082 사용 가능

## 🔧 설정 변경

### Spark 워커 리소스 조정
`docker-compose.yml`에서 환경변수 수정:
```yaml
environment:
  - SPARK_WORKER_MEMORY=4g
  - SPARK_WORKER_CORES=4
```

### Airflow 설정 변경
`docker-compose.yml`의 환경변수 섹션에서 Airflow 설정 수정 가능# meta-airflow
