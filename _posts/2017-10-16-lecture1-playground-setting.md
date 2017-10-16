---
layout: post
title: 171016_iOS_Level2_playground 환경 따라하기, 용어정리, git사용하기
tags: iOS 수업정리 git xcode playground sdk framework library codesquad
categories: Codesquad
---



> #### 코드스쿼드 마스터즈코스 첫 번째 수업.
>
> 스위프트 개발환경 세팅과 playground 사용해보기, xcode 단축키 사용해보기, git을 local - remote 간 자유자재로 사용하는 법을 익혔고 본격적으로 공부하기에 앞서 몰랐던 용어를 정리하는 시간이었다. 
>
> git은 지킬 블로그 빌드 때 터미널을 사용해서 써왔기때문에 어느정도   용어 정리랑 조사가 잘 되지 않아서 바로 다른 실습을 하려고 했지만, 이번 아니면 용어 조사를 제대로 해 볼 여유가 없을지도 몰라서 가능한 최대하게 찾아보았다. 



#### git 사용하기

~~~
$ git init .

$ ls -al 명령어로 git설치확인

$ git remote add origin https:// ~

로컬과 리모트 저장소가 연결된 상태.

이후 커밋 / 푸시 / 풀 과정 진행하면 됨

$ git add .

$ git commit -m "블라블라"

$ git push origin master
~~~

>  git init된 폴더를 바꾸고싶을때 
>
> (하나의 ios workspace - git 버전관리용으로 로컬에 만들고 그 바로 밑 경로에 폴더를 만들어서 각 폴더마다 한 remote repo로 지정하고싶을때, 이미 한 로컬 저장소를 만들고 repo에 연결했다면 )
>
> 하위 폴더(프로젝트 파일들)를 무작정 파인더에서 옮기면 숨어있는 git파일들이 안옮겨질 수 있음
>
> git init된 파일 (remote된 repo)을 통째로 한 폴더의 하위로 만들면 가능!

- git Readme.md 파일 : 주로 해당 소스코드의 전반적인 introduction을 기록할때사용


- git wiki : 소스코드에 대한 것을 문서화 할때 사용
  - ex) 프로덕션 코드를 올렸을때 새로운 버전이 릴리즈될 때마다 변경사항을 쓴다던지, 버전에 대한 설명을 쓴다던지 할때 활용
- git 사용법 강의로 멤버분들이 많이 추천해주신 [생활코딩 - 지옥에서 온 Git](https://opentutorials.org/course/2708)





#### Xcode

#### 단축키 (계속해서 찾아보고 추가할 예정)

shift + option + cmd + N : 새로운 playground

cmd + N : 새로운 파일



출력할때 newline (/n 없이 출력하기)

```swift
print("Hello", terminator:"")
```





#### Lecture1에 있던 용어정리 미션들 

- Xcode : 애플이 개발한 macOS 통합 개발 툴
- Playground : 사소한 테스트 코드를 작성하고 바로 실행해볼수있는 타입(?소스코드?파일?) (Swift는 프로그래머들이 사소한 테스트 때문에 코드를 빌드하고 실행하는 과정에 시간을 낭비하는 일 없이 즉시 코드를 테스트하고 결과를 볼 수 있도록 하는 혁신적인 요소인 playgrounds를 지원합니다.)
- SDK (Software Development Kit) : 소프트웨어 개발자가 사용하여 응용프로그램을 만들 수있게하는 개발 도구의 집합. 특정한 소프트웨어나 플랫폼을 이용해서 프로그램(소프트웨어)을 개발할 때 이를 돕는 개발도구의 집합이다.
  - 일반적으로 [소프트웨어 기술자](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EA%B3%B5%ED%95%99)가 사용하여 특정한 [소프트웨어](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4) 꾸러미, [소프트웨어 프레임워크](https://ko.wikipedia.org/wiki/%EB%BC%88%EB%8C%80), [하드웨어](https://ko.wikipedia.org/wiki/%ED%95%98%EB%93%9C%EC%9B%A8%EC%96%B4) 플랫폼, [컴퓨터 시스템](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%EC%8B%9C%EC%8A%A4%ED%85%9C), [게임기](https://ko.wikipedia.org/wiki/%EA%B2%8C%EC%9E%84%EA%B8%B0), [운영 체제](https://ko.wikipedia.org/wiki/%EC%9A%B4%EC%98%81_%EC%B2%B4%EC%A0%9C) 등을 위한 [응용 프로그램](https://ko.wikipedia.org/wiki/%EC%9D%91%EC%9A%A9_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8) 등을 만들 수 있게 해주는 개발 도구의 집합이다. (ref. [위키백과](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EA%B0%9C%EB%B0%9C_%ED%82%A4%ED%8A%B8))
- 프레임워크 : 프로그램 기본 구조(뼈대) 소프트웨어 프레임워크는 SDK의 한 종류, 단순히 함수들을 모아 놓은 것이 아니라 특정한 프로그램 제작에 필요한 필수 함수들을 모두 포함하고 있으며 프레임워크를 사용한다는 것은 해당 프레임워크가 제시하는 방식으로 개발을 한다는 것을 뜻합니다. 
  - ex) MVC 패턴 개발에서 @PostMapping / @ModelAndView 같은 것 처럼 개발하는 틀이 정해져있고 개발자는 그에 맞는 value들만 맞는 위치에 넣어주면 되는 것
- 라이브러리 : 재사용이 필요한 기능으로 반복적인 코드 작성을 없애기 위해 언제든지 필요한 곳에서 호출하여 사용할 수 있도록 Class나 Function형태로 만들어짐. ([프레임워크와 라이브러리에 대한 설명 참고 블로그.](http://jokergt.tistory.com/89))
  - 어떤 형태의 코드든지 개발 도중에 필요한 기능이 있으면 이미 구현된 기능 코드를 쓸 수 있는 것


- 소스코드 : 소프트웨어 프로그램 설계도. (디지털기기의 소프트웨어 내용을 프로그래밍 언어로 나타낸 설계도.)