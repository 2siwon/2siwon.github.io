```python
from selenium import webdriver
import unittest


class NewVisitorTest(unittest.TestCase):
    def setUp(self):
        self.browser = webdriver.Chrome()

    def tearDown(self):
            self.browser.quit()

    def test_can_start_a_list_and_retrieve_it_later(self):
        # 에디스(Edith)는 멋진 작업 목록 온라인 앱이 나왔다는 소식을 듣고
        # 해당 웹 사이트를 확인하러 간다.
        self.browser.get('http://localhost:8000')
        
        # [...]
        # 만족하고 잠자리에 든다

if __name__ == '__main__':
    unittest.main(warnings='ignore')
```

**기능 테스트** : 사용자 관점에서 애플리케이션 외부를 테스트(제대로 된 기능성을 갖춘 애플리케이션을 구축)

**단위 테스트** : 프로그래머 관점에서 그 내부를 테스트(깔끔하고 버그 없는 코드 작성에 도움)

1. 기능 테스트를 작성해서 사용자 관점의 새로운 기능성을 정의
2. 기능 테스트가 실패하면 어떻게 코드를 작성해야 테스트를 통과할지 생각(이 시점에서 적어도 하나 이상의 단위 테스트를 이용해서 어떻게 코드가 동작해야 하는지 정의)
3. 단위 테스트가 실패하면 단위 테스트를 통과할 수 있을 정도의 최소한의 코드 작성, 테스트가 완전해질 때까지 과정 2와 3을 반복
4. 기능 테스트 재실행. 제대로 동작하는지 확인. 이 과정에서 새로운 단위 테스트를 작성해야 할 수도 있다.

기능 테스트는 상위 레벨 개발 주도, 단위 테스트는 하위 레벨 주도.
