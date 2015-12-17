---
layout: page
title: 스프레드쉬트에서 데이터베이스로
subtitle: 데이터 추출
minutes: 10
---
> ## 학습목표 {.objectives}
>
> * `csv.reader`를 왜 "랩퍼(wrapper)"로 부르는지 설명한다.
> * 짧은 파이썬 프로그램을 작성해서 CSV 파일에서 데이터를 추출한다.
> * 문자열 쪼개기를 사용해서 CSV같은 데이터 형식 자료를 파싱하는 위험을 설명한다.
> * 그럼에도 불구하고, 이 데이터에서 저자명을 추출하는데 문자열 쪼개기가 왜 수용가능한 접근법인지 설명한다.

첫번째 단계는 스프레드쉬트 행을 (키값, 저자명) 짝으로 변환하는 것이다. 
파이썬이 올바르게 스프레드쉬트를 읽어들이도록 확인하고서 시작한다:

~~~ {.python}
# count-lines.py
# 스프레드쉬트에 얼마나 많은 줄이 있는지 계수한다.
import sys

filename = sys.argv[1]
reader = open(filename, 'r')
count = 0
for line in reader:
    count += 1
reader.close()
print count
~~~

상기 코드가 이제는 칙숙해보여야 된다:
파일명이 첫번째 명령라인 인자(`sys.argv[1]`)로 주어졌다.
따라서, 파일을 열고, `for` 루프를 사용해서 한줄씩 읽어들인다.
매번 루프가 실행될 때, 1을 `count` 변수에 더한다;
루프가 종류될 때, 파일을 닫고 계수 결과를 출력한다.

상기 프로그램을 다음과 같이 실행한다:

~~~ {.input}
$ python code/count-lines.py data/bibliography.csv
~~~

물론, 결과는 다음과 같다:

~~~ {.output}
2937
~~~

그래서, 파이썬이 모든 행을 읽어들인 것을 알게된다.

다음 단계는 각 줄을 필드로 쪼개서 각 항목에 대한 키값과 저자명을 얻게된다.
필드는 콤마로 구분된다. 그래서 `str.split` 사용해서 시도해볼 수 있다.
하지만, 동작하지는 않는데 이유는 저자명에도 콤마가 포함되어서 그렇다("성, 이름"같은 형식으로 되어 있어서 그렇다).

대신에 취할 수 있는 조치는 선호하는 검색엔진에 도움을 청한다.
물론, "python csv"에 대한 검색결과는 [`csv` 라이브러리](https://docs.python.org/2/library/csv.html)가
나오고, 표준 파이썬 배포판의 일부이기도 하다.
라이브러리 문서에 일부 예제가 포함되어 있다. 몇번 실험을 한 뒤에, 다음과 같은 결과가 나오게 된다:

~~~ {.python
# read-fields.py
# CSV 파일에서 필드값을 제대로 읽어 오는지 확인한다.

import sys
import csv

raw = open(sys.argv[1], 'r')
reader = csv.reader(raw);
for line in reader:
    print line
raw.close()
~~~

작성한 프로그램은 참고문헌 파일을 열어서 시작한다(다시 한번, 첫번째 명령-라인 인자로 파일명을 넘긴다)
그리고 나서, `csv.reader` 메쏘드를 호출해서 파일주위에 래퍼를 생성한다.
`open`으로 생성된 기본 파일 객체가 한번에 한줄씩 읽어올 때,
`csv.reader`에 의해서 생성된 래퍼가 해당 라인을 올바른 지점에서 필드로 쪼갠다.
`csv.reader`는 해당 필드에 내장된 콤마, 특수문자, 신경쓰지 않아도 되는 다른 엄청난 것에 대해 어떻게 처리하는지 알고 있다.

올바르게 동작하는지 점검하려면, `csv.reader`에 의한 처리가 끝난 후에 각 줄을 출력하면 된다.
출력결과 중 첫 몇줄이 다음에 나와 있다:

~~~ {.input}
$ python code/read-fields.py data/bibliography.csv | head -5
~~~
~~~ {.output}
['8SW85SQM', 'journalArticle', '2013', 'McClelland, James L', 'Incorporating Rapid Neocortical Learning of New Schema-Consistent Information Into Complementary Learning Systems Theory.', 'J Exp Psychol Gen', '', '1939-2222', '', 'http://www.biomedsearch.com/nih/Incorporating-Rapid-Neocortical-Learning-New/23978185.html', '', '', '', '', '', '', '', '', '']
['85QV9X5F', 'journalArticle', '1995', "McClelland, J. L.; McNaughton, B. L.; O'Reilly, R. C.", 'Why There are Complementary Learning Systems in the Hippocampus and Neocortex: Insights from the Successes and Failures of Connectionist Models of Learning and Memory', 'Psychological Review', '', '', '', '', '', '', '', '', '', '', '', '', '']
['Z4X6DT6N', 'journalArticle', '1990', 'Ratcliff, R.', 'Connectionist models of recognition memory: constraints imposed by learning and forgetting functions.', 'Psychological review', '', '0033-295X', '', 'http://view.ncbi.nlm.nih.gov/pubmed/2186426', '', '', '', '', '', '', '', '', '']
['F5DGU3Q4', 'bookSection', '1989', 'McCloskey, M.; Cohen, N. J.', 'Catastrophic Interference in Connectionist Networks: The Sequential Learning Problem', 'The Psychology of Learning and Motivation, Vol. 24', '', '', '', '', '', '', '', '', '', '', '', '', '']
['PNGQMCP5', 'conferencePaper', '2006', 'Bucilu\xc7\x8e, Cristian; Caruana, Rich; Niculescu-Mizil, Alexandru', 'Model compression', 'Proceedings of the 12th ACM SIGKDD international conference on Knowledge discovery and data mining', '', '', '', '', '', '', '', '', '', '', '', '', '']
~~~

(프로그램 출력결과를 `head` 명령어로 실행해서 출력결과를 스크롤해서 다시 위로 올라가기 보다 첫 몇줄만 화면에 출력함에 주목한다.)
상기 결과는 정확하게 필요한 결과다: 키값이 각 리스트 첫번째 구성요소로 있고, 저자는 모두 네번째에 몰려있따.
프로그램을 변경해서, 단지 두 필드만 출력하게 변경하자:

~~~ {.python}
# display-fields.py
# 키값과 저자 모두를 화면에 출력한다.

import sys
import csv

raw = open(sys.argv[1], 'r')
reader = csv.reader(raw);
for line in reader:
    print line[0], line[3]
raw.close()
~~~

출력결과는 다음과 같다:

~~~ {.output}
8SW85SQM McClelland, James L
85QV9X5F McClelland, J. L.; McNaughton, B. L.; O'Reilly, R. C.
Z4X6DT6N Ratcliff, R.
F5DGU3Q4 McCloskey, M.; Cohen, N. J.
PNGQMCP5 Buciluǎ, Cristian; Caruana, Rich; Niculescu-Mizil, Alexandru
~~~

마지막 단계는 저자 다수를 갖는 행을 복수개 행으로 단일저자가 한줄에 나타나도록 변경한다.
이번이 `str.split` 메쏘드를 사용할 때다:
저자명이 세미콜론으로 구분되어 있어서, 저자 목록을 각 저자별로 나눌 수 있다.
또다른 루프를 사용해서 하나씩 결과를 화면에 출력한다:

~~~ {.input}
$ python code/display-authors-1.py data/bibliography.csv | head -10
~~~
~~~ {.output}
8SW85SQM McClelland, James L
85QV9X5F McClelland, J. L.
85QV9X5F  McNaughton, B. L.
85QV9X5F  O'Reilly, R. C.
Z4X6DT6N Ratcliff, R.
F5DGU3Q4 McCloskey, M.
F5DGU3Q4  Cohen, N. J.
PNGQMCP5 Buciluǎ, Cristian
PNGQMCP5  Caruana, Rich
PNGQMCP5  Niculescu-Mizil, Alexandru
~~~

이제 원하는 바에 가까워졌다.
하지만, 꼭 그렇지는 않다; 
저자명은 실제로 세미콜론과 공백으로 구분되는데 세미콜론만으로 구분했기 때문에,
각 줄마다 두번째와 이어진 명칭에 원치않는 공백이 앞에 온다.
세미콜론과 공백으로 쪼개면 어떻게 될까?

~~~ {.python}
# display-authors-2.py
# (키값, 저자명) 짝을 화면에 출력한다.

import sys
import csv

raw = open(sys.argv[1], 'r')
reader = csv.reader(raw);
for line in reader:
    key, authors = line[0], line[3]
    for auth in authors.split('; '): # 세미콜론 대신에, 세미콜론과 공백 사용
        print key, auth
raw.close()
~~~
~~~ {.output}
8SW85SQM McClelland, James L
85QV9X5F McClelland, J. L.
85QV9X5F McNaughton, B. L.
85QV9X5F O'Reilly, R. C.
Z4X6DT6N Ratcliff, R.
F5DGU3Q4 McCloskey, M.
F5DGU3Q4 Cohen, N. J.
PNGQMCP5 Buciluǎ, Cristian
PNGQMCP5 Caruana, Rich
PNGQMCP5 Niculescu-Mizil, Alexandru
~~~

그리고나면 작업완료:
데이터 추출 첫번째 작업 완료.
뭔가 유용한 것을 얻었기 때문에, 
후세를 위해서 저장한자:

~~~ {.input}
$ git init .
~~~
~~~ {.output}
Initialized empty Git repository in /Users/aturing/lessons/capstone-novice-spreadsheet-biblio/.git
~~~
~~~ {.input}
$ git add -A
$ git status
~~~
~~~ {.output}
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

  new file:   code/count-lines.py
  new file:   code/display-authors-1.py
  new file:   code/display-authors-2.py
  new file:   code/display-fields.py
  new file:   code/read-fields.py
  new file:   data/bibliography.csv
~~~
~~~ {.input}
$ git commit -m "Extracting (key, author) pairs from bibliography"
~~~
~~~ {.output}
[master (root-commit) 9db78ed] Extracting (key, author) pairsfrom bibliography
 6 files changed, 2996 insertions(+)
 create mode 100644 code/count-lines.py
 create mode 100644 code/display-authors-1.py
 create mode 100644 code/display-authors-2.py
 create mode 100644 code/display-fields.py
 create mode 100644 code/read-fields.py
 create mode 100644 data/bibliography.csv
~~~

> ## 가정사항 점검 {.challenge}
>
> 세미콜론과 공백이 아닌 세미콜론만으로 저자명 일부로 구분된다는 의구심이 있다.
> 이런 사항을 점검하는데 사용할 유닉스 쉘 명령어가 무얼까?

> ## 더 안전한 필드 쪼개기 {.challenge}
>
> 저자명 일부가 세미콜론과 공백이 아닌 세미콜론만으로 구부된다는 것을 알게됐다고 가정하자.
> 프로그램을 변경해서, 세미콜론으로 저자명 필드를 쪼개고 나서, 출력하는 동안에 원치않는 공백을 개별 저자명에서 벗겨내도록 하시오.
