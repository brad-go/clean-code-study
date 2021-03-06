# 깨끗한 코드

# 책에서 배워 갈 점

- 코드를 최대한 다양한 각도에서 살펴보는 능력
- 좋은 코드와 나쁜 코드를 구분하는 능력
- 좋은 코드를 작성하는 방법
- 나쁜 코드를 좋은 코드로 바꾸는 실력

# 코드가 존재하리라

## 코드는 요구사항을 표현하는 언어

- 앞으로 우리 프로그래밍 언어에서 추상화 수준은 점차 높아질 것
- 더불어 특정 응용 분야에 적합한 프로그래밍 언어 수도 점차 많아질 것

→ 그러나 코드는 사라지지 않음

### 어떤 언어를 사용하든 코드는 기계가 이해하고 실행할 정도로 엄밀하고 정확하고 상세하고 정형화되어야 한다.

## 나쁜 코드

- 고행 : 나쁜 코드에 발목이 잡혀 고생하는 것
- 우리 모두는 자신이 짠 쓰레기 코드를 쳐다보며 나중에 손보겠다고 생각한 경험 → 나중은 결코 오지 않음
- 르블랑의 법칙 : 나중은 절대 돌아오지 않는다는 법칙

## 나쁜 코드로 치르는 대가

- 나쁜 코드는 개발 속도를 크게 저하시킴
- 시간이 지나면서 쓰레기 더미는 점점 더해짐
- 나쁜 코드가 쌓일수록 팀 생산성은 떨어짐

- 원대한 재설계의 꿈
    - 관리층에게 재설계를 요구
    - 타이거 팀과 기존 팀이 생성
        - 타이거 팀 : 기존 시스템 기능을 모두 제공, 개발 기간동안 기존 시스템의 변경점 포함
        - 기존 팀 : 계속 변경점 추가
    - 타이거 팀이 개발을 끝내면 다시 2개의 팀이 생성
    
    → 악순환
    
- 태도
    - 프로그래머가 전문가 답지 못했기 때문에 나쁜 코드가 발생
        - 관리자와 마케팅은 약속과 공약을 내걸며 우리에게 정보를 구함
        - 사용자는 요구사항을 내놓으며 우리에게 현실성 자문
        - 프로젝트 관리자는 일정을 잡으며 우리에게 도움을 청함
- 원초적 난제
    - 전문가는 기한을 맞추는 유일한 방법(빨리가는 유일한 방법)은 언제나 코드를 최대한 깨끗하게 유지하는 습관이라는 것을 앎
- 깨끗한 코드라는 예술?
    - 깨끗한 코드를 작성하려면 ‘청결’이라는 힘겹게 습득한 감각을 활용해 자잘한 기법들을 적용하는 절제와 규율이 필요 → 열쇠는 ‘코드 감각’
    - ‘코드 감각’으로 최고 방안을 선택한 후 여기서 거기까지 이동하는 경로를 계획
- 깨끗한 코드란?
    - 바야네 스트롭스트룹
        - 깨끗한 코드는 보는 사람에게 즐거움을 선사
        - CPU 자원을 낭비하지 않는 코드, 속도를 포함한 효율
        - 메모리 누수, 경쟁 상태, 일관성 없는 명명법을 포함한 철저한 오류처리
            
            →깨끗한 코드는 세세한 사항까지 꼼꼼하게 처리하는 코드
            
        - 깨끗한 코드는 한가지에 집중
    - 그래디 부치
        - 가독성
        - 깨끗한 코드는 해결할 문제의 긴장을 명확히 드러내야함
        - 코드는 추측이 아니라 사실에 기반 → 반드시 필요한 내용만 담아야 함
    - 큰 데이브 토마스
        - 다른 사람이 고치기 쉬워야 함
        - 테스트 케이스가 있어야함
        - 작은 코드일수록 좋음
        - 문학적
    - 마이클 페더스
        - 깨끗한 코드는 주의 깊게 작성한 코드
    - 론 제프리스
        - 중복을 피하라
        - 의미 있는 이름을 포함한 표현력 → 한가지 기능만 수행하라
            - 여러 기능을 수행하는 객체 → 여러 객체로 나눔
            - 여러 기능을 수행하는 메서드 → 여러 메서드로 나눔
                - [메서드 추출 리팩터링 기법](https://lelecoder.com/117)
        - 추상 메서드나 추상 클래스로 실제 구현을 감싸기 → 작게 추상화 하라
            - 모든 프로그램이 아주 유사한 요소로 이루어진 경우
            - 어떤 집합에서 특정 항목을 찾아낼 필요가 있는 경우
    - 워드 커닝 햄
        - 읽으면서 짐작한 대로 돌아가는 코드가 깨끗한 코드
        - 모듈을 읽으면 다음에 벌어질 상황이 보임
        - 단순하게 설계
        - 언어를 단순하게 보이도록 만드는 열쇠는 프로그래머
- 저자의 생각
    - 깨끗한 변수 이름, 깨끗한 함수, 깨끗한 클래스를 만드는 방법 소개
    - 여러 논쟁의 여지가 있으나 시각을 이해하고 존중하며 읽기
- 우리는 저자다
    - [Javavdoc](https://www.lesstif.com/java/javadoc-cheat-sheet-26083721.html)
        - @author 필드는 저자를 소개
    - 저자에게는 독자와 잘 소통할 책임도 있다.
    - 새 코드를 짜면서 우리는 끈임없이 기존 코드를 읽는다. → 읽기 쉽게 만들기
- 보이스카우트 규칙
    - 체크아웃할 때보다 좀 더 깨끗한 코드를 체크인 한다면 코드는 절대 나빠지지 않는다.
    - 지속적인 개선이야말로 전문가 정신의 본질
- 프리퀄과 원칙
    - SRP : 클래스에는 한가지, 단 한가지 변경 이유만 존재해야 한다.
    - OCP : 클래스는 확장에 열려 있어야 하며 변경에 닫혀 있어야 한다.
    - LSP : 상속받은 클래스는 기초 클래스를 대체할 수 있어야 한다.
    - DOP : 추상화에 의존해야 하며, 구체화에 의존하면 안 된다.
    - ISP : 클라이언트에 밀접하게 작게 쪼개진 인터페이스를 유지한다.
- 결론
    
    연습하자 ^^
    

## Quiz

- 르블랑의 법칙이란?
    
    나중은 절대 돌아오지 않는다는 법칙
    
- 깨끗한 코드를 짜기위해서 필요한것 5가지
    
    중복제거, 작게추상화, 한가지 기능만수행, 철저한 오류처리, 테스트케이스 필요, 가독성 등등