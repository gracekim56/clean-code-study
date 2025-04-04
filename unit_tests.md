# 9장 단위 테스트

### **TDD 법칙 세 가지**
TDD는 실제 코드를 짜기 전에 단위 테스트부터 짜는 기법이다. 다음 세 가지 법칙이 있다.

- 첫째: 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
- 둘째: 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
- 셋째: 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

<br>

### **깨끗한 테스트 코드 유지하기**
코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 바로 단위 테스트다. 테스트 케이스가 있으면 변경이 두렵지 않기 때문이다. 테스트 케이스가 없다면 모든 변경이 잠정적인 버그다.  
테스트 코드가 지저분하면 코드를 변경하는 능력이 떨어지며 코드 구조 개선도 어려워진다. 테스트 코드가 지저분할수록 실제 코드도 지저분해진다.

<br>

### **깨끗한 테스트 코드**
가독성은 실제 코드보다 테스트 코드에 더 중요하다. 테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.  

어떻게 하면 가독성이 좋은 테스트 코드를 만들 수 있을까?
- 중복 코드 제거하기
    - 같은 동작을 여러 번 반복하면 코드가 길어지고 이해하기 어려워진다.
    - 공통되는 부분을 함수로 분리하면 가독성이 좋아진다.
- 불필요한 세부 사항 숨기기
    - 테스트에서 중요한 것은 “무엇을 검증하는가?”이지, 내부 동작이 어떻게 이루어지는지가 아니다.
    - API 호출이나 객체 생성 같은 세부 사항이 너무 많으면 테스트의 목적이 흐려진다.
- BUILD-OPERATE-CHECK 패턴 따르기
    - 테스트를 세 가지 단계로 나누면 이해하기 쉽다.
        1.	BUILD(설정) → 테스트를 위한 데이터를 만든다.
        2.	OPERATE(실행) → 기능을 실행한다.
        3.	CHECK(검증) → 기대한 결과가 나오는지 확인한다.

<br>

### **테스트 당 assert 하나**
일부 개발자들은 하나의 테스트 함수에서 단 하나의 assert 문만 사용해야 한다는 원칙을 주장한다. 이 원칙을 따르면 테스트가 명확한 결론을 가지며 이해하기 쉬워진다.  

아래는 JSON 응답이 올바른 형식인지 확인하는 테스트 코드이다.

```python
import unittest

class TestPageResponse(unittest.TestCase):
    
    def test_get_page_hierarchy_as_xml(self):
        given_pages(["PageOne", "PageOne.ChildOne", "PageTwo"])
        response = when_request_is_issued("root", {"type": "pages"})
        then_response_should_be_xml(response)
    
    def test_get_page_hierarchy_has_right_tags(self):
        given_pages(["PageOne", "PageOne.ChildOne", "PageTwo"])
        response = when_request_is_issued("root", {"type": "pages"})
        then_response_should_contain(response, 
            "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
        )

if __name__ == "__main__":
    unittest.main()
```

- 단일 Assert의 장점
    - 각 테스트가 하나의 결론만 검증하여 명확하다.
    - 디버깅이 쉬워진다 → 어떤 테스트가 실패했는지 빠르게 알 수 있다.

- 단일 Assert의 단점
    - 중복 코드가 증가 → given_pages()와 when_request_is_issued()가 반복된다.
    - 불필요한 테스트 분리가 발생 → 하나의 개념을 여러 개의 테스트로 나누어야 할 수도 있다.

- 현실적인 접근법
    - assert 개수를 최소화하되, 필요하면 여러 개의 assert 문을 허용한다.
    - 중복이 많아지면 공통 로직을 별도 함수로 추출하여 코드 간결성을 유지한다.

테스트에서 더 중요한 원칙은 “하나의 테스트 함수는 하나의 개념만 검증해야 한다.”는 것이다.

<br>

### **F.I.R.S.T 원칙**
깨끗한 테스트는 F.I.R.S.T 원칙을 따라야 한다.
이 원칙은 테스트 코드의 품질을 높이고, 유지보수성을 향상시키는 데 중요한 역할을 한다.

1. 빠르게 (Fast)
- 테스트는 빠르게 실행되어야 한다.
- 실행 속도가 느리면 개발자가 자주 실행하지 않게 되고, 그로 인해 문제를 빨리 발견하지 못하게 된다.

2. 독립적으로 (Independent)
- 테스트는 서로 독립적이어야 하며, 다른 테스트에 영향을 주어서는 안 된다.
- 하나의 테스트가 다른 테스트를 위한 설정을 하면, 첫 번째 테스트가 실패하면 이후 모든 테스트가 실패하는 문제가 발생할 수 있다.
- 테스트는 어떤 순서로 실행하든 항상 같은 결과를 보장해야 한다.

3. 반복 가능하게 (Repeatable)
- 테스트는 어떤 환경에서도 동일한 결과를 보장해야 한다.
- 특정 환경에서만 테스트가 실행되도록 의존성을 가지면, 다른 환경에서는 지원되지 않기에 테스트를 수행하지 못하는 상황에 직면한다.

4. 자가검증하는 (Self-Validating)
- 테스트는 결과가 명확하게 성공 또는 실패로 나와야 한다.
- 로그 파일을 읽거나, 다른 파일과 수동으로 비교해야 하는 테스트는 좋은 테스트가 아니다.
- 테스트가 스스로 성공과 실패를 가늠하지 않으면 판단은 주관적이게 된다.

5. 적시에 (Timely)
- 테스트는 적시에 작성되어야 하며, 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다.
- 실제 코드가 먼저 작성되면, 테스트하기 어려운 코드가 만들어질 가능성이 높아진다.
