### Context
n8n에서 AI Agent를 활용하여 자동화 작업을 수행할 때, 출력 형식이 지켜지지 않는다는 오류("model output doesn't fit required format")가 발생했습니다. 이러한 문제는 특히 AI Agent가 JSON 형식의 출력을 요구하는 상황에서 많이 나타납니다.

### Core
이 문제를 해결하기 위해 다음과 같은 점을 고려해야 합니다:

* AI Agent의 출력을 JSON 형식으로 유지해야 합니다. 이를 위해 아래와 같은 필드가 필요합니다:
  * folder
  * filename
  * content
  * summary
* 입력 값을 질문형이 아닌 기록형 메모로 입력하면 AI의 응답이 훨씬 안정적입니다.

예시 프롬프트:
```json
{
  "input": "n8n model output doesn't fit required format error"
}
```

n8n 설정 예시:
* Auto Fixing 옵션을 활성화합니다.
* Retry On Fail 옵션을 활성화합니다.

### Insight
프롬프트를 기록형 메모로 구성할 때, AI Agent의 응답 오류 발생률이 낮아졌습니다. 이렇게 함으로써 출력 형식의 일관성을 유지할 수 있었습니다. AI의 안정적인 응답을 위해 구조화된 입력을 설계하는 것이 중요하다는 것을 확인했습니다.

**출처:** [n8n Documentation](https://docs.n8n.io/)