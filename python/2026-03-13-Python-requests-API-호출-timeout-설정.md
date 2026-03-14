### Context
외부 API 호출 시 응답이 지연되거나 없으면, 호출하는 프로그램이 무한히 대기하여 전체 시스템의 안정성을 저해할 수 있습니다. 특히 네트워크 문제, 서버 과부하 등으로 인해 응답 시간이 길어지는 상황에서 프로그램이 멈추는 문제를 해결하기 위해 API 호출에 대한 명확한 시간 제한(timeout) 설정이 필수적입니다.

### Core

Python의 `requests` 라이브러리를 사용하여 API를 호출할 때 `timeout` 매개변수를 설정하여 지정된 시간 내에 응답을 받지 못할 경우 예외를 발생시키도록 할 수 있습니다. `timeout`은 연결 시도 및 응답 수신에 대한 최대 대기 시간을 초 단위로 정의합니다.

*   **단일 값 (Float)**: 전체 요청(연결 및 읽기)에 대한 최대 대기 시간입니다.
*   **튜플 값 (Tuple)**: `(connect_timeout, read_timeout)` 형식으로 연결 시간 제한과 응답 본문 읽기 시간 제한을 각각 설정할 수 있습니다. 이는 연결 설정과 데이터 전송 중 어느 단계에서 문제가 발생했는지 파악하는 데 유용합니다.

```python
import requests
from requests.exceptions import Timeout, ConnectionError, RequestException

url = "https://api.example.com/data"

try:
    # 10초의 전체 타임아웃 설정 (연결 및 읽기 포함)
    response = requests.get(url, timeout=10)
    response.raise_for_status() # HTTP 오류 발생 시 예외 처리
    print("API 호출 성공:", response.json())
except Timeout:
    print("API 호출 시간 초과 (Timeout Error)")
except ConnectionError:
    print("API 연결 오류 (Connection Error)")
except RequestException as e:
    print(f"API 호출 중 예기치 않은 오류 발생: {e}")
except Exception as e:
    print(f"알 수 없는 오류 발생: {e}")

# 튜플을 사용하여 연결 및 읽기 타임아웃을 명시적으로 설정
try:
    # 연결에 5초, 데이터 읽기에 15초 허용
    response = requests.get(url, timeout=(5, 15))
    response.raise_for_status()
    print("API 호출 성공 (튜플 타임아웃):", response.json())
except Timeout:
    print("API 호출 시간 초과 (튜플 타임아웃 오류)")
except RequestException as e:
    print(f"API 호출 중 예기치 않은 오류 발생 (튜플 타임아웃): {e}")

# 재시도 로직 (예: backoff 라이브러리 활용)
# `backoff` 라이브러리는 실패한 API 호출에 대해 지수 백오프(exponential backoff) 방식으로 재시도 로직을 쉽게 구현할 수 있도록 돕습니다.
# 설치: pip install backoff
"""
import backoff
import requests

@backoff.on_exception(backoff.expo,
                      requests.exceptions.RequestException,
                      max_tries=3)
def get_data_with_retries(url):
    response = requests.get(url, timeout=5)
    response.raise_for_status()
    return response.json()

try:
    data = get_data_with_retries(url)
    print("재시도 후 API 호출 성공:", data)
except requests.exceptions.RequestException as e:
    print(f"재시도에도 불구하고 API 호출 실패: {e}")
"""
```

### Insight
`requests` 라이브러리의 `timeout` 매개변수를 활용하면 외부 API 호출 시 발생할 수 있는 무한 대기(indefinite waiting) 상황을 효과적으로 방지하고 애플리케이션의 안정성(stability)을 크게 향상시킬 수 있습니다. 특히 `requests`는 기본적으로 타임아웃을 설정하지 않으므로, 모든 외부 API 호출에는 명시적으로 `timeout`을 설정하는 것이 모범 사례(best practice)입니다.

단일 값보다는 `(connect_timeout, read_timeout)`과 같은 튜플 형태로 타임아웃을 지정하는 것이 더 세밀한 제어와 문제 진단에 도움이 됩니다. `connect_timeout`은 서버와의 초기 연결 설정에 걸리는 시간이고, `read_timeout`은 연결이 설정된 후 서버로부터 데이터를 읽어들이는 데 걸리는 시간입니다. 이 두 단계를 분리하여 관리하면 네트워크 지연과 서버 처리 지연을 구분하여 대응할 수 있습니다.

또한, `requests.exceptions.Timeout`과 같은 구체적인 예외를 처리함으로써, 시간 초과 상황에 대해 프로그램이 적절하게 대응(예: 사용자에게 알림, 로그 기록, 재시도)하도록 설계할 수 있습니다. `backoff`와 같은 라이브러리를 활용한 재시도(retry) 로직을 결합하면 일시적인 네트워크 문제나 서버 부하로 인한 실패에 더 강력하게 대처할 수 있으며, 서비스의 견고함(robustness)을 높일 수 있습니다.

**출처:**
*   [Requests: HTTP for Humans™](https://requests.readthedocs.io/)
*   [Best Practices · Always set a timeout. Never rely on defaults; Requests does not timeout by default. · Use tuple values. Distinguish between ...](https://serpapi.com/search?q=python+requests+timeout+best+practices)
