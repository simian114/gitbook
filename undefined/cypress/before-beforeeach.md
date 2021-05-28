---
description: before와 beforeEach
---

# before\(\)와 beforeEach\(\)

## before\(\)

> 테스트 실행전의 작업을 수행한다. 하지만, 단 한번만 실행이 된다는 점을 잊지말자.

```javascript
describe('My first test', () => {

  it('visit!!!', () => {
    cy.visit('https://example.cypress.io');

    cy.contains('type').click();
    cy.url().should('include', 'commands/actions');
    cy.get('.action-email')
      .type('fake@email.com')
      .should('have.value', 'fake@email.com');
  })

  it('doesnt isolated!!!', () => {
    cy.visit('https://example.cypress.io');
    cy.contains('type').click();
  })
});
```

위 테스트에는 두 개의 테스트가 존재한다. 테스트를 잘 살펴보면 **두 개의 테스트 모두에서 특정 사이트에 접근을 한 다음에 테스트를 진행한다는걸 알 수 있다. 즉 공통의 작업을 하는것.**

이런 상황에서 사용할 수 있는게 바로 **before\(\)** 메서드다. 이 메스드를 사용하면 특정 작업을 시작하기 전에 등록한 작업을 마치고 테스트에 들어갈 수 있게 된다.

```javascript
describe('My first test', () => {
  before(() => {
    cy.visit('https://example.cypress.io');
  })

  it('visit!!!', () => {
    cy.contains('type').click();
    cy.url().should('include', 'commands/actions');
    cy.get('.action-email')
      .type('fake@email.com')
      .should('have.value', 'fake@email.com');
  })

  it('doesnt isolated!!!', () => {
    cy.contains('type').click();
  })
});
```

이렇게 수정할 수 있게 된다.

**하지만 위의 테스트를 실행하면 에러가 발생한다.** 대체 왜? _\*왜냐하면 before\(\) Hook 은 단 한번만 실행되기 때문이다._

### 위 테스트의 문제점

> 테스트의 고립

2개의 테스트를 진행한다. 하지만 에러가 발생한다. 이 에러가 발생하는 이유는 두 번째 테스트가 첫 번째 테스트에 **고립된 테스트가 아니기 때문이다.**

고립이 아니라는게 무슨말인가? 간단하다. 첫 번째 테스트에서 다른 페이지로 이동했다. 그리고 테스트를 진행했는데, 사이프레스는 테스트에 대한 모든 state을 clear 하고 다음 테스트를 진행하지만, 이 이동된 페이지는 그대로 남기 때문이다.

따라서 두 번째 테스트가 우리가 원하는 페이지에서 실행되지 못하고 첫 번째 테스트로 인해 움직인 페이지에서 수행되고 있던 것.

즉 두 개의 테스트는 **고립되지 못했다.**

## beforeEach

> beforeEach 를 사용하면 모든 테스트는 자신이 실행되기 전에 이 작업을 수행한다.

**before은 단 한번만 실행된다. 이 덕분에 위에서는 고립이 되지 못한 상황이 발생하고 에러가 일어났다. 이런 문제를 해결할 수 있는게 바로 beforeEach다.**

사용법은 아주 간단하다.

```javascript
describe('My first test', () => {
  beforeEach(() => {
    cy.visit('https://example.cypress.io');
  })

  it('visit!!!', () => {
    cy.contains('type').click();
    cy.url().should('include', 'commands/actions');
    cy.get('.action-email')
      .type('fake@email.com')
      .should('have.value', 'fake@email.com');
  })

  it('doesnt isolated!!!', () => {
    cy.contains('type').click();
  })
});
```

이게 끝이고, 이렇게 하면 위의 문제는 깔끔히 해결된다.

