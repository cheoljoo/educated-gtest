# educated-gtest
## book
- MS는 소프트웨어를 어떻게 테스트하는가?
- Google은 소프트웨어를 어떻게 테스트하는가?   -> 개발에서 자연스럽게 test할수 있도록 적용하는 역할을 담당

- https://github.com/imguru/gtest_0615

# gtest
## 1일차
wget https://github.com/google/googletest/archive/release-1.10.0.tar.gz

bazel : google 의 새로운 framework

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


- 암시적 test를 사용하는것이 가장 좋다. Setup / TearDown
    - TEST_F 를 사용하는게 가장 좋다.
    - virtual void Setup() override {  }
    - 이게 xUnit Test Pattern  이다.
- 공유 fixture : 성능적으로 문제가 있을때는 suite 단위로만 초기화를 한번만 하면 된다. 이때는 SetUpTestSuite() 와 TearDownTestSuite()를 사용하면 된다. static이다.
    - static void SetUpTestSuite()

- EXPECT_THROW 를 이용하여 try catch 도 시험할수 있다. 
    - 여기에 FAIL , SUCCESS() 를 사용하면 더 좋다.

## 2일차
- google test는 private 필드나 메소드에 대한 접근이 가능하게 하는 FRIEND_TEST 라는 기능을 제공
    - charles Q : 실 코드에 들어가야 하는데 문제가 없을까?
        - #ifdef  , #define private public 보다는 좋다. 

- 접근하고자 하는 멤버 변수나 또는 함수의 접근 지정자가 protected인 경우 자식 클래스는 부모의 protected를 public으로 변경할수 있다.
```cpp
    class TestUser : public User {
        public:
            using User::age;   // protected -> public
    };
```

- 로버트 C 마틴
    - private method의 목적은 public method의 가독성을 높이기 위해서 사용해야 한다. - clean code - 

- public method에 대해서만 test case를 만들면 된다. 대신 private 함수들은 public에서 호출되어야 한다. 

- 테스트 전용 하위 클래스 패턴을 사용할 경우에는 시험하려는 class가 virtual 함수여야 한다. (제약사항)
    - 이래야 , 자식 클래스에서 이를 대체 가능하다.

- Parameterized
    - 주의 할 점 : GetParam() 안 test안에 한번만 수행해야 한다.  필요하면 한번 받아서 그 변수를 다시 사용하면 문제 안 될 듯!
    - 1.18에서는 GetParam() 을 여러번 testcase안에 사용해도 같은 값을 줍니다. 주의 안해도 될 듯!!! :-)
    - 범위값 (Range) 사용 ,  랜덤값 테스트 ?
    - **gtest/gtest.h  를 보면 설명이 자세히 나온다.**
    - 함수를 통해서 값을 dynamic하게 set하는 것도 가능 10*_4.cpp 에서 확인 가능 : random도 처리 가능하다.

- tuple 의 max는 10개라고함.

# gmock
## 2일차
- fuse 수행
[ec2-user@ip-172-31-47-1 scripts]$ ls
fuse_gmock_files.py  generator  gmock-config.in  gmock_doctor.py  upload_gmock.py  upload.py
[ec2-user@ip-172-31-47-1 scripts]$ pwd
/home/ec2-user/charles/googletest/googlemock/scripts
- gmock-gtest-all.cc 에  gtest / gmock 모두 모아둔 것이라고함.  이것을 libgtest.a 로 만들어 build.sh 를 만들면 된다.
- fuse를 이용하여 하나로 모은다. gmock-gtest-all.cc

- g++ ./googletest/googlemock/src/gmock_main.cc -c -I
- ar rcv libgtest.a gmock-gtest-all.o gmock_main.o

- 1.10 이전
    - MOCK_METHOD숫자
    - generator/gmock_gen.py 를 이용하여 mock을 자동으로 만듦    -> 자동 생성됨.
- 1.10 이후
    - 새로운 형태의 매크로를 제공
    - MOCK_METHOD(반환타입 , 함수 이름 , 함수 인자 , 한정자)
        - 함수 인자와 한정자는 ( ) 로 묶어야 한다.
    - MOCK_METHOD(void , Write , (Level level, const std::string& msg) , (override));

## 3일차
- MOCK은 실제 함수의 구현을 호출하지 않는다. 
    - 함수에 대해서 호출된 사실만 기록할 뿐이다. 그래서 private 함수도 mocking할수 있다.
- 사용하는 mock만 MOCKING하고 , 나머지는 그냥 사용하겠다고하면
    - using Foo::Add;를 넣으면 된다. 16*_3.cpp
        - 부모 클래스에서 기본 구현이 되었을 경우에만 사용 가능

- template mocking  16*_4.cpp

- template을 기반으로 작성될때, 함수에 대한 구현이 존재해야만 소스도 존재
    - template은 상속으로 만들지 않는다.  함수가 암묵적으로 약속되어있다고 가정하므로 , 바로 기능을 구현한다.
    
- ON_CALL
    - mock에 대한 함수의 호출을 다른 객체에게 위임하거나, 결가를 고정할수 있는 매크로
    - ON_CALL(*this, Add).WillByDefault([this](int a ,int b){
            return fake.Add(a,b);       // fake 는 실제로 수행되어 값을 확인하기 위해서 (DB에서는 모든 것을 만들수 없으니...)
      });

- Hamcrest style은 EXPECT_THAT을 사용하며 , 뒤에 Matcher들을 그대로 사용할수 있다.
    - MatchesRegex 라는 정규 표현식도 제공

- Field & Property
    - Field : 멤버 변수   ::testing::Field
    - Property : Accessor Method (접근자 메소드) - Getter / Setter     ::testing::Property
    - EXPECT_THAT(user , Field(&User::age,Eq(42)));    <= EXPECT)EQ(user.age , 42);

- **Matching Containers**

- EXPECT_CALL은 순서를 체크하지 않는다.
    - 호출 순서를 검증하고 싶다.
        - InSequence 객체만 생성하면 호출 순서까지 check한다.
        - using ::testing::InSequence;


### 추가적으로 풀어본 문제로 소스가 이전과 이후가 변경된 파일
- \_3로 복사해서 만드네요.    11*_3.cpp
    - NiceMock
- 12*_3.cpp
    - throw
- 13*_2.cpp
    - fake
    
