---
slug: "/articles/hello-travis-ci"
date: "2020-05-20"
title: "Travis CI 맛보기"
---

# 1. 지속적 통합 (Continuous Integration, CI)이란?

지속적 통합이란, 개발 중간 중간 작은 변경 사항을 합쳐나가는 행동이다. 이는 개발 과정의 마지막에 거대한
변경 사항을 한 번에 합치는 기존의 관습과는 반대되는 것이다. 지속적 통합을 적용하지 않은 프로젝트의
개발자들은, 개발 중인 소스 코드와 프로젝트의 소스 코드를 통합하기 위해 점점 더 많은 시간을 쏟아부어야
한다. 따라서 지속적 통합은, 프로젝트를 더 작고 자주 통합하면서 거대한 통합을 위한 시간을 단축시켜 준다.
즉, 지속적 통합의 목표는 더 작게 개발하고 테스트하면서 더 건강한 소프트웨어를 만드는 것이다.

위키피디아는 지속적 통합에 대해 다음과 같이 설명한다.

```
소프트웨어 공학에서, 지속적 통합은 지속적으로 퀄리티 컨르롤을 적용하는 프로세스를 실행하는 것이다. 작은
단위의 작업, 빈번한 적용, 지속적인 통합은 모든 개발을 완료한 뒤에 퀄리티 컨트롤을 적용하는 고전적인
방법을 대체하는 방법으로서 소프트웨어의 질적 향상과 소프트웨어를 배포하는데 걸리는 시간을 줄이는데 초점이
맞추어져 있다.
```

Travis CI 또한, 이러한 목표를 달성하기 위해 나타났다.

# 2. Travis CI

Travis CI는 지속적 통합을 위한 서비스들 중 하나로, GitHub 기반으로 코드 변화를 자동으로 빌드하고
테스트하며, 즉각적으로 피드백을 제공하며 개발을 지원한다. 뿐만 아니라, 배포 관리, 알림 등 다양한 개발
과정을 자동화할 수 있도록 도와준다. 우리가 Travis 플랫폼에서 빌드를 실행하면, Travis CI는 GitHub
저장소를 완전히 새로운 가상 환경에 복제한다. 그 뒤, 코드를 빌드하고 테스트하기 위한 일련의 작업을
수행한다. 만약 작업들 중 실패가 발생할 경우 해당 빌드는 깨진broken 상태가 되며, 모든 작업이 성공할
경우 빌드는 통과passed 상태가 되며 배포 과정을 진행한다.

Travis CI는 GitHub 저장소에 훅hook을 설정하여 저장소에 푸쉬가 이루어질 경우 자동으로 통합 과정을
실행한다. 빌드가 통과될 경우, 우리는 배포, 알림 등 다양한 기능을 자동화할 수 있다.

**2.1. Travis CI 시작하기**

Travis CI는 GitHub 기반으로 동작하기 때문에, 사용하기 위해선 먼저 GitHub 계정으로 회원가입 한다.
우측 상단의 “Sign in with GitHub” 버튼을 클릭하여 회원가입을 진행할 수 있다.

Travis CI의 과금 정책은 GitHub와 비슷하다. 공개 저장소에 대해서는 무료로 기능을 제공하지만, 비공개
저장소에 적용하기 위해선 \$69 이상의 비용을 지불해야 한다. 무료 사용을 위해 공개 저장소를 하나 만들자.

프로젝트를 등록하기 위한 “+” 버튼을 클릭하면, 연동된 GitHub 계정에 있는 저장소를 선택할 수 있다.
만약 저장소가 보이지 않는다면 “Sync account” 버튼을 클릭해 계정을 동기화하거나, 권한을 확인해보자.
저장소 우측에 있는 스위치를 활성화하면 Travis CI에 프로젝트가 추가된다.

처음 프로젝트를 등록하면 위와 같은 화면이 보일 것이다. 아직 저장소에 대한 훅을 설정하지 않았으므로
저장소에 빌드가 존재하지 않는 것을 확인할 수 있다.

먼저, Travis가 빌드를 수행할 수 있도록 간단한 계산기 프로그램과 테스트 케이스를 포함하는 간단한
Gradle 프로젝트를 작성한다. 소스 코드는 다음과 같다. 물론 아직 훅을 설정하지 않았기 때문에 GitHub에
푸쉬하더라도 Travis CI의 프로젝트에서는 빌드가 없다고 나올 것이다.

Calculator.java

```
public class Calculator {
public double add(double a, double b) {
return a + b;
}

public double subtract(double a, double b) {
return a - b;
}

public double multiply(double a, double b) {
return a * b;
}

public double divide(double a, double b) {
return a / b;
}
}
```

CalculatorTest.java

```
public class CalculatorTest {

private static final double DELTA = 0.0001d;

@Test
public void add() {
Calculator calculator = new Calculator();
assertEquals(6.0d, calculator.add(2, 4), DELTA);
}

@Test
public void subtract() {
Calculator calculator = new Calculator();
assertEquals(-2.0d, calculator.subtract(2, 4), DELTA);
}

@Test
public void multiply() {
Calculator calculator = new Calculator();
assertEquals(8.0d, calculator.multiply(2, 4), DELTA);
}

@Test
public void divide() {
Calculator calculator = new Calculator();
assertEquals(0.5d, calculator.divide(2, 4), DELTA);
}
}
```

이제 훅을 설정해보자. Travis CI는 GitHub 저장소의 루트 위치에 있는 `.travis.yml` 파일을
바탕으로 프로젝트를 설정한다. 간단한 프로젝트이기 때문에 설정 파일도 간단하다. 그저 프로젝트가 사용하는
언어와, 사용하는 Java 버전만 명시하면 된다.

.travis.yml

```
language: java

jdk:
- oraclejdk8
```

`.travis.yml` 파일을 생성하고 푸쉬해보자.

훅이 설정되면 Travis CI는 커밋이 푸쉬될 때 마다 빌드를 실행한다. Gradle 프로젝트를 사용하기 때문에
빌드를 수행하기 전 `gradle assemble` 명령을 실행한 후 `gradle check` 명령으로 프로젝트를
빌드한다. `gradle check` 명령은 프로젝트에 작성된 테스트 케이스를 실행하여 올바르게 테스트를
통과하는지 검사한다. 만약 모든 테스트 케이스가 성공할 경우 빌드는 통과되겠지만, 하나라도 실패할 경우
빌드는 깨질 것이다. 지금은 모든 테스트 케이스가 성공하기 때문에 프로젝트 빌드는 통과된다.

빌드 결과는 자동으로 메일로 전송된다.

**2.2. Pull Request를 해보자**

이제, 오픈 소스 프로젝트의 묘미인 Pull Request에서 Travis CI가 어떻게 동작하는지 알아보자. 같이
하는 친구가 있다면, 친구의 저장소에 PR을 날려도 좋고, 혼자라면 본인의 저장소에 PR을 날려도 괜찮다.

우리는 나눗셈의 제수가 0이 되는 경우 `IllegalArgumentException`을 발생시킬 것이다. 이를 위해
소스 코드를 조금 수정하자.

Calculator.java

```
public double divide(double a, double b) {
if (b == 0.0d) {
throw new IllegalArgumentException("divisor must not be zero.");
}
return a / b;
}
```

CalculatorTest.java

```
@Test
public void divide() {
Calculator calculator = new Calculator();
assertEquals(0.5d, calculator.divide(2, 4), DELTA);
assertEquals(0.0d, calculator.divide(2, 0), DELTA);
}
```

물론 0으로 나눌 경우에 대한 에러 처리를 하지 않으므로 테스트 케이스는 실패할 것이다. 이는 인위적으로
빌드를 깨트릴 목적이므로 같이 해보자. PR을 날리기 위해 본인의 저장소에 푸쉬할 경우 Travis CI가
동작하여 빌드가 깨질 것이지만, 잠시만 무시하고 PR을 날리자.

Travis CI의 Pull Reqeusts 메뉴를 클릭하면, 방금 날린 PR의 테스트 케이스가 실패해서 빌드가 깨진
것을 볼 수 있다.

또한 GitHub 저장소의 Pull Request 게시글에서도 볼 수 있어 굳이 Travis CI 프로젝트 페이지에
들어가지 않더라도 빌드 성공 여부를 볼 수 있다.

빌드가 깨진 것을 확인했으니 다시 수정해서 PR을 날려보자. `divide` 메소드를 실행할 때
`IllegalArgumentException`을 catch 하자. 만약 `IllegalArgumentException`이
catch되지 않으면 테스트 케이스를 실패시킨다. 0으로 나눌 경우 `IllegalArgumentException`이
throw 되어야 하므로 이 테스트 케이스는 성공한다.

CalculatorTest.java

```
@Test
public void divide() {
Calculator calculator = new Calculator();
assertEquals(0.5d, calculator.divide(2, 4), DELTA);
try {
calculator.divide(2, 0);
fail();
} catch (IllegalArgumentException e) {
}
}
```

테스트 케이스가 성공하도록 수정한 뒤 푸쉬하면 PR이 자동으로 업데이트 된다.

이제 모든 테스트 케이스가 성공해서 빌드가 통과된다.

마찬가지로 GitHub Pull Request 게시글에서도 빌드가 통과됬음을 알 수 있다. 프로젝트 관리자는 빌드
통과 여부를 바탕으로 머지 여부를 판단할 것이다.

**2.3. Build Status 이미지 붙이기**

마지막으로, 오픈 소스 프로젝트의 README에서 자주 보이는 빌드 상태 이미지를 붙여보자.

Travis CI 프로젝트 페이지의 이름 우측에 빌드 상태 이미지를 클릭하면 붙여넣기 쉽도록 링크를 제공한다.

복사해서 GitHub 저장소의 README.md의 적절한 위치에 추가하자.

이제 우리 저장소에도 멋진 빌드 상태 이미지가 추가되었다.