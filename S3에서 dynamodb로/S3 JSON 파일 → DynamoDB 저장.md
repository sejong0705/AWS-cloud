# 📄 AWS Lambda 함수 설명: S3 JSON 파일 → DynamoDB 저장

## 🔧 개요

이 AWS Lambda 함수는 **S3에 저장된 JSON 파일을 읽고, 현재 시각(`timestamp`)을 추가한 후 DynamoDB에 저장**하는 기능을 수행합니다.  
간단한 ETL(Extract → Transform → Load) 역할을 하며, 정적 JSON 파일을 실시간 데이터로 변환하여 저장할 수 있습니다.

---

## 🧩 사용 기술 스택

- Python 3.x
- AWS Lambda
- AWS S3 (저장소)
- AWS DynamoDB (NoSQL DB)
- `boto3` (AWS SDK for Python)
- `datetime` (타임스탬프 생성)

## 📂 S3 데이터 구조 (예시)

S3에 저장된 JSON 파일 예시 (`dynamo_data/user_likes_data.json`):

```json
{
  "user_id": "haeri01",
  "product": "갤럭시워치",
  "brand": "삼성",
  "price": 40000,
  "color": "블랙"
}
```
## 코드설명
1. AWS 클라이언트 및 테이블 초기화
```
s3 = boto3.client('s3')
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('sgu-202537-user-likes-time')
```
2. S3 파일 위치 지정
```
bucket_name = 'sgu-202537-s3'
object_key = 'dynamo_data/user_likes_data.json'
```
3. S3에서 JSON 데이터 읽기 및 파싱
```
response = s3.get_object(Bucket=bucket_name, Key=object_key)
content = response['Body'].read().decode('utf-8')
data = json.loads(content)
```
4. 타임스탬프 추가
```
timestamp = datetime.now().isoformat()
data['timestamp'] = timestamp   

- 현재 시각을 ISO 포맷으로 기록

- 기존 JSON에 timestamp 필드 추가
```
5. DynamoDB에 데이터 저장
```
table.put_item(Item=data)
```

---

## 전체코드
```python
import json
import boto3
from datetime import datetime

def lambda_handler(event, context):
    # S3 클라이언트 및 DynamoDB 리소스 생성
    s3 = boto3.client('s3')
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('sgu-202537-user-likes-time')

    # S3 버킷과 오브젝트 키
    bucket_name = 'sgu-202537-s3'
    object_key = 'dynamo_data/user_likes_data.json'

    try:
        # S3에서 JSON 파일 읽기
        response = s3.get_object(Bucket=bucket_name, Key=object_key)
        content = response['Body'].read().decode('utf-8')
        data = json.loads(content)

        # 현재 타임스탬프 추가
        timestamp = datetime.now().isoformat()
        data['timestamp'] = timestamp

        # DynamoDB에 데이터 삽입
        table.put_item(Item=data)

        return {
            'statusCode': 200,
            'body': json.dumps({
                'message': 'DynamoDB insert successful!',
                'item': data
            }, ensure_ascii=False)
        }

    except Exception as e:
        return {
            'statusCode': 500,
            'body': json.dumps({
                'error': str(e)
            }, ensure_ascii=False)
        }


```
