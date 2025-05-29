# 📄 AWS Lambda 함수 설명: 사용자 상품 정보 저장

## 🔧 개요

이 Lambda 함수는 HTTP 요청의 `body`에 담긴 사용자 상품 정보를 **DynamoDB 테이블에 저장**합니다.  
필수값(`user_id`, `product`)과 함께 선택적으로 `reason`, `brand`, `price`, `color` 등의 추가 정보를 받아 저장합니다.

---

## 🧩 사용 기술 스택

- Python 3.x
- AWS Lambda
- AWS DynamoDB (`boto3` 사용)
- JSON 요청/응답 처리
- `datetime` 모듈

---

## 📥 입력 형식 (Request Body)

```json
{
  "user_id": "haeri01",
  "product": "갤럭시워치",
  "reason": "예쁘고 유용해서",
  "brand": "삼성",
  "price": 40000,
  "color": "블랙"
}
```
## 코드 설명

- user_id, product는 필수
- reason, brand, price, color는 선택

1. 요청 데이터 파싱
   
```python
body = json.loads(event['body'])
user_id = body['user_id']
product = body['product']

```
2. 선택필드 처리
```python
reason = body.get('reason')
brand = body.get('brand')
price = body.get('price')
color = body.get('color')
```
3. dynamodb 저장
```
table = boto3.resource('dynamodb').Table('sgu-202537-user-likes-time')
table.put_item(Item=item)
```

## 전체 코드
```python
import boto3
import json
from datetime import datetime

def lambda_handler(event, context):
    try:
        body = json.loads(event['body'])
        user_id = body['user_id']
        product = body['product']
        timestamp = datetime.now().isoformat()

        reason = body.get('reason')
        brand = body.get('brand')
        price = body.get('price')
        color = body.get('color')

        item = {
            'user_id': user_id,
            'timestamp': timestamp,
            'product': product
        }

        if reason: item['reason'] = reason
        if brand: item['brand'] = brand
        if price: item['price'] = price
        if color: item['color'] = color

        table = boto3.resource('dynamodb').Table('sgu-202537-user-likes-time')
        table.put_item(Item=item)

        return {
            'statusCode': 200,
            'body': json.dumps({
                'message': '저장완료',
                'user_id': user_id,
                'timestamp': timestamp
            }, ensure_ascii=False)
        }

    except Exception as e:
        return {
            'statusCode': 400,
            'body': json.dumps({'error': str(e)}, ensure_ascii=False)
        }

```
