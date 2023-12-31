# What is Class?

<br/>

## 1. 클래스의 기본

### (1) 클래스란

- 객체를 생성할 수 있는 일종의 **템플릿**
- 원래는 프로토타입을 기반으로 했지만, ES6부터 class 기반으로 **객체 지향 프로그래밍**을 할 수 있음
- 즉, 절차적으로 코드를 작성하기보다, 서로 밀접하게 연관되어 있는 것들을 객체로 구성하며, 객체끼리 서로 호환 가능하도록 함

### (2) 용어 정리

- 인스턴스(Instance): 클래스를 이용해 만들어진 객체
- 객체는 그냥 만들어진 객체고, **인스턴스는 어떤 클래스를 통해 만들어진 객체**

### (3) 용례

```javascript
// 생성자 함수의 function 대신 class로 작성
// 중괄호로 묶고 생성자 constructor 지정
class Fruit {
  // 생성자: new 키워드로 객체를 생성할 때 호출되는 함수
  constructor(name, emoji) {
    this.name = name;
    this.emoji = emoji;
  }
  // 생성자 밖에서 함수를 정의
  display = () => {
    console.log(`${this.name}: ${this.emoji}`);
  };
}

// new 키워드로 객체를 생성
// apple은 Fruit 클래스의 인스턴스임
const apple = new Fruit('apple', '🍎');
// orange은 Fruit 클래스의 인스턴스임
const orange = new Fruit('orange', '🍊');

console.log(apple); // Fruit {name: 'apple', emoji: '🍎', display: ƒ}
console.log(orange); // Fruit {name: 'orange', emoji: '🍊', display: ƒ}
console.log(apple.name); // apple
console.log(apple.emoji); // 🍎
apple.display(); // apple: 🍎

const obj = { name: 'ella' };
// obj는 객체고, 그 어떤 클래스의 인스턴스도 아님
// 차이: 객체는 그냥 만들어진 객체, 인스턴스는 어떤 클래스를 통해 만들어진 객체
```

<br/>

## 2. 재사용성 높이기 (static)

### (1) 인스턴스 vs. 클래스

- 인스턴스 레벨의 프로퍼티와 메소드는 각각 객체마다 서로 다른 데이터를 가지고 있지만
- 모든 객체마다 동일하게 참조해야 하는 속성이나 함수가 있다면 `static` 키워드로 인스턴스 레벨이 아닌 **클래스 레벨의 프로퍼티와 메소드**를 만들면 됨
- 이는 만들어진 인스턴스에 포함되지 않고, **클래스에 그대로 남아있게 됨**
- 즉, 클래스에 딱 한 번만 만들어주고, 각각의 인스턴스에는 들어있지 않기에 **클래스에 한번만 정의해서 재사용 가능**

```javascript
// static 정적 프로퍼티, 메서드
class Fruit {
  static MAX_FRUITS = 4;
  // 생성자: new 키워드로 객체를 생성할 때 호출되는 함수
  constructor(name, emoji) {
    this.name = name;
    this.emoji = emoji;
  }

  // 클래스 레벨의 메서드
  static makeRandomFruit() {
    // 클래스 레벨의 메서드에서는 아무것도 채워지지 않은 템플릿이기에 this 참조 불가
    return new Fruit('banana', '🍌');
  }

  // 인스턴스 레벨의 메서드
  display = () => {
    console.log(`${this.name}: ${this.emoji}`);
  };
}

const banana = Fruit.makeRandomFruit();
console.log(banana); // Fruit {name: 'banana', emoji: '🍌', display: ƒ}
console.log(Fruit.MAX_FRUITS); // 4

// static 사용 예시: object 굳이 만들지 않아도 됨
Math.pow();
Number.isFinite(1);
```

<br/>

## 3. 필드

### (1) 접근 제어자 (캡슐화)

- 한번 만들어진 다음에 외부에서 변경이 불가능하도록 만들고 싶을 때 사용
- 즉, **클래스 내부 상으로 필요한 데이터를 외부에서 보이지 않도록 숨겨놓는 것**
- private(#), public(자바스크립트 기본 상태), protected

### (2) 용례

```javascript
class Fruit {
  // 생성자 안에 써주면 딱히 안써도 되긴 함
  #name;
  #emoji;
  #type = '과일'; // 과일로 미리 초기화 시킴

  constructor(name, emoji) {
    this.#name = name;
    this.#emoji = emoji;
  }
  // 클래스 내부에서만 사용하도록
  #display = () => {
    console.log(`${this.#name}: ${this.#emoji}`);
  };
}

const apple = new Fruit('apple', '🍎');
//apple.#name = '오렌지'; → 한번 지정해 주면 #field는 외부에서 접근이 불가능
console.log(apple); // Fruit {#name: 'apple', #emoji: '🍎', #type: '과일', #display: ƒ}
```

<br/>

## 4. 접근자 프로퍼티 (Accessor Property) - 세터와 게터

### (1) 접근자 프로퍼티란?

- 클래스의 상태처럼 보이기는 하나, 실제로는 **함수**를 말함

### (2) 용례

```javascript
class Student {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // 단순히 first name과 last name 이라는 속성을 조합해서 보여주는 것
  // 때문에 속성의 한 부분이라고 간주되는 것들을 함수로 만들어야 할 때 get 사용
  get fullName() { // 접근자 프로퍼티로 함수 호출
    return `${this.lastName} ${this.firstName}`;
  }

  // set은 할당할 때 함수가 호출되며, 할당하는 value도 받아올 수 있음
  set fullName(value) {
    console.log('set', value); // set 김철수 → 인자로 전달됨
  }
}

const student = new Student('연수', '최');
student.firstName = '엘라';

// get을 쓰지 않으면
console.log(student.firstName); 
console.log(student.fullName());
// 행동이라기 보다, 단순히 객체의 상태. 즉, 단지 이름이라는 상태를 함수를 호출하듯이 쓰는게 어색함

// get을 쓰면
console.log(student.firstName); // 엘라
console.log(student.fullName); // 최 엘라 → get 호출

student.fullName = '김철수'; // 할당하면 set 호출되어 위에 인자로 전달됨
```

<br/>

## 5. 클래스의 확장 (상속)

### (1) 상속을 받지 않는 경우

- 아래 코드는 공통의 행동들이 있기에 중복 발생

```javascript
class Tiger {
  constructor(color) {
    this.color = color;
  }
  eat() {
    console.log('먹는다');
  }
  sleep() {
    console.log('잔다');
  }
}

class Dog {
  constructor(color) {
    this.color = color;
  }
  eat() {
    console.log('먹는다');
  }
  sleep() {
    console.log('잔다');
  }
  play() {
    console.log('논다'); // 추가
  }
}
```

### (2) 상속을 받는 경우

- 위와 같이 중복으로 쓰지 않고, 아래와 같이 공통의 템플릿을 만들어 줌

```javascript
class Animal {
  constructor(color) {
    this.color = color;
  }
  eat() {
    console.log('먹는다');
  }
  sleep() {
    console.log('잔다');
  }
}

// (1) Tiger는 extends를 통해 Animal 템플릿 상속
class Tiger extends Animal {}
const tiger = new Tiger('노랑');
console.log(tiger); // Tiger {color: '노랑'}

tiger.sleep(); // 잔다
tiger.eat(); // 먹는다

// (2) 상속 + 클래스 하나 더 추가
class Dog extends Animal {
  // 자식 클래스에서 constructor 정의하면
  constructor(color, ownerName) {
    // super로 부모에서 만들었던 것 다 상속받고
    super(color);
    // this로 자기 자신의 것을 받아옴
    this.ownerName = ownerName;
  }
  // dog는 자식 클래스 하나 더 있어서 추가
  play() {
    console.log('재밌게 노는 멍멍'); // 재밌게 노는 멍멍 → 아래에서 play 호출해서 출력됨
  }

  // 오버라이딩(overriding): 부모의 행동을 자식 행동으로 덮어씌우기
  eat() {
    // 부모의 행동을 유지하면서 추가로 무언가를 만든다
    super.eat(); // 먹는다
    console.log('강아지 냠냠'); // 강아지 냠냠 → 아래에서 eat 호출해서 출력됨
  }
  sleep() {
    console.log('갱얼쥐 잔다'); // 갱얼쥐 잔다 → 아래에서 sleep 호출해서 출력됨
  }
}

const dog = new Dog('초록', '엘라');
console.log(dog); // Dog {color: '초록', ownerName: '엘라'}

dog.play(); // play 클래스 호출
dog.eat(); // eat 클래스 호출
dog.sleep(); // sleep 클래스 호출
```

<br/>

## 6. 응용

### (1) quiz1

- 0 이상의 값으로 초기화 한 뒤 하나씩 숫자를 증가할 수 있는 카운터를 만들기

```javascript
class Counter {
  #value; // 외부에서 접근이 불가하도록 캡슐화
  constructor(startValue) {
    // 만약 startValue가 숫자가 아니거나 0보다 작다면
    if (isNaN(startValue) || startValue < 0) {
      this.#value = 0; // 0으로 초기화해서 시작해야 함
    } else {
      // 아니라면 startValue로 지정해서 그 숫자부터 카운팅
      this.#value = startValue;
    }
  }

  // 현재 값을 읽기만 하도록 접근자 프로퍼티로 함수 호출
  get value() {
    return this.#value;
  }

  // 유일하게 increment만을 이용해 value를 증가시키고 외부에서는 변경 불가
  increment = () => {
    this.#value++; // 1씩 증가
  };
}

const counter = new Counter(0);
counter.increment(); // 1
counter.increment(); // 2
console.log(counter.value);
```

### (2) quiz2

- 정직원과 파트타임직원을 나타낼 수 있는 클래스를 만들어 보기
- 직원들의 정보: 이름, 부서이름, 한 달 근무 시간
- 매 달 직원들의 정보를 이용해 한 달 월급을 계산할 수 있음
- 정직원은 시간당 10,000원
- 파트타임 직원은 시간당 8,000원

```javascript
class Employee {
  constructor(name, department, hoursPerMonth, payRate) {
    this.name = name;
    this.department = department;
    this.hoursPerMonth = hoursPerMonth;
    this.payRate = payRate; // 10,000원 or 8,000원
  }
  calculatePay() {
    return this.hoursPerMonth * this.payRate;
  }
}

class FullTimeEmployee extends Employee {
  // (1) 각각의 클래스 안에서 시간당 얼마를 받는지 알아야 하므로 & 정해져 있으므로
  // 각각의 인스턴스에서 결정하는 게 아니라, 클래스를 만들 때 얼마 받는지를 결정
  static #PAY_RATE = 10000;
  constructor(name, department, hoursPerMonth) {
    // Employee에 있는 생성자 불러오고 + 내부적으로 결정한 rate
    super(name, department, hoursPerMonth, FullTimeEmployee.#PAY_RATE);
  }
}

class PartTimeEmployee extends Employee {
  // 숫자를 그냥 적기보다, 이같이 상수변수를 활용해 의미부여 & 클래스 내부에서만 사용되어 외부에서 참조할 필요도 없고
  // 다른 값으로 변경되면 안되므로 static private으로 캡슐화
  static #PAY_RATE = 8000;
  constructor(name, department, hoursPerMonth) {
    super(name, department, hoursPerMonth, PartTimeEmployee.#PAY_RATE);
  }
}

// (2) 외부로부터는 몇시간 일하는지, 부서, 이름 등의 정보를 받아옴
const ella = new FullTimeEmployee('엘라', 'frontend', 22);
const chloe = new PartTimeEmployee('클로이', 'backend', 20);
console.log(ella.calculatePay());
console.log(chloe.calculatePay());
```
