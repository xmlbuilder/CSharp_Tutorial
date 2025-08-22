# 🧵 Environment.NewLine의 핵심 요약
| 항목 | 설명 |
|------|-----| 
| 정의 | 현재 환경(OS)에 맞는 줄바꿈 문자열 | 
| Windows | "\r\n" | 
| Unix/Linux/macOS | "\n" | 
| 타입 | string | 
| 용도 | 콘솔 출력, 파일 저장, 문자열 포맷 등에서 줄바꿈을 명시적으로 처리할 때 사용 | 



## 📌 왜 직접 "\n"이나 "\r\n"을 쓰지 말아야 할까?
- 플랫폼 독립성: 코드가 Windows에서 잘 돌아가더라도, Linux나 macOS에서는 줄바꿈이 깨질 수 있음
- 가독성: Environment.NewLine은 의도를 명확히 표현함 → “여기 줄바꿈이 들어간다”
- 유지보수: 나중에 줄바꿈 정책을 바꾸거나 테스트할 때 훨씬 유리함

## 🧪 예제 코드 분석
```csharp
Console.WriteLine($"NewLine: {Environment.NewLine}  first line{Environment.NewLine}  second line");
```

출력 결과:
```csharp
NewLine: 
  first line
  second line
```

- 첫 번째 {Environment.NewLine}은 "NewLine: " 다음에 줄바꿈을 삽입
- 두 번째와 세 번째는 각각 "first line"과 "second line" 앞에 줄바꿈을 삽입
- 결과적으로 줄 단위로 출력이 깔끔하게 분리됨

## ✨ 실전에서 유용한 순간들
- 로그 파일 생성 시 줄 단위 구분
- 텍스트 기반 프로토콜 구현 (예: HTTP 헤더)
- CSV/TSV 파일 생성 시 줄바꿈 처리
- 콘솔 UI 구성 시 줄 정렬
---


