# 📄 AWS Lambda 함수 설명: S3에 텍스트 파일 업로드

## 🔧 개요

이 Lambda 함수는 **실행 시점의 날짜와 시간을 포함한 텍스트 파일을 자동으로 생성하여 S3 버킷에 업로드**하는 기능을 수행합니다.  
업로드된 파일에는 `"Hello Sejong!"`이라는 문구가 포함됩니다.

---

## 🧩 사용 기술 스택

- Python 3.x
- AWS Lambda
- AWS S3 (`boto3` 사용)
- `datetime` 모듈

---

## 📂 파일 저장 구조

- **버킷 이름:** `sgu-202537-s3`
- **파일 경로(prefix):** `upload/`
- **파일명 형식:** `hello_YYYY-MM-DD_HH-MM-SS.txt`

코드
```python
import json
import boto3 # AWS 서비스(S3 등)를 사용하기 위한 SDK
from datetime import datetime

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    bucket_name = 'sgu-202537-s3'
    prefix = 'upload/'

    now = datetime.now().strftime('%Y-%m-%d_%H-%M-%S')
    filename = f'{prefix}hello_{now}.txt'
    content = f'Hello Sejong! This file was created at {now}'

    # s3파일 업로드
    s3.put_object(
        Bucket=bucket_name,
        Key=filename,
        Body=content.encode('utf-8')
    )
    return {
    "statusCode": 200,
    "statusDescription": "200 OK",
    "isBase64Encoded": False,
    "headers":{
        "Content-Type":"application/json"
    },
    "body": '{"message": "업로드 완료"}' 
}
```
## 출력 예시
```text
Hello Sejong! This file was created at 2025-05-27_15-30-00
