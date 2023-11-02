---
layout: post
title:  "객체지향과 의존성 주입(DI), 그리고 스프링"
date:   2023-11-02 00:03:25
categories: DataBase
tags: featured
---

## 1. 객체지향 5원칙(SOLID)

1.  단일 책임 원칙 (Single Responsiblity Principle)
    - 한 클래스는 하나의 책임만 가져야 한다.

2.  개방-폐쇄 원칙 (Open Closed Principle)
    * 소프트웨어 엔티티는 확장에 대해서는 열려 있어야 하지만 변경에 대해서는 닫혀 있어야 한다. -> 소프트웨어 요소를 새롭게 확장 혹은 변경되어도 클라이언트 코드의 변경은 없어야 한다.

3.  리스코프 치환 원칙 (Liskov Substitution Principle)
    * "서브 타입은 언제나 자신의 기반 타입(base type)으로 교체할 수 있어야 한다."

4. 인터페이스 분리 원칙 (Interface Segregation Principle)

5. **의존 역전 원칙 (Dependency Inversion Principle)**
    - "추상화된 것은 구체적인 것에 의존하면 안 된다. 구체적인 것이 추상화된 것에 의존해야 한다."
        "자주 변경되는 구체(Concrete) 클래스에 의존하지 마라"

솔직히 이렇게 봐도 모른다. 일단 읽어보고 넘어가고 아래에서 설명한다.
---

## 2. DIP 중점 코드

- 일반 예시 코드
    
```
    class BackendDeveloper {
        public void writeJava() { // Python
            System.out.println("자바 개발 시작");
        }
    }
    
    class FrontendDeveloper {
        public void writeJavascript() {
            System.out.println("JS 개발 시작");
    }
```   

```  
    public class Project {  // 클라이언트
    		private BackendDeveloper backendDeveloper = new BackendDeveloper();
    		private FrontendDeveloper frontendDeveloper = new FrontendDeveloper();
    
        public void startProject() {
            backendDeveloper.writeJava();
            frontendDeveloper.writeJavascript();
        }
    }
```
    

```    
    public static void main(String[] args) {
    		Project project = new Project();
    		projext.startProject();
    }
```

    
현재 있는 코드에서 BackendDeveloper 클래스의 writeJava 메서드를 Python으로 변경을 하고 싶다. 
그렇게 하기 위해서는 해당 메서드를 제외하고도 클라이언트 클래스인 Project를 수정해야한다.
딱 봐도 유지보수에 불편해 보이며 이것은 OCP(개방-폐쇄원칙)를 어긴 코드이다.


- 의존 관계
    <img src="https://user-images.githubusercontent.com/86454635/280039320-5da0d898-0ca5-42f1-ad5e-147aefdb5435.png" width="100%" height="80%">

위 의존관계 사진을 보면 Project class가 구체적인 class인 FrontendDeveloper와 BackendDeveloper를 의존하고 있는 것이다.
이는 *DIP(의존 역전 원칙)에 위배*되는 것이다. 



### 그냥 위의 코드는 완전 객체지향? 그게 뭔데? 먹는건가? 꿀꺽한 코드가 되는 것이다...


## 3. OCP 위반 해결
    
    ```
    interface Developer {
        void develop();
    }
    
    class BackendDeveloper implements Developer {
        public void develop() {  // Overriding
            writeJava();
        }
        public void writeJava() {
            System.out.println("자바 개발 시작");
        }
    }
    
    class FrontendDeveloper implements Developer {
        public void develop() {  // Overriding
            writeJavascript();
        }
        public void writeJavascript() {
            System.out.println("JS 개발 시작");
        }
    }
    
    ---
    
    public class Project {
    		private final Developer backendDeveloper = new BackendDeveloper();
    		private final Developer frontendDeveloper = new FrontendDeveloper();
    		// 다형성 - 부모 클래스로 자식 클래스의 제어가 가능
    
        public void startProject() {
            backendDeveloper.develop();
            frontendDeveloper.develop();
        }
    }
    ```

OCP를 해결하기 위해서 Developer를 추상화한 interface를 두었다. 이제 BackendDeveloper와 FrontendDeveloper class는 Developr를 implements하고 있다.
추상 메서드인 develop을 각 자식 class에서 overriding을 통한 구체화를 진행했다. 이를 통해서 클라이언트 class인 Project는 Develop이라는 class 변수로 develop이라는 메서드를 사용한다. 
아까와 같이 BackendDeveloper가 개발을 Python으로 변경한다고 해도, BackendDeveloper class의 추상메서드 구체화 부분만 손대면 변경이 가능하다.
즉, BackendDeveloper를 수정한다고 Project class를 수정할 필요가 사라지는 것이다.

    
- 의존 관계
    <img src="https://user-images.githubusercontent.com/86454635/280039444-3cc0f126-7b86-4afe-b7da-f8ef236d5f0d.png" width="100%" height="80%">

의존 관계는 Project class는 Developer interface를 의존하도록 바뀌었다. 
DIP 원칙인 구체화 class가 아닌 추상 class를 의존해야 한다. 라는 원칙도 지키게 되었다. 

## 3.1 과연 해결일까?


    public class Project {
    		private final Developer backendDeveloper = new BackendDeveloper();
    		private final Developer frontendDeveloper = new FrontendDeveloper();
    		// 다형성 - 부모 클래스로 자식 클래스의 제어가 가능
    
        public void startProject() {
            backendDeveloper.develop();
            frontendDeveloper.develop();
        }
    }

    
어떤 프로젝트는 BackendDeveloper가 필요 없고 DevOps가 필요하다고 한다. 어,,, BackendDeveloper class 이름을 DevOpsDeveloper로 바꿨다.
아 그렇게 되니 클라이언트 class인 Project의 new BackendDeveloper() 부분의 객체 인스턴스를 생성하는 부분을 바꿔주어야 하는 상황이 왔다. 
분명 DIP와 OCP를 해결한 줄 알았는데 아니였다. Project class가 BackendDeveloper와 FrontendDeveloper를 알고 있지 말아야 한다.
*아니 그러면 객체 생성을 지워? 그럼 어떻게 객체 인스턴스를 생성해서 사용하자?* 

        
## 4. DIP 위반 해결
답은 간단하다. 외부에서 넣어주자! 이것이 바로 *의존관계 주입, DI(Dependency Injection)*이다.

```
    public class Project {
    		private final Developer backendDeveloper;
    		private final Developer frontendDeveloper;
    		
    	public Project(Developer backendDeveloper, Developer frontendDeveloper) {
    	         this.backendDeveloper = backendDeveloper;
    	         this.frontendDeveloper = frontendDeveloper;
    	}
    
        public void startProject() {
            backendDeveloper.develop();
            frontendDeveloper.develop();
        }
    }
```
```
AppConfig
        public class AppConfig {
             public Project () {
                 return new Project(new backendDeveloper(), new FrontendDeveloper()); // -> Project가 원하는 객체 instance 부여
        	}
        }
```
```        
    public static void main(String[] args) {
        		AppConfig appConfig = new AppConfig();
            MemberService project = appConfig.project();
        		project.startProject();
    }
```

AppConfig라는 파일을 생성해서 객체 인스턴스를 생성해서 넣어주는 역할을 부여한다.
클라이언트 class가 원하는 객체를 넣어주는 방법은 다양하다. 생성자 주입, Setter등 위 코드는 생성자 주입 방식이다.
Project 인스턴스를 생성하면서 backendDeveloper, FrontendDeveloper 객체 인스턴스를 생성해서 부여하는 것을 확인할 수 있다.
 
        
- 의존 관계 - > SRP까지 커버
    <img src="https://user-images.githubusercontent.com/86454635/280039472-f7f3413d-371b-410f-bdd6-4578bba9e5a6.png" width="100%" height="80%">
        
의존 관계를 보자.
사용 영역에 있는 class들은 이제 서로 어떠한 연관이 없다. 각 코드에 서로의 코드도 없고 필요로 하나, 어떤 객체 인스턴스를 사용할지는 모른다. AppConfig에서 결정하고 오로지 사용만한다.
AppConfig는 인스턴스를 생성하고, 관리한다. 인스턴스 구성에 관련한 일을 하는 것이다.

이렇게 구성영역과 사용영역으로 나뉘었다. 이것이 SRP(단일 책임)원칙의 역할이다. 어찌 다른 원칙을 지키려고 하다보니 2가지의 원칙을 함께 클리어했다.

각 원칙이 따로 불리나 완전히 다른 것이 아니다. 이렇게 서로 끈끈하게 연관되어있다. 
각각을 따로 이해하고 외우려니 너무 힘들고 어려웠지만 이렇게 코드로 직접 작성해보며 객체지향 원칙에 대해 더 알게 되었다.
너무 두려워말자.


## 3. 스프링의 appConfig

 3.1 DI 컨테이너(IoC 컨테이너)
    - 제어의 역전 → 클라이언트 객체가 구현 객체를 생성하는 것이 아닌 외부에서 생성한 객체를 넣어주는 것(의존성 주입)
    - `@Bean` 을 통하여 객체를 생성하며 DI 컨테이너에서 관리

Spring은 위의 Bean 어노테이션을 붙이는 객체를 DI 컨테이너에서 관리해서 사용자가 크게 신경쓰지 않도록 돕는다.

```
@Configuration
public class AppConfig {
    @Bean
    public Project () {
         return new Project(new backendDeveloper(), new FrontendDeveloper());
	}
}

---
public static void main(String[] args) {
		ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
		Project project = ac.getBean("project", Project.class)

    MemberService project = appConfig.project();
		project.startProject();
}
```
위의 코드 처럼 원하는 ApplicationContext라는 DI 컨테이너에서 원하는 Bean을 조회하여 사용하게 된다.

더 나아가 아래와 같은 어노테이션을 이용해서 위의 코드보다 의존성 주입을 보다 간편하게 할 수 있게 된다.
- `@Service, @Repository, @Controller, @Autowired, @RequiredArgsConstructor`

이런식으로 Spring은 개발을 객체지향적으로 할 수 있도록 개발자를 돕는다. 이것들이 아니라면 굉장히 많은 코드로 인하여 복잡해졌을 것이다.
Spring... 개발자의 봄... 이것은 정말 겨울을 끝낼 봄이라는 이름으로 불릴 자격이 있을수 있을 것 같다.
