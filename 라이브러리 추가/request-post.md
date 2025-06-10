# 🔍 requests 라이브러리를 사용해 POST 방식으로 HTTPBin에 요청 보내기

## 📌 코드 설명

```python
import requests
import json

def lambda_handler(event, context):
    url = "https://httpbin.org/post"           # 요청을 보낼 대상 URL
    payload = {"name": "test"}                 # POST 요청 Body에 포함할 데이터

    response = requests.post(url, json=payload)  # POST 요청 전송
    data = response.json()                       # 응답 데이터를 JSON으로 파싱

    print("API 응답:", data)                    # 응답 내용을 로그로 출력

    return {
        'statusCode': 200,
        'body': str(data)                       # 응답을 문자열로 변환해 반환
    }
```

---

## 📘 실습 2) request-post 설명 정리

| 코드 | 설명 |
|------|------|
| `payload = {"name": "test"}` | - 전송할 데이터 딕셔너리<br>- `POST` 요청의 **Body**에 JSON 형태로 포함됨 |
| `requests.post(url, json=payload)` | - `POST` 요청 전송<br>- `payload`를 JSON 문자열로 자동 변환해서 전송<br>- `Content-Type: application/json` 헤더도 자동 추가됨 |
| `data = response.json()` | - 응답(Response)의 Body에서 JSON 형식을 **Python 딕셔너리로 변환**<br>- 즉, JSON → dict |

---

## 🔄 흐름 요약

1. `payload`라는 데이터를 만든다 → `{"name": "test"}`
2. `requests.post()`로 HTTPBin에 `POST` 요청을 보낸다
3. HTTPBin은 요청을 그대로 응답(JSON)으로 돌려준다
4. 그 JSON 응답을 `.json()`으로 받아 Python 딕셔너리로 바꾼다
5. Lambda 함수의 반환값으로 전달한다 (`'statusCode': 200`과 함께)
