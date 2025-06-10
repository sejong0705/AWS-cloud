# 📦 AWS Lambda에서 emoji 라이브러리 사용하기

## 🧩 개요

AWS Lambda는 기본적으로 외부 Python 패키지(예: `emoji`)를 포함하지 않기 때문에, **직접 설치한 라이브러리를 Layer로 등록**해야 사용할 수 있습니다. 이 문서에서는 `emoji` 패키지를 사용하기 위한 전체 절차를 설명합니다.

---

## 1️⃣ 문제 상황: 라이브러리 미포함 Lambda 코드 실행 실패

```python
import emoji

def lambda_handler(event, context):
    text = "AWS Lambda is awesome! :rocket:"
    result = emoji.emojize(text, language='alias')
    print(result)
    return {
        'statusCode': 200,
        'body': result
    }
```

- ❌ 위 코드는 로컬에서는 정상 실행되지만, Lambda에서는 다음과 같은 에러 발생:

```json
{
  "errorMessage": "Unable to import module 'lambda_function': No module named 'emoji'",
  "errorType": "Runtime.ImportModuleError"
}
```

---

## 2️⃣ 해결 단계 1: 로컬에서 라이브러리 설치

### ✅ 명령어 실행
```bash
py -3.13 -m pip install emoji -t python/
```

- `python/` 폴더 안에 `emoji` 관련 파일들이 설치됨
- 설치 완료 후 폴더 구조 예시:
```
aws_3c/
└── python/
    ├── emoji/
    └── emoji-2.14.1.dist-info/
```

---

## 3️⃣ 해결 단계 2: zip으로 압축

- `python/` 폴더를 통째로 압축 → `python.zip` 파일 생성

```
python/
├── emoji/
├── emoji-2.14.1.dist-info/
→ 이 전체를 zip으로 압축
```

---

## 4️⃣ 해결 단계 3: Lambda Layer 생성 (AWS Console)

### 경로
```
Lambda → 추가 리소스 → 계층 → 계층 생성
```

### 설정 값
- **이름:** `sgu-계정-layer`
- **설명:** `emoji install`
- **호환 런타임:** `Python 3.13`
- **업로드 방식:** `.zip 파일 업로드` 선택 후 `python.zip` 업로드

---

## 5️⃣ 해결 단계 4: Lambda 함수에 Layer 추가

- Lambda 함수 코드 편집 화면 → **"레이어 추가"** 클릭
- **사용자 지정 계층** → `sgu-계정-layer` 선택
- **버전 1** 선택 → 추가 완료

---

## ✅ 최종 결과

- 이제 Lambda 함수에서 `import emoji`가 정상적으로 동작
- 이모지 문자열도 성공적으로 출력됨:

```python
"AWS Lambda is awesome! 🚀"
```
