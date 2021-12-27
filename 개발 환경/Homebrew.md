# Homebrew

우선 저는 애플실리콘 맥북을 이용하고있기 때문에 로제타2니 뭐니 해서 지원안하는 프로그램도 많고 복잡합니다... 텐서플로우 같은 것도 설정하기 정말 힘들다고하네요.

Homebrew는 macOS에서 다양한 패키지를 설치할 수 있도록 도와주는 프로그램입니다.
(리눅스의 apt와 유사)

## 현재 Homebrew 확인

먼저 brew가 설치되어있는지, 어떤 버전인지 확인합니다.

~~~bash
which brew
~~~

/usr/local/bin/brew가 출력된다면 구버전 /opt/hombrew/bin/brew가 출력되면 최신 버전입니다.

## 기존 Homebrew 삭제

Homebrew에서 3.0.0부터 애플실리콘을 정식으로 지원합니다. 3.0.0 이후 버전을 설치하기 전에 인텔용 Homebrew를 삭제합니다. 이미 3.0.0이후로 설치되어있다면 넘어갑니다.

1. 터미널에 다음 명령어를 입력합니다.

~~~bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall.sh)"
~~~

2. /usr/local 폴더의 Homebrew 폴더를 삭제합니다.

~~~bash
sudo rm -rf Homebrew
~~~



## 애플 실리콘 아키텍처로 iTerm2 / Terminal 앱을 실행

터미널 프로그램이 로제타2로 실행중인지 확인합니다.

응용 프로그램에서 터미널 프로그램을 선택해 **정보 가져오기** 또는 **Command + i** 를 눌러 확인합니다.

**Universal** 이면 로제타를 사용하여 열기를 체크 해제합니다.

![스크린샷 2021-11-05 오전 3.29.55](/Users/eomyoosang/Library/Application Support/typora-user-images/스크린샷 2021-11-05 오전 3.29.55.png){: width="100" }

## Homebrew 설치

터미널에 명령어를 입력하여 설치한다.

~~~bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
~~~



## 마무리

m1 거지같다. 그래도 발열/배터리 생각하면 버릴 수가 없다....

