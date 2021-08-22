# UUID

{% embed url="https://en.wikipedia.org/wiki/Universally\_unique\_identifier" caption="위키피디아" %}

### 개요

> 네트워크 상에서 고유성이 보장되는 id 를 만들기 위한 표준 규약

Uuid 란 `Universally unique identifier` 의 약자로, 정보 식별을 위해 사용되는 식별자다. 네트워크 상에서는 서로 모르는 개체들을 식별하고 구별하기 위해서는 각각의 고유한 이름이 필요하다. 이 이름은 고유성이 매우 중요하다. 같은 이름을 갖는 개체가 존재한다면 구별이 불가능해 지기 때문이다. 고유성을 완벽하게 보장하려면 중앙시스템에서 일련번호를 부여해주면 간단하지만, 동시다발적이고 독립적으로 개발되고 있는 시스템들의 경우 중앙관리시스템은 불가능하다.

따라서 개발주체가 스스로 이름을 짓도록 하되 고유성을 충족할 수 있는 방법이 필요했고, 이를 위해 탄생한게 바로 UUID 이며 국제기구에서 표준으로 정하고 있다.

UUID표준에 따라 이름을 부여하면 고유성을 완벽하게 보장할 수는없지만 실제 사용상에서 중복될 가능성이 거의 없다고 인정되기 때문에 많이 사용된다.

### 정의

`UUID` 는 16옥텟의 수다. 1옥텟은 8비트를 의미한다. 8비트의 숫자 범위는 10진수로 0 ~ 255고 16진수로는 0 ~ ff 로 나타낼 수 있다. 즉 UUID 의 32개 글자는 2개가 하나의 숫자가 되고 하나의 숫자는 0 ~ 255의 범위를 갖는다.

\(128비트\) 표준형식에서 `UUID`는 32개의 십육진수로 표현되며 총 36개 문자 \(32개 숫자와 4개의 하이픈\) 로 된 `8 - 4 - 4 - 4 - 12` 로 구성된다.

* `UUID` 로 만들어지는 고유한 값은 총 `340,282,366,920,938,463,463,374,607,431,768,211,456` 개다.

Uuid 는 128-bit 숫자로 이루어져 있으며 `xxxxxxxx-xxxx-Mxxx-Nxxx-xxxxxxxxxxxx` 형식으로 표현된다.

* `M` 은 버전을 의미한다. 4버전을 사용한다고 하면 `M` 은 4가 된다.
* `N` 은 `variants` 을 의미한다. [여기를 참고하자.](https://en.wikipedia.org/wiki/Universally_unique_identifier#Variants)
* 128비트의 순자가 32자리의 16진수로 표현된다.
  * 8 - 4 - 4 - 4 - 12 글자마자 `-` 을 집어넣어서 5개의 그룹으로 구분한다

`UUID` 레코드 레이아웃

| 이름 | 길이 \(바이트 / 비트\) | 내용 |
| :---: | :---: | :---: |
| time\_low | 4 / 8 | 시간의 low 32비트를 부여하는 정수 |
| time\_mid | 2 / 4 | 시간의 middle 16비트를 부여하는 정수 |
| time\_hi\_and\_version | 2 / 4 | 최상위 비트에서 4비트 "version", 그리고 시간의 high 12비트 |
| clock\_seq\_hi\_and\_res clock\_seq\_low | 2 / 4 | 최상위 비트에서 1-3비트, 그리고 13-15비트 클럭 시퀀스 |
| node | 6 / 12 | 48비트 노드 id |

### 종류

`UUID` 버전은 1,3,4 그리고 5가 있다. 이 중 많이 사용되는 것은 1, 4버전이다.

* v1: 타임스탬프륽 기준으로 생성된다.
* v4: 랜던생성
* v3: MD5
* v5: SHA-1

### 구현

> 출처: [https://github.com/lukeed/uuid/blob/master/src/index.d.ts](https://github.com/lukeed/uuid/blob/master/src/index.d.ts)

```javascript
var IDX = 256,
  HEX = [],
  BUFFER;
while (IDX--) HEX[IDX] = (IDX + 256).toString(16).substring(1);

function v4() {
  var i = 0,
    num,
    out = '';

  if (!BUFFER || IDX + 16 > 256) {
    BUFFER = Array((i = 256));
    while (i--) BUFFER[i] = (256 * Math.random()) | 0;
    i = IDX = 0;
  }

  for (; i < 16; i++) {
    num = BUFFER[IDX + i];
    if (i == 6) {
      out += HEX[(num & 15) | 64]; // 1. version!
    } else if (i == 8) {
      out += HEX[(num & 63) | 128]; // 2. variants!
    } else out += HEX[num];
    if (i & 1 && i > 1 && i < 11) out += '-';
  }

  IDX++;
  return out;
}
```

해당 코드에서 주의깊게 볼 곳은 주석으로 표시한 1번이다.

* 1번: 16옥텟 중 7번째 옥텟은 버전을 나타낸다. 버전은 `4x` 와 같은 형태로 나타내야한다. `4x` 의 숫자범위를 10진수로 생각하면 64이상 80미만이다. 2진수로 바꾸면 `1000000 ~ 1001111` 이다. `bitwise` 연산을 통해 이 범위로 숫자를 한정시키기 위해서 `(num & 15) | 64` 를 사용했다. 손으로 해보면 금방 이해간다.

