# Mock이란?
- 유닛 테스트를 위한 Java mocking framework
- 자바 단위테스트에서 가짜 객체를 지원해주는 프레임워크
- 즉, 단위 테스트를 하기 위해 Mock을 만들어주는 프레임워크
- Mockito는 Szczepan Faber and friends에 의해 서비스가 제공
- Mock 객체 생성, Mock 객체 동작을 지정, 그리고 테스트 대상 로직이 제대로 수행 되었는지 확인 가능
- 자세한 내용은 공식 홈페이지 http://mockito.org/ 에서 확인 가능
- 실제 객체를 만들어 사용하기에 시간, 비용 등의 Cost가 높거나 혹은 객체 서로간의 의존성이 강해 구현하기 힘들 경우 가짜 객체를 만들어 사용하는 방법이다.