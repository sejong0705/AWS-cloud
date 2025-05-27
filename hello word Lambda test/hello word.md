# 📄 AWS Lambda 함수 설명: 간단한 "Hello Lambda" 응답

## 🔧 개요

이 Lambda 함수는 AWS에서 실행될 때, 클라이언트에게 간단한 JSON 형식의 `"Hello Lambda"` 메시지를 **HTTP 200 OK 응답 코드와 함께 반환**합니다.  
외부 입력(`event`, `context`)과 무관하게 항상 동일한 응답을 반환하는 **기본 테스트용 템플릿 함수**입니다.

---

## 🧩 사용 기술 스택

- Python 3.x
- AWS Lambda
- JSON 응답 구조

---

## 📌 코드 설명

```python
import json
import json

def lambda_handler(event, context):
    # TODO implement
    return {
        'statusCode': 200,
        "statusDescription" : "200 OK",
        "isBase64Encoded" : False,
        "headers":{
            "Content-Type":"application/json"
        },
        "body": '{"message": "Hello Lambda "}'
    }
```
