---
layout: post
title: "DuckDB로 60TB 데이터를 16GB RAM에서 처리하기"
date: 2026-01-07
tags: [데이터처리, DuckDB, Parquet, 최적화]
---

## 들어가며

5G RAN KPI 예측 프로젝트에서 60TB 규모의 데이터를 16GB RAM 환경에서 처리한 경험을 공유합니다.

## 문제 상황

- 압축 해제 시 **60TB**에 달하는 대용량 데이터
- 개발 환경: **16GB RAM**, **1TB 스토리지**
- Pandas로는 메모리 부족(Out of Memory) 에러 발생

## 해결 방법

### 1. DuckDB 도입

Pandas 대신 DuckDB를 사용하여 필요한 데이터만 메모리에 로드:

```python
import duckdb

# CSV 파일에서 필요한 컬럼만 선택
conn = duckdb.connect()
query = """
    SELECT cell_id, timestamp, prb, cqi, throughput
    FROM read_csv_auto('data/*.csv')
    WHERE cell_id = '12345'
"""
df = conn.execute(query).df()
```

**효과:**
- 메모리 사용량 **80% 감소** (80GB → 16GB)
- Lazy Evaluation으로 쿼리 실행 시점 최적화

### 2. Parquet 포맷 변환

CSV를 Parquet 포맷으로 변환하여 저장 공간과 읽기 속도 개선:

```python
import pandas as pd

# CSV를 Parquet로 변환
df = pd.read_csv('data.csv')
df.to_parquet('data.parquet', compression='snappy')
```

**효과:**
- 저장 공간 **86% 감소** (60TB → 8.4TB)
- 읽기 속도 **5배 이상 향상**

### 3. 청크 단위 배치 처리

전체 데이터를 Cell 단위로 분할하여 순차 처리:

```python
def process_cell(cell_id):
    query = f"""
        SELECT * FROM read_parquet('data/*.parquet')
        WHERE cell_id = '{cell_id}'
    """
    df = conn.execute(query).df()
    # 데이터 처리
    process_data(df)
    # 메모리 해제
    del df

for cell_id in cell_ids:
    process_cell(cell_id)
```

## 결과

- 제한된 리소스에서 **60TB 데이터 전처리 성공**
- 메모리 사용량 **80% 감소**
- 데이터 로딩 속도 **5배 향상**

## 배운 점

익숙한 도구(Pandas)에만 의존하지 않고, 문제에 맞는 적절한 도구(DuckDB)를 선택하는 것이 중요하다는 것을 배웠습니다.
