---
title:  "macOS M1에 Jekyll 개발환경 구축하기"
date:   2022-05-31 22:47:40 +0900
categories: ruby
---

## 1. Homebrew 설치하기

공식 홈페이지에서 다운로드 및 설치  
[https://brew.sh/index_ko]()

## 2. rbenv 로 Ruby 환경 구성하기 

python 처럼 ruby 도 macOS 에 내장되어 있는데 시스템 ruby 를 사용할 경우 버전 호환 문제나 패키지 설치 시 권한 문제 등 여러 트러블을 야기하므로 `rbenv` 같은 버전/환경 관리자를 통해 쓰는 것이 보다 편리합니다.

### a. rbenv 설치

{% highlight shell %}
$ brew install rbenv
{% endhighlight %}

### b. ruby 설치 및 구성

{% highlight shell %}
# 설치 가능한 ruby 버전 찾기
$ rbenv install -l
2.6.10
2.7.6
3.0.4
3.1.2
jruby-9.3.4.0
mruby-3.0.0
rbx-5.0
truffleruby-22.1.0
truffleruby+graalvm-22.1.0

# 3.1.2 버전의 Ruby 설치
$ rbenv install 3.1.2

# 쉘이 열리면 rbenv를 초기화 하도록 구성
# 아래 명령 실행 후 나오는 eval 라인을 ~/.zshrc 에 추가
$ rbenv init
eval "$(rbenv init - zsh)"

# 터미널 재시작 이후 기본 Ruby 버전 설정
$ rbenv global 3.1.2

# Ruby 버전 확인
$ rbenv which ruby
/Users/ykstyle/.rbenv/versions/3.1.2/bin/ruby
{% endhighlight %}

### c. Jekyll 설치
{% highlight shell %}
$ gem install jekyll bundler
{% endhighlight %}


참고: [https://kbs4674.tistory.com/187]()