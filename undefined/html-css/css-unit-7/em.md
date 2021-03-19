# em의 정확한 기준은 뭐야?

{% embed url="https://webdesign.tutsplus.com/ko/tutorials/comprehensive-guide-when-to-use-em-vs-rem--cms-23984" caption="잘 정리되어있다" %}

## 문제 em의 크기를 정하는게 그래서 정확히 뭐야?

em의 크기가 정해지는 규칙을 두 가지에서 와리가리 했었다.

1. 부모 요소의 font-size 를 가져와서 현재 요소의 크기를 상대적으로 정한다.
2. 현재 요소에 font-size 가 정해져 있으면 그 font-size 에 상대적으로 정해진다.

위의 1번에서 이야기한 상위 요소의 font-size 직접 연관이 되어있다는게 잘못된 사실이다. 실제로는 **실제 사용된 요소의 폰트 크기와 직접 연관되어 있다.** 상위 요소의 font-size 가 em 값에 영향을 주는 것은 사실이지만, 이 모든 것은 오로지 스타일 상속 때문이다!

## 결론

em 은 현재 요소의 font-size 에 상대적인 값으로 결정된다. 만약 현재 요소에 font-size 가 없다면 상위요소의 font-size가 현재 요소로 상속되고, em 은 이를 가지고 상대적으로 결정되는것.

