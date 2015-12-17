---
layout: page
title: 스프레드쉬트에서 데이터베이스로
subtitle: 들어가며
minutes: 2
---
> ## 학습목표 {.objectives}
>
> * 초기 스프레드쉬트가 작업하기 어려운지 설명한다.
> * 단일 테이블로 된 데이터베이스로 작업하는 장점을 설명한다.

출발점은 다음과 같은 2,937행을 갖는 `bibliography.csv` 라는 스프레드쉬트다:

|key|type|year|authors  |title                                        |journal|
|---|----|----|--------|---------------------------------------|--------|
|8SW85SQM|journalArticle|2013|McClelland, James L|Incorporating Rapid Neocortical Learning of New Schema-Consistent Information Into Complementary Learning Systems Theory.|J Exp Psychol Gen|
|85QV9X5F|journalArticle|1995|McClelland, J. L.; McNaughton, B. L.; O'Reilly, R. C.|Why There are Complementary Learning Systems in the Hippocampus and Neocortex: Insights from the Successes and Failures of Connectionist Models of Learning and Memory|Psychological Review|
|Z4X6DT6N|journalArticle|1990|Ratcliff, R.|Connectionist models of recognition memory: constraints imposed by learning and forgetting functions.|Psychological review|

다음을 알고자 한다:

*   사람 각각이 얼마나 많은 논문을 기여했는가?

*   누가 누구와 협업을 하는가?

만약 저자 한명만 관심있다면, 첫번째 질문에 답하는데 스프레드쉬트에서 저자명을 검색할 수 있다.
그리고 나서, 저자가 포함된 행을 선택하고 수작업으로 두번째 질문에 답하는데 공동저자를 계수하면 된다.
하지만, 모든 저자에 대해 하나씩 이런 작업을 수행하는 것은 몇일이 소요된다.
거의 확실히 실수도 할 것이다. 그리고 나면, 누군가 거의 확실히 또다른 더 큰 스프레드쉬트를 건네줄 것이고,
다시 작업을 반복해서 시작해야만 된다.

대안으로, 우리가 하려는 것이 다음에 나와있다:

1.  모든 논문에 모든 기여자에 대한 짝(키값, 저자)을 출력하는 작은 파이썬 프로그램을 작성한다.
    예를 들어, 작성한 프로그램이 스프레드쉬트 첫 세줄을 다음과 같이 변환한다:

    ~~~
    8SW85SQM McClelland, James L
    85QV9X5F McClelland, J. L.
    85QV9X5F McNaughton, B. L.
    85QV9X5F O'Reilly, R. C.
    Z4X6DT6N Ratcliff, R.
    ~~~

2.  프로그램을 변경해서 데이터베이스에 키값과 저자를 삽입하는 SQL `insert` 문장을 생성한다.

3.  SQL 쿼리를 사용해서 최초 질문에 답한다.

이렇게 프로그램을 작성하는 것이 질문 두개에 대한 답을 구하는데 상당한 작업처럼 보인다. 
하지만, 6줄 이상되는 어떤 스프레드쉬트에 대해서도, 상당한 시간을 절약해 준다:

*   데이터가 데이터베이스에 있으면, 다양하게 많은 질문에 묻고 답하기 쉽다.

*   다음번에 주어지는 스프레드쉬트에 작성한 도구를 재사용할 수 있다.

*   작업한 것에 대한 기록이 있다. (스프레드쉬트를 클릭하면 기록이 없다.)

*   더 정확할 것이다.

> ## 확률이 얼마나될까? {.challenge}
>
> 스프레드쉬트는 2,937행을 담고 있다.
> 전체 분석작업의 99%를 틀리지 않게 하는데, 손으로하는 전사작업은 얼마나 정확성이 있을까?
> 즉, 행당 오류율이 얼마나 되어야 전체 작업을 올바르게 완수하는데 0.99 확률이 될까?

> ## 손익분기점 {.challenge}
>
> 수작업으로 5분만 소요되는 작업을 (전산화해서) 10분 걸려 프로그램 작성한다면,
> 해당 작업을 두번 이상 수행한다면, 프로그램으로 작성할 가치가 있다.
> 유사하게, 특정한 저자와 공저자가 누구인지만 알아내려고 하고, 다른 질문은 전혀 없을 것이거나,
> 반복작업을 할 필요가 없다면, 수작업으로 스프레드쉬트를 검색하는 것이 데이터를 데이터베이스로 옮기는 
> 프로그램을 작성하는 것보다 아마도 더 빠를 것이다.
>
> 현재 수작업으로 하고 있는 작업을 선택하라. 매번 얼마의 시간이 소요되고,
> 얼마나 자주 수행하는지 추정하고, 대신에 작업을 프로그램으로 만드는데 얼마나 소요되는지 추정하라.
> 프로그래밍이 실질적으로 얼마나 시간을 절약해줄까?
> 얼마나 확신이 되나요?
