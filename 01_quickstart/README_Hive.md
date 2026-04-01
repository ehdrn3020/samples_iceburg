## Hive
### Docker로 Hive 4 실행
```declarative
Hive + Iceberg Quickstart 예제, 
Hive 4 Docker 컨테이너를 띄운 뒤 
Beeline으로 접속해서 Iceberg 테이블을 생성/조회한다. 
Hive 4.0.0 이상은 Iceberg가 포함되어 있어서 추가 jar 없이 시작할 수 있다.

apache/hive 이미지를 사용해 HiveServer2를 띄우며, 
embedded Derby metastore를 사용한다.

docker pull apache/hive:4.0.0

docker run -d \
-p 10000:10000 \
-p 10002:10002 \
--env SERVICE_NAME=hiveserver2 \
--name hive4 \
apache/hive:4.0.0
```

### Beeline으로 HiveServer2 접속
```declarative
docker exec -it hive4 beeline -u 'jdbc:hive2://localhost:10000/'
```

### Iceberg DB / 테이블 생성
```declarative
CREATE DATABASE nyc;

CREATE TABLE nyc.taxis
(
  trip_id bigint,
  trip_distance float,
  fare_amount double,
  store_and_fwd_flag string
)
PARTITIONED BY (vendor_id bigint)
STORED BY ICEBERG;

- nyc: 데이터베이스
- taxis: 테이블명
- vendor_id: 파티션 컬럼
- STORED BY ICEBERG: Hive 테이블이 Iceberg 방식으로 관리됨
```

### 데이터 INSERT
```declarative
INSERT INTO nyc.taxis
VALUES
  (1000371, 1.8, 15.32, 'N', 1),
  (1000372, 2.5, 22.15, 'N', 2),
  (1000373, 0.9, 9.01, 'N', 2),
  (1000374, 8.4, 42.13, 'Y', 1);
```

### 데이터 조회
```declarative
SELECT * FROM nyc.taxis;
+----------+---------------+--------------+---------------------+------------+
| trip_id  | trip_distance | fare_amount  | store_and_fwd_flag  | vendor_id  |
+----------+---------------+--------------+---------------------+------------+
| 1000371  | 1.8           | 15.32        | N                   | 1          |
| 1000372  | 2.5           | 22.15        | N                   | 2          |
| 1000373  | 0.9           | 9.01         | N                   | 2          |
| 1000374  | 8.4           | 42.13        | Y                   | 1          |
+----------+---------------+--------------+---------------------+------------+

DESCRIBE FORMATTED nyc.taxis;
```