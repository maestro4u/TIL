### Context
외부 API를 호출할 때 응답이 지연되어 프로그램이 멈추는 현상이 발생했습니다. 이 문제를 해결하기 위해 Python의 `requests` 모듈을 활용하여 요청에 제한 시간을 설정했습니다.

### Core
`requests.get` 요청에 `timeout` 옵션을 추가하여 응답 지연 문제를 해결할 수 있습니다. `timeout`은 (접속 시간, 읽기 시간)의 튜플로 지정할 수 있습니다.

```python
import requests

try:
    response = requests.get('https://api.example.com/data', timeout=(3, 5))
    response.raise_for_status()
    data = response.json()
except requests.Timeout:
    print('요청 시간이 초과되었습니다.')
except requests.RequestException as e:
    print(f'요청 에러가 발생했습니다: {e}')
else:
    print('데이터를 성공적으로 가져왔습니다.')
```

위 코드에서는 연결 시간을 3초, 읽기 시간을 5초로 설정했습니다. 이는 대부분의 외부 API에 적합한 설정입니다. 

### Insight
`timeout` 설정을 통해 프로그램이 응답 지연으로 인해 멈추는 문제를 효과적으로 해결할 수 있습니다. 특히, 중요한 응용프로그램에서는 연결과 읽기 시간 초과를 구분하여 처리하는 것이 좋습니다. 위 설정으로 대부분의 응답 지연 문제를 해결할 수 있으며, 설정 값을 필요에 따라 조정할 수 있습니다.

**출처:** [Python Requests Official Documentation](https://docs.python-requests.org/en/master/user/quickstart/#timeouts)