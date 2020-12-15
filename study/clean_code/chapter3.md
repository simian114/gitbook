# Chapter3. 함수

## 3.0 좋지 않은 함수

시작하기 앞서 아래의 코드를 3분동안 분석해보자.

```java
package clean.code.chapter03;

import clean.code.added.to.make.code.build.*;

public class HtmlUnit {
  public static String testableHtml(
     PageData pageData,
     boolean includeSuiteSetup
   ) throws Exception {
     WikiPage wikiPage = pageData.getWikiPage();
     StringBuffer buffer = new StringBuffer();
     if (pageData.hasAttribute("Test")) { if (includeSuiteSetup) {
         WikiPage suiteSetup =
           PageCrawlerImpl.getInheritedPage(
               SuiteResponder.SUITE_SETUP_NAME, wikiPage
           );
         if (suiteSetup != null) {
           WikiPagePath pagePath =
             suiteSetup.getPageCrawler().getFullPath(suiteSetup);
           String pagePathName = PathParser.render(pagePath);
           buffer.append("!include -setup .")
                 .append(pagePathName)
                 .append("\n");
         }
       }
       WikiPage setup =
         PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
       if (setup != null) {
         WikiPagePath setupPath =
           wikiPage.getPageCrawler().getFullPath(setup);
         String setupPathName = PathParser.render(setupPath);
         buffer.append("!include -setup .")
               .append(setupPathName)
               .append("\n");
       }
     }
     buffer.append(pageData.getContent());
     if (pageData.hasAttribute("Test")) {
       WikiPage teardown =
         PageCrawlerImpl.getInheritedPage("TearDown", wikiPage);
       if (teardown != null) {
         WikiPagePath tearDownPath =
           wikiPage.getPageCrawler().getFullPath(teardown);
         String tearDownPathName = PathParser.render(tearDownPath);
         buffer.append("\n")
               .append("!include -teardown .")
               .append(tearDownPathName)
               .append("\n");
       }
       if (includeSuiteSetup) {
         WikiPage suiteTeardown =
           PageCrawlerImpl.getInheritedPage(
                   SuiteResponder.SUITE_TEARDOWN_NAME,
                   wikiPage
           );
         if (suiteTeardown != null) {
           WikiPagePath pagePath =
             suiteTeardown.getPageCrawler().getFullPath (suiteTeardown);
           String pagePathName = PathParser.render(pagePath);
           buffer.append("!include -teardown .")
                 .append(pagePathName)
                 .append("\n");
         }
      }
    }
    pageData.setContent(buffer.toString());
    return pageData.getHtml();
   }
}
```

> 목록 \[3-1\]

* 코드를 이해했는가? 아마 아닐 것이다. 이 함수는 추상화 수준도 너무 다양하고 코드 또한 너무 길다. 어떻게 함수를 만들어야 할까?

## 3.1 작게 만들어라

* 함수를 만드는 첫 번째 규칙은 `작게`다.
* 그리고 두 번째 규칙은 `더 작게!`다.
* 그렇다면 얼마나 작게 만들어야해?
  * 모든 함수가 `2줄`, `3줄`, `4줄`만큼의 길이를 가지는게 이상적이다!

    **블록과 들여쓰기**
* 함수의 길이가 2, 3, 4줄이 되기 위해선 `if, else, while` 문 등에 들어가는 블록은 `한 줄` 이어야 한다는 것.
* 또한 이 말은 함수 내부의 들여쓰기 수준이 1단 또는 2단이어야 한다는 것이다.

## 3.2 한 가지만 해라

> 함수는 한 가지를 해야 한다. 그 한가지를 잘 해야 한다. 그 한 가지만 해야 한다.

* 위 예시 코드는 함수 하나가 여러 가지를 처리한다.
  * 버퍼를 생성하고
  * 페이지를 가져오고
  * 상속된 페이지를 검색한다음에
  * 경로를 렌더링하고
  * 불가사의한 문자열을 덧붙이고
  * HTML을 생성한다.
* 위의 quote에 적혀있듯이 함수는 단 한가지만 잘해야한다. 여러가지를 잘해도 안된다. 단 한가지만 잘하게 만들어야 한다.
  * 그렇다면 그 `한 가지`란 무엇인가?

```java
public static String renderPageWithSetupsAndTeardowns(
PageData pageData. boolean isSuite) throws Exception {
  if (isTestPage(pageData))
    includeSetupAndTeardownPagesp(pageData, isSuite);
  return pageData.getHtml();
}
)
```

> 목록 \[3-3\]

* 위의 코드를 보자. `목록 [3-3]` 은 한 가지만 하는가? 아니면 세 가지를 하는가? 1. 페이지가 테스트 페이지인지 판단한다. 2. 그렇다면 설정 페이지와 해제 페이지를 넣는다. 3. 페이지를 HTML로 렌더링한다.
* 세 단계는 지정된 함수 이름 아래에서는 추상화 수준이 하나다. 함수의 이름을 말로 풀어보면 아래와 같다. \(아래를 `To 문단` 이라고 한다.\)

```text
To renderPageWithSetupsAndTeardowns, 페이지가 테스트 페이지인지 확인한 후 테스트 페이지라면 설정 페이지와 해제 페이지를 넣는다. 테스트 페이지이든 아니든 페이지를 HTML로 렌더링 한다.
```

* _**함수가 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한가지 작업만 한다.**_ 라고 이해하면 된다.
  * 따라서 `목록 [3-3]` 은 한 가지 작업만 한다.  
* 우리가 함수를 만드는 이유는 큰 개념을 \(다시 말해 함수 이름\) 다음 추상화 수준에서 여러 단계로 나눠 수행하기 위해서기 때문이다.

## 3.3 함수 당 추상화 수준은 하나로!

* 함수가 확실히 `한 가지` 작업만 하려면 함수 내 모든 문장이 동일한 추상화 수준에 있어야 한다. `목록 [3-1]`의 추상화 수준을 분석해보면
  1. `getHtml()`: 매우 높음.
  2. `string pagePathName = PathParser.render(page)`: 중간
  3. `.append` 매우 낲음
* 하나의 함수 내에서 위의 경우처럼 추상화 수준을 섞으면 코드를 읽는 사람은 헷갈릴 수 밖에 없다.
* 특정 표현이 근본 개념인지 아니면 세부 사항인지 구분하기 어렵기 때문.
* 정말 문제인 것은 한번 이렇게 만들어 지기 시작하면 깨어진 창문처럼 걷잡을 수 있게된다는 것이다.

### 위에서 아래로 코드 읽기: 내려가기 규칙

> 내려가기 규칙: 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계 씩 낮아지는 것.

* 코드는 위에서 아래로 이야기처럼 읽혀야 좋다.
* 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.
* 즉 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계 씩 낮아진다. 이를 `내려가기 규칙` 이라고 하자.
* 다르게 표현하면, 일련의 `To 문단`을 읽듯이 프로그램이 읽혀야 한다는 의미다.
  * 여기서 각 `To 문단`은 현재 추상화 수준을 설명하며 이어지는 아래 단계 `To 문단`을 참조한다.

```text
To 설정 페이지와 해제 페이지를 포함하려면, 설정 페이지를 포함하고, 테스트 페이지 내용을 포함하고, 해제 페이지를 포함한다.
  To 설정 페이지를 포함하려면, 수트이면 수트 설정 페이지를 포함한 후 일반 설정 페이지를 포함한다.
  To 수트 설정 페이지를 포함하려면, 부모 계층에서 "SuiteSetup" 페이지를 포함한 후 일반 설정 페이지를 포함한다.
  To 부모 계층을 검색하려면 ...
```

* 추상하 수준이 하나인 함수를 구현하기란 쉽지 않다. 그렇지만 매우 중요한 규칙이다.
  * 핵심은 짧으면서 `한 가지`만 하는 함수다.
* 위에서 아래로 TO 문단을 읽어내려 가듯이 코드를 구현하면 추상화 수준을 일관되게 유지하기가 쉬워진다.

## 3.4 Switch 문

> Switch문은 본질적으로 한 가지만 하지 않는다. 그렇다고 완전히 피할 수는 없다.

* `Switch`문은 작게 만들기 어렵다. 또한 `한 가지` 작업만 하는 `switch`문도 만들기 어렵다.
  * 본질적을 스위치문은 N 가지를 처리하도록 설계되었기 때문.
  * 그렇다고 해서 `Switch`문을 완전히 피할 방법은 없기 때문에. `Switch`을 잘 사용하는 방법을 알아보자.
* 그냥 사용하지 말도록 하자.

## 3.5 서술적인 이름을 사용하라

* 이 챕터의 끝에가면 `testableHtml`이 `SetupTeardownIncluder.render` 로 변경된다.
  * 함수가 하는 일을 좀 더 잘 표현하므로 훨씬 나은 이름이다.
* 좋은 이름이 주는 가치는 아무리 강조해도 지나치지 않는다.
* 클린 코드란 코드를 읽었을 때 짐작했던 기능을 그대로 수행하는 것이다.
* 또한 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.
* `이름이 길어도 상관없다.` 길고 서술적인 이름이 짧고 어려운 이름보다 낫다.
* 이름을 붙일 때는 일관성이 있어야 한다.
  * 모듈 내에서 함수 이름은 `같은 문구`, `명사`, `동사`를 사용한다.
  * 문체가 비슷하면 이야기를 순차적으로 풀어가기가 쉬워진다.

## 3.6 함수 인수

* 함수에서 이상적인 인수 개수는 0개다.
* 인수가 4개 이상이려면 특별한 이유가 있어야한다. 아니, 특별한 이유가 있어도 사용하면 안 된다.
* `includeSetupPageInto(newPageContent)`보다 `includeSetupPage()` 가 이해하기 더 쉽다.
  * `includeSetupPageInto(newPageContent)` 는 함수 이름과 인수가 추상화 수준이 다르다.

### 많이 쓰는 단항 형식

* 함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 두 가지다. 1. 인수에 질문을 던지는 경우
  * `boolean fileExists("myFile")`
    1. 인수를 뭔가로 변환해 결과를 반환하는 경우
  * `InputStream fileOpen("Myfile")`
* 위의 두 경우는 아주 일반적인 경우고 다소 드물지만 유용한 단항 함수 형식은 `이벤트`다.
  * 이벤트 함수는 입력 인수만 있고 출력인수는 없다.
  * 프로그램은 함수 호출을 이벤트로 해석해 입력 인수로 시스템 상태를 바꾼다.
  * `passwordAttempFailedNtimes(int attempts)` 가 좋은 예다.
* 위의 세가지 경우가 아니라면 단항 함수는 피하자.

### 플래그 인수

* 플래그 인수는 추하다.
* 함수로 부울 값을 넘기는 관례는 끔찍하다.
  * 왜냐? 한수가 한꺼번에 여러 가지를 처리한닥 대놓고 이야기 하는 셈이기 때문.

### 이항 함수

* 이항 함수가 무조건 나쁜것은 아니다. 다만 할 수 있다면 단항함수로 만들자.

### 삼항 함수

* 흠... 사용하지 말자

### 인수 객체

* 인수가 2 ~ 3 개 필요하다면 이 일부를 `독자적인 클래스 변수` 로 선언할 가능성을 짚어보자.
* 아래처럼

```java
Circle makeCircle(double x, double y, double radius);
Circle makeCircl(point center, double radius);
```

### 인수 목록

* 때로는 인수 개수가 가변적인 함수도 필요하다.
* 가변 인수는 하나의 리스트 형 인수로 생각할 수 있다. 즉 가변 하나만 들어가면 이는 단항 함수로 생각해도 된다는 것이다.
* 문제는 아래처럼 가변 인수가 들어가는데 다른 변수가 또 들어가는 경우다.

```java
void dyad(String name, Integer... args);
void triad(String name, int count, Integer... args);
```

### 동사와 키워드

* 함수의 의도와 인수의 순소, 의도를 제대로 표현하려면 좋은 함수 이름이 필수적이다!
* 단항 함수는 함수와 인수가 `동사/명사` 쌍을 이뤄야 한다.
  * 예를 들면 `write(name)` 은 누구나 곧바로 `write - 동사`, `name - 명사` 가 되서 `name`을 쓴다! 라고 이해할 수 있다. 그러나 조금은 모호하다.
    * `writeField(name)` 라고 함수 이름을 짓는다면 더 명확하게 이해할 수 있다.
* 함수 이름에 키워드를 추가하는 형식 - 함수 이름으로 인자의 순서와 개수를 명확하게 알 수 있게!
  * `assertEquals` 보다 `assertExpectedEqualsActual(expected, actual)` 이 더 낫다. 왜냐하면 이렇게 하면 함수 인자의 순서를 기억할 필요가 없다. 이름에 명확히 드러나니까!

## 3.7 부수 효과를 일으키지 마라

* 부수 효과는 거짓말이다.
* 함수의 이름으로 그 함수는 단 한가지 \(함수 이름에 해당하는 행동\) 을 하겠다고 약속한 것이다. 이렇게 약속하고나서 남몰래 다른 짓도 한다는 것은 거짓말이다!!!

## 3.8 명령과 조회를 분리하라

* 함수는 뭔가를 수행하거나 뭔가에 답하거나. 둘 중 `하나`만 해야한다. 둘 다 하면 안된다.

```java
puvlic boolean set(String atttribute, String value);

if (set("username", "unclebob"))
  ...
```

* 위의 코드는 속성을 `set` 하면서 동시에 `bool` 을 리턴한다. 따라서 2번째 줄과 같은 괴상한 코드가 나온다.
* 위 함수는 아래처럼 쪼개도록하자.

```java
if (attributeExists("username")) {
  setAttribute("username", "unclebob");
  ...
}
```

## 3.9 오류 코드보다 예외를 사용하라

* 명령 함수에서 오류 코드를 반환하는 방식은 명령/조회 분리 규칙을 미묘하게 위반한다. 자칫하면 if 문에서 명령을 표현식으로 사용하기 쉬운탓이다.
* 오류 코드 대신 예외를 사용하면 오류 처리 코드가 원래 코드에서 분리되어 코드가 깔끔해진다.

### Try/Catch 블록 뽑아내기

* `try/catch` 블록은 원래가 추하다. 코드 구조에 온란을 일으키며 정상적인 동작과 오류 처리 동작을 뒤섞는다. 그러므로 `try/catch` 블록을 별도 함수로 뽑아내는 편이 낫다.

```java
public void delete(Page page) {
  try {
    deletePageAndAllReferences(page);
  }
  catch (Exception& e) {
    logError(e)
  }
}

private void deletePageAndAllReferences(Page page) throws Exception {
  deletePage(page);
  ...
  ...
}
```

### 오류 처리도 한 가지 작업이다

* 함수는 `한 가지` 작업만 해야한다. 오류 처리도 `한 가지` 작업에 속한다. 그로므로 오류를 처리하는 함수는 오류만 처리해야 마땅하다.

## 3.10 반복하지 마라

> 하지마!!!

## 3.11 구조적 프로그래밍

* 데익스트라의 구조적 프로그래밍 원칙은
  * 모든 함수와 함수 내 모든 블록에 입구와 출구가 단 하나만 존재해야 한다는 것.
  * 즉 모든 함수는 `return` 문이 하나여야 한다는 소리다.
  * 반복에서 `break` 나 `continue` 를 사용해서는 안되며 `goto`는 절대로 안된다.
* 구조적 프로그래밍은 함수가 작을 때는 큰 이익을 제공하지 못하지만 함수가 아주 클 때 이익을 제공한다.
* 하지만 우리가 원하는 클린코드는 함수의 구조 자체를 작게하는 것이므로 별 의미는 없다.

## 3.12 함수를 어떻게 짜지?

* 소프트웨어를 짜는 행위는 글짓기 와 마찬가지다. 먼저 생각을 하고 기록한 후 읽기 좋게 다듬는다. 초안은 대게 서투르므로 원하는 대로 읽힐 때까지 말을 다듬고 문장을 고치고 문단을 정리한다.
* 코드가 처음 만들어 졌을 때는 클린코드가 아닐 수 있다. 다만 이 코드를 끝없이 정리하면서\(테스트 케이스는 언제나 통과\) 정리하면 우리의 규칙을 따르는 함수가 얻어질것이다.

## 3.13 결론

* 프로그래밍의 대가는 시스템을 구현할 플로그램이 아니라 풀어갈 이야기로 여긴다.
* 프그래밍 언어라는 수단을 사용해 좀더 풍부하고 좀더 표현력이 강한 언어를 만들어 이야기를 풀어간다.
* 시스템에서 발생하는 모든 동작을 설명하는 함수 계층이 발 그 언어에 속한다.
* 이 장은 함수를 잘 만드는 기교에 대해 공부했다. 위의 규칙을 따른다면
  1. 짧고
  2. 이름이 좋고
  3. 체계가 잡힌
* 함수가 나 것이다.

