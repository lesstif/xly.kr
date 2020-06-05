
# 개요

정적 코드 분석기는 큰 비용없이 소스 코드의 품질을 검사하고 예상되는
문제점을 찾을 수 있는 좋은 도구입니다.

제가 github에서 진행하는 [간단한 open source
프로젝트](https://github.com/lesstif/php-jira-rest-client)가
있는데 다른 개발자가
*[phpstan](https://github.com/phpstan/phpstan)* 이라는
정적 코드 분석 룰을 적용한
[PR](https://github.com/lesstif/php-jira-rest-client/pull/321)
을 보냈길래 배우고 적용해 보았는데 매우 유용한 도구같아서 사용법을 공유해 봅니다. 


> [주영익님의 php-annotated-monthly-december-2018](http://haah.kr/2018/12/15/php-annotated-monthly-december-2018-3rd/)에 의하면 정적 분석 도구을 사용하는 회사들은 다른
> 도구([psalm](https://github.com/vimeo/psalm),[phan](https://github.com/phan/phan))들도 같이 사용한다고 하니 여기에 소개된 도구들도 검토해 보세요. 


phpstan은 *composer* 로 설치할 수 있으므로 적용할 PHP 프로젝트 폴더에서 다음 명령을 실행하면 됩니다.

```bash
composer require --dev phpstan/phpstan
```

# 실행

[*phpstan*](https://phpstan.org/){.external-link}으로 정적 코드 분석을
하려면 analyse 명령을 주고 검사 대상 폴더를 지정해 주면 됩니다.

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: java; gutter: false; theme: RDark" theme="RDark"}
./vendor/bin/phpstan analyse [options] [<paths>...]
```
:::
:::

\

아래는 src 와 tests 라는 폴더내 .php 파일을 정적 검사하는 예제입니다.

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: java; gutter: false; theme: RDark" theme="RDark"}
./vendor/bin/phpstan analyse src tests
```
:::
:::

::: {.confluence-information-macro .confluence-information-macro-tip}
[]{.aui-icon .aui-icon-small .aui-iconfont-approve
.confluence-information-macro-icon}

::: {.confluence-information-macro-body}
온라인에서 phpstan 의 동작을 확인하려면 <https://phpstan.org/> 에
연결해서 테스트해 보면 됩니다.
:::
:::

\

### 커맨드 옵션 {#phpstan-PHP정적코드분석기(StaticAnalysisTool)로코드품질검사하기-커맨드옵션}

[*phpstan*](https://phpstan.org/){.external-link}은 커맨드 라인에서
옵션을 통해 좀 더 세밀한 동작 제어를 할 수 있으며 주요 옵션은 아래와
같습니다.

-   ***\--level, -l* **: 코드 검사 레벨을 지정하며 숫자가 클수록
    엄격하게 검사합니다. 최소는 0 이고 최대는 8이며 생략할 경우 0을
    사용합니다.
-   ***\--configuration,-c***: 설정 파일을 지정하며 생략할
    경우* phpstan.neon* 이나* phpstan.neon.dist* 파일을 찾습니다.
-   ***\--autoload-file, -a:***  app 가 custom auto loader 를 사용할
    경우 -a 옵션으로 오토로딩할 파일을 지정할 수 있습니다. 이 옵션은
    아래에 설명할 laravel 연동시에 꼭 필요합니다.
-   ***\--memory-limit:*** PHP 가 사용할 메모리 상한을 지정합니다.
    phpstan 이 메모리 부족으로 종료될 경우 이 옵션을 사용하며 *php.ini*
    와 동일한 문법을 지정합니다.

\

다음 명령은 *custom\_autoload.php* 파일을 오토로딩하고 src 폴더 밑에
.php 들을 level 3 규칙으로 검사합니다.

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: java; gutter: false; theme: RDark" theme="RDark"}
./vendor/bin/phpstan analyse src -l 3 -a custom_autoload.php
```
:::
:::

\

### 정적 분석하기 {#phpstan-PHP정적코드분석기(StaticAnalysisTool)로코드품질검사하기-정적분석하기}

아래와 같은 소스가 있을 경우 *phpstan* 을 돌리면 레벨에 따라 다른 에러가
발생합니다.

::: {#expander-467511097 .expand-container}
::: {#expander-control-467511097 .expand-control}
[]{.expand-icon .aui-icon .aui-icon-small
.aui-iconfont-chevron-down}[Click here to
expand\...]{.expand-control-text}
:::

::: {#expander-content-467511097 .expand-content}
:::
:::

\

level 이 0 일 경우 parameter *typehint* 가 잘못 되었을 경우에만 경고를
출력합니다. (![(info)](images/icons/emoticons/information.svg){.emoticon
.emoticon-information} *DateTimeImutable* 은 *DateTimeImmutable* 의
오타입니다.)

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeHeader .panelHeader .pdl style="border-bottom-width: 1px;"}
**level 0**
:::

::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: bash; gutter: false; theme: RDark" theme="RDark"}
./vendor/bin/phpstan analyze src -l 0


------ ----------------------------------------------------------------------------------------------
  Line   HelloWorld.php
 ------ ----------------------------------------------------------------------------------------------
  11     Parameter $date of method HelloWorld::sayHello() has invalid typehint type DateTimeImutable.
 ------ ----------------------------------------------------------------------------------------------
```
:::
:::

\

level 을 2로 설정하면 잘못된 parameter 를 사용하는 부분(13 line)과
PHPDoc 의 annotation 과 실제 method 파라미터(19 line)의 일치 여부를
비교해서 경고를 출력합니다.

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeHeader .panelHeader .pdl style="border-bottom-width: 1px;"}
**level 2**
:::

::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: bash; gutter: false; theme: RDark" theme="RDark"}
./vendor/bin/phpstan analyze src -l 2


------ ----------------------------------------------------------------------------------------------
  Line   HelloWorld.php
 ------ ----------------------------------------------------------------------------------------------
  11     Parameter $date of method HelloWorld::sayHello() has invalid typehint type DateTimeImutable.
  13     Call to method format() on an unknown class DateTimeImutable.
  19     PHPDoc tag @param for parameter $name with type int is incompatible with native type string.
 ------ ----------------------------------------------------------------------------------------------
```
:::
:::

\

level 이 3일 경우 선언된 method return type 과 실제 리턴 값의 type(21
line) 을 검사해 줍니다.

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeHeader .panelHeader .pdl style="border-bottom-width: 1px;"}
**level 3**
:::

::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: bash; gutter: false; theme: RDark" theme="RDark"}
./vendor/bin/phpstan analyze src -l 3


------ ----------------------------------------------------------------------------------------------
  Line   HelloWorld.php
 ------ ----------------------------------------------------------------------------------------------
  11     Parameter $date of method HelloWorld::sayHello() has invalid typehint type DateTimeImutable.
  13     Call to method format() on an unknown class DateTimeImutable.
  19     PHPDoc tag @param for parameter $name with type int is incompatible with native type string.
  21     Method HelloWorld::sayWorld() should return array but returns string.
 ------ ----------------------------------------------------------------------------------------------
```
:::
:::

\

### config 사용 {#phpstan-PHP정적코드분석기(StaticAnalysisTool)로코드품질검사하기-config사용}

실행시마다 옵션을 넣기는 번거로우므로 설정 파일을 통해 동작을 제어할 수
있으며 [neon](https://ne-on.org/){.external-link} 이라는 설정 파일
포맷을 사용합니다. [neon](https://ne-on.org/){.external-link} 은 저도
이번에 처음 알았는데 사람이 읽기 쉬운 데이타 직렬화 언어(data
serialization language)라고 합니다.

\

저도 PR 로 온 것과 매뉴얼을 보고 대략적으로 반영했는데 예제는 다음 2가지
파일을 참고하세요.

-   <https://github.com/lesstif/php-jira-rest-client/blob/master/phpstan.neon.dist>
-   <https://github.com/lesstif/php-jira-rest-client/blob/master/phpstan-baseline.neon>

\

2번째는 phpstan 에서 제외할 에러 유형을 지정하는 파일인데 정규식
형식으로 내용을 기술해 주면 되며 정규식에 사용되는 특수 문자는 \\ 를
붙여서 처리해 줘야 합니다.

cache 삭제 {#phpstan-PHP정적코드분석기(StaticAnalysisTool)로코드품질검사하기-cache삭제}
----------

phpstan은 빠른 검사를 위해  결과를 캐싱하는데 가끔 캐시가 잘못 됐는지
소스가 바뀌어도 검사 결과가 똑같은 경우가 있습니다.

이럴 경우 *sys\_get\_temp\_dir()* 를  호출 결과 폴더 하단에 phpstan
폴더를 삭제해 주면 됩니다.

Laravel 연동 {#phpstan-PHP정적코드분석기(StaticAnalysisTool)로코드품질검사하기-Laravel연동}
------------

\

Laravel 을 사용할 경우 *facade* 와 *magic method* 때문에  undefined
에러가 많이 나옵니다. [*ide
helper*](https://www.lesstif.com/pages/viewpage.action?pageId=29590101)
패키지를 사용해서 *facade*와 model class 에 대해 meta 파일을 만들어 줘야
합니다.

\

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeHeader .panelHeader .pdl style="border-bottom-width: 1px;"}
**laravel facade PHPDoc 생성**
:::

::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: bash; gutter: false; theme: RDark" theme="RDark"}
php artisan ide-helper:generate
```
:::
:::

\

모델 클래스 생성시에는 **아래 질문에 no 를 해야** Model 클래스를
변경하지 않고 별도의 메타 파일을 생성합니다.

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: java; gutter: false; theme: RDark" theme="RDark"}
php artisan ide-helper:models

 Do you want to overwrite the existing model files? Choose no to write to _ide_helper_models.php instead (yes/no) [no]:
```
:::
:::

\

meta 파일이 작성되었으면 *phpstan* 실행시 메타 파일을 오토로딩하도록 -a
옵션을 주고 실행하면 됩니다.

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: java; gutter: false; theme: RDark" theme="RDark"}
./vendor/bin/phpstan analyse -a _ide_helper.php -a _ide_helper_models.php app
```
:::
:::

\

매번 커맨드에 auto loading 옵션을 추가하기 번거로우므로 다음과 같이
*phpstan.neon.dist* 에 *autoload\_files* 항목을 추가해 주면 됩니다.

::: {.code .panel .pdl style="border-width: 1px;"}
::: {.codeHeader .panelHeader .pdl style="border-bottom-width: 1px;"}
**phpstan.neon.dist**
:::

::: {.codeContent .panelContent .pdl}
``` {.syntaxhighlighter-pre syntaxhighlighter-params="brush: text; gutter: false; theme: RDark" theme="RDark"}
includes:
    - phpstan-baseline.neon

parameters:
    level: 1
    paths:
        - app
    autoload_files:
        - _ide_helper.php
        - _ide_helper_models.php
    excludes_analyse:
        - app/Providers/NovaServiceProvider.php
```
:::
:::

\

마치며 {#phpstan-PHP정적코드분석기(StaticAnalysisTool)로코드품질검사하기-마치며}
------

phpstan 은 직접 사용해 보니 큰 비용없이 굉장히 유용한 정보를 제공해 주는
훌륭한 정적 코드 분석기입니다.

소스 코드의 품질에 관심이 많은 개발자라면 잠시 짬을 내서 학습하고
배워볼만한 도구라 생각합니다. 

\

Ref {#phpstan-PHP정적코드분석기(StaticAnalysisTool)로코드품질검사하기-Ref}
---

-   <https://phpstan.org/>
-   [Add Laravel support -
    https://github.com/phpstan/phpstan/issues/239](https://github.com/phpstan/phpstan/issues/239){.external-link}
-   <https://phpstan.org/user-guide/getting-started>
:::
:::
:::

::: {#footer role="contentinfo"}
::: {.section .footer-body}
Document generated by Confluence on 2020, Jun 05 13:26

::: {#footer-logo}
[Atlassian](http://www.atlassian.com/)
:::
:::
:::
:::
