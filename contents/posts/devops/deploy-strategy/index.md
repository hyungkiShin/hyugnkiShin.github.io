---
title: "배포전략"
date: 2022-11-18
update: 2022-11-18
tags:
  - 배포
---

# 배포 전략에 관하여

문득 dev 환경에서 테스트를 할겸 <br/>
열씸히 dev 브렌치를 merge 후 열씸히 젠킨스 빌드버튼을 누르던 중

![](click.gif)

배포 전략에 대한 내용을 생각해보게 되었습니다. <br/>

# 배포 전략 ?
모놀리틱 서비스 환경에서 마이크로 서비스 환경으로 바뀌면서 배포 주기가 짧아졌기 때문에 <br/>
배포 전략이 필요하게 되었답니다. <br/>

# 중단 배포 방식, 다운타임 (DownTime)

중단 배포 방식은 배포 중에 서비스가 중단되는 방식입니다. <br/>
사용자들은 자연스레 서비스가 중단되는 시간동안 서비스를 이용할 수 없게 되겠습니다. <br/>
서비스를 사용할 수 없는 시간대를 downtime 이라고 합니다. <br/>
때문에 현대에 웹 어플리케이션 에서 중단 배포 방식은 자연스레 사용하지 않게 되었습니다. <br/>
그래서 등장한 전략이 " 무중단 배포 " 전략입니다.

# 무중단 배포 전략의 종류 로는 크게 세가지가 있습니다.
- Rolling 배포
- Blue/Green 배포
- Canary 배포

각각의 전략에 따라 장단 점이 극명한데 <br/>
주관적인 생각으로는 서비스의 특성에 따라 적절한 전략을 선택하는 것이 좋을 것 같습니다. <br/>

## Rolling 배포

사실 여러 블로그, 문서들을 찾아보면서 배포 전략에 대한 내용을 찾아보았는데 <br/>
그림이 마땅치가 않습니다... <br/>
제일 널리 알려져 있는 그림은 아래와 같으나, 제가 본 최고의 그림은 저작권이 있어서 아래 그림으로 대체하겠습니다. <br/>
100 만의 트레픽을 받는 인스턴스가 총 3대가 띄워져 있다고 가정하겠습니다 :) <br/>


3 대중 2대만이 운영 트레픽을 받게되고 1대는 LB (로드벨런서) 에서 떼어놓습니다. <br/>
그렇게 2대만이 가용서버가 되고 트레픽은 각각 50만 50만 씩 사이좋게 나누어 받습니다. <br/>
배포가 완료되고 다음차례인 서버에 배포를 진행합니다. <br/>
이과정에서 로드벨런서 를 붙였다 뗏다를 반복하며, 순차적으로 모든 서버가 신규 서버로 점진적 교체가 이루어 지면서 배포가 완료됩니다. <br/>
(물론 이때 트레픽은 100% 운영중인 서버에만 흘러가게 됩니다.) <br/>

이것이 Rolling 배포방식이 되겠습니다 :) <br/>

## 장점
- 서비스 자원을 그대로 이용하기 때문에 낭비가 없습니다.
- 배포를 차례로 진행하기 때문에 상황에 따라 손쉽게 롤백이 가능하다는 점.

## 단점
- 구 버전과 신 버전이 공존하며, 호환성 문제가 발생할 수 있다는점.
- 트레픽이 몰리다보니 도메인에 따라, 사용자들이 덜 몰리는 새벽작업이 필연적일 수 있다는점.

## Blue/Green 배포

역시 총 3대의 인스턴스가 띄워져 있다고 가정하겠습니다. <strike>그림에서 서버가 3개 라 가정함... </strike>  <br/>

구버전이 배포되어 있는 인스턴스를 Blue, 신버전이 배포되어 있는 인스턴스를 Green 이라고 합니다. <br/>
색깔에 많은 의의를 두지 않아도 됩니다 :) <br/>

아무 의미가 없지만 레드 옐로 배포가 아닌 이유는, 우리가 보통 신호들에서 빨간불엔 안건ㄴ...<br/>

아래 그림과 같이 구버전과 운영중인 블루 서버가 있고, <br/>
신버전과 배포중인 그린 서버가 있습니다. <br/>

그린 서버가 배포가 완료되면, 블루 서버와 그린 서버의 역할을 바꿔줍니다. <br/>
그렇기 때문에 한번에 배포가 가능해 지겠죠 :)

## 장점
- Rolling 배포와 다르게 구버전과 신버전이 공존하지 않기 때문에 호환성 문제가 발생하지 않습니다.
- 한번에 배포가 가능하기 때문에 트레픽이 몰리는 새벽작업이 필요없습니다.

## 단점
- 운영 서버가 운영되고 있는 서비스 자원이 두배로 들게 됩니다.

## Canary 배포
카나리 배포는 조금 슬픈 이야기가 존재합니다. <br/>
19세기만 해도 탄광에서 가스 중독 사고가 많았답니다. <br/>
광부들에게 희망을 준 것은 카나리아 라는 새였습니다. <br/>

이 새는 일산화탄소와 메탄에 유독 약하기 때문에 광부들은 카나리아를 새장에 넣어 갱도로 들어가서 <br/>
석탄을 캐다가 카나리아가 이상증세를 보이면 즉시 탈출했다. (아차 싶을땐 역시 도망이 최고) <br/>
"이상 증세가 보이면 바로 롤백 이 핵심" <br/>

카나리 배포는 아래 그림과 같이 소규모 유저들에게 신버전을 배포하고, <br/>
이상증세가 보이면 바로 롤백을 진행하는 전략입니다. <br/>

그러나 이런 전략 덕분에 A/B 테스트가 가능해 지겠네요 :) <br/>
물론 점진적으로 신버전을 확장하다가 뒤늦게 이상증세를 확인하여 롤백하게 되는날엔 마음이 아프겠지만요 :) <br/>

## 장점
- 단계적인 전환 방식을 통해 부정적 영향을 최소화하고 상황에 따라 트래픽 양을 늘리거나 롤백할 수 있다.
- A/B 테스트가 가능하다.

## 단점
- 신버전과 구버전이 동시에 존재하기 때문에 버전관리가 까다롭다.

# 결론
흥미로운 이름의 배포전략이 존재했고, <br/>
모놀리식에서 마이크로서비스로 전환하면서 배포전략이 어떻게 변화했는지 알아보았습니다. <br/>

## 운영 관점에서 느낀 주관적인 생각과 궁금증
- 시간을 내어서 AWS 를 이용해 Blue Green 배포를 직접 진행해 보겠습니다. (나중에) <br/>
- 도메인별 전략에 따라, 전략이 다를것 같습니다. <br/>
- 결제 도메인의 경우는 어떤 전략 방식이 나을까 ? <br/>

## 참고자료
- https://hudi.blog/zero-downtime-deployment/
- https://loosie.tistory.com/781