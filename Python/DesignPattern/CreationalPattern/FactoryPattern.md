# Factory Design Pattern

## 의도

객체를 생성하기위한 인터페이스를 정의하지만, 서브 클래스가 인스턴스화 할 클래스를 결정하게 한다.

factory method를 사용하면 클래스가 서브 클래스에 대한 인스턴스 생성을 지연하게 할 수 있다.

vitual constructor를 정의한다.

## 문제

프레임 워크는 다양한 응용 프로그램의 구조 모델을 표준화해야하지만 개별 응용 프로그램이 자체 도메인 객체를 정의하고 인스턴스화 할 수도 있다.


