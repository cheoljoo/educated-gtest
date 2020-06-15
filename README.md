# educated-gtest
## book
- MS는 소프트웨어를 어떻게 테스트하는가?
- Google은 소프트웨어를 어떻게 테스트하는가?   -> 개발에서 자연스럽게 test할수 있도록 적용하는 역할을 담당

- https://github.com/imguru/gtest_0615

# 1일차
wget https://github.com/google/googletest/archive/release-1.10.0.tar.gz

bazel

g++
yum seach gcc

sudo yum install g++

/home/ec2-user/charles/googletest/googletest/scripts
 ./fuse_gtest_files.py  ~/charles/

  
  구글은 정적을 권장

  [ec2-user@ip-172-31-47-1 charles]$ ls
  googletest  gtest  release-1.10.0.tar.gz
  [ec2-user@ip-172-31-47-1 charles]$ g++ ./gtest/gtest-all.cc -c -I.


  [ec2-user@ip-172-31-47-1 charles]$ ar rcv libgtest.a gtest-all.o
  a - gtest-all.o

  libgtest.a 가 만들어져서 이제 환경 setup이 끝났다. (정적 library)


  g++ 1.cpp -I. -lgtest -L. -pthread



  [ec2-user@ip-172-31-47-1 charles]$ g++ googletest/googletest/src/gtest_main.cc
  -c -I.
  [ec2-user@ip-172-31-47-1 charles]$ ls
  1.cpp  a.out  build.sh  googletest  gtest  gtest-all.o  gtest_main.o
  libgtest.a  release-1.10.0.tar.gz
  [ec2-user@ip-172-31-47-1 charles]$ ar rcv libgtest.a gtest_main.o
  a - gtest_main.o

  이제 libgtest.a 에는 main이 포함되어져있다. 
  그러므로 이제는 1.cpp를 작성할때 main을 작성할 필요가 없다.


  동작 라이브러리가 우선 순위가 높음.

  BDD, TDD는 용어만 다르고 , test를 작성하는 방식은 크게 다르지 않다.
  // Test Case의 이름(Test Suite 이름)은 테스트 대상 클래스의 마지막에 Test를
  붙인 이름을 사용합니다.
  //   Calculator -> CalculatorTest / CalculatorSpec

  // 3A
  // 1. Arrange: 객체를 생성하고, 필요한 경우 설정하고 준비한다.  - Given
  // 2. Act: 객체의 작용을 가한다.                                - When
  // 3. Assert: 기대하는 바를 단언한다.                           - Then

  // TDD(테스트 주도 개발) vs BDD(행위 주도 개발)
  //  => 동일하지만, 용어가 조금 다릅니다.


