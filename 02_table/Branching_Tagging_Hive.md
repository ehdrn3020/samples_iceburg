## Branching and Tagging
### 개념
```declarative
Iceberg는 Git처럼 데이터 버전 관리를 한다고 보면 된다.
- Snapshot → 데이터 변경 시점 (commit)
- Branch → 움직이는 포인터 (개발 브랜치)
- Tag → 고정된 포인터 (릴리즈 버전)

구조
main branch → snapshot 100 → snapshot 101 → snapshot 102
    ↘ branch(test) → snapshot 103 → snapshot 104
tag(v1) → snapshot 101 (고정)
```

### Snapshot
```declarative
// 데이터 insert하면 자동 생성됨
INSERT INTO nyc.taxis VALUES (...);
>>> 내부적으로 snapshot_id 100 생성
```

### Branch
```declarative
// 생성
ALTER TABLE nyc.taxis CREATE BRANCH test_branch;

// 쓰기
INSERT INTO nyc.taxis.branch_test_branch
VALUES (999, 10.0, 50.0, 'N', 1);

// 조회
SELECT * FROM nyc.taxis.branch_test_branch;

// main 과 비교
-- main
SELECT * FROM nyc.taxis;

-- test branch
SELECT * FROM nyc.taxis.test_branch;
```

### Tag
```declarative
// 현재 snapshot을 v1으로 고정
ALTER TABLE nyc.taxis CREATE TAG v1;

SELECT * FROM nyc.taxis.tag_v1;
```

### SnapShot ID
```declarative
SELECT * FROM nyc.taxis.refs;
SELECT * FROM nyc.taxis.snapshots;
```

### Rollback
```declarative
- Hive에서는 snapshot_id 기반 롤백이 안 된다 (거의 불가능)
- Hive는 Iceberg snapshot을 “볼 수만 있고 컨트롤은 못한다”
- Rollback은 Spark/Trino에서 해야 함
```