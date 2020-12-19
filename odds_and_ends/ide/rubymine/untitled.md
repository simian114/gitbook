---
description: IDE를 사용하면서 자주 사용하는 단축키를 정리합니다.
---

# 실전이 중요!

## 실행 설

> 이 방법은 아마 거대한 프로젝트에서는 사용하지 못하고 지금처럼 하나의 파일만 실행하는데 사용하는 방법인 것 같다.

코드를 작성하고 실행을 하려고 보니깐 실행 버튼이 눌리지 않는다... 아래 방법으로 임시\(?\) 해

![&#xC2E4;&#xD589; &#xBC84;&#xD2BC;&#xC774; &#xB20C;&#xB9AC;&#xC9C0; &#xC54A;&#xB294;&#xB2E4;...](../../../.gitbook/assets/rubymine1.png)

오른쪽에 보이는 `작은 터미널`을 누르자.\(`Control` 연타\) 그러면 `Run Anything` 이라는 팝업이 뜬다. 창에다가 `ruby file_name.rb` 를 입력하자. 이후부터는 `Ctrl + R` 로 파일 실행이 가능해진다. 팝업창에는 이전 기록들이 모두 남아 있기 때문에 단위테스트가 중요한 루비에서는 매우 유용할것으로 보인다.

## 파일 생성 및 열기

1. `command + 1`로 옆에 내비게이션 바를 열자.
2. 그 상태에서 화살표 키 또는 마우스를 이용해 파일을 생성하기 원하는 곳으로 이동하고 `command + N` 을 입력하자

## 터미널 열기

`Opt + F12`

> 내 키보드는 Function 키가 안눌리므로 prtSc를 F12로 매핑했다.

## 최근 작업 보기

1. 최근 작업 파일
   * `commad + E`
2. 최근 작업 위치
   * `command + shift + E`

## 탭 스플릿

* `ctrl + alt + ]` - move tab to the right splitter \(or create a new one if it's the rightmost splitter\)
* `ctrl + alt + [` - move tab to the left splitte 
* `ctrl + alt + P` - move tab to the splitter above
* `ctrl + alt + '` - move tab to the splitter below \(or create a new one if it's the bottom splitter\)
* `alt + shift + [` - stretch splitter left
* `alt + shift + ]` - stretch splitter right
* `alt + shift + =` - equal size splitter
* `alt + shift + M` - maximize/restore splitter

### To move focus between splitters:

* `ctrl+alt+shift+]` - right
* `ctrl+alt+shift+[` - left
* `ctrl+alt+shift+P` - up

  -`ctrl+alt+shift+;` - down

* `ctrl + alt + .` - \(built-in action\) recommended binding for Goto Next Splitter action

