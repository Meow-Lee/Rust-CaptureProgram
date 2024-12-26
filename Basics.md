# Rust Basics
## 1. 프로그래밍 개념
### 1-1. 변수와 가변성
- **변수**는 기본적으로 불변 => Rust가 제공하는 안정성과 쉬운 동시성을 활용하는 방식 중의 하나
```rust
fn main() {
  let x = 5;
  println!("The value of x is {x}");

  x = 6; // 컴파일 오류

  let mut y = 5;
  println!("The value of y is {y}");

  y = 6; // 정상
  println!("The value of y is {y}");
}
```
- **상수**는 불변 변수와 비슷하지만, mut과 함께 사용할 수 없으며 항상 불변
- const 키워드 사용
```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```
- **섀도잉** => 새 변수를 이전 변수명과 같은 이름으로 선언할 수 있음
- 첫 번째 변수가 두 번째 변수에 의해 가려졌다(shadowed)고 하여 섀도잉이라고 표현
```rust
fn main() {
  let x = 5; // 5
  let x = x + 1; // 6

  // 스코프
  {
    let x = x * 2;
    println!("The value of x in the inner scope is: {x}"); // 12
  }

  println!("The value of x is: {x}"); // 6
}
```

### 1-2. 데이터 타입
- 모든 값은 특정한 타입을 가지며 이는 어떤 종류의 데이터가 지정되고 있는지 알려줌
- Rust는 정적 타입의 언어로, 모든 변수의 타입이 컴파일 시점에 반드시 정해져 있어야 함
- **Scalar 타입** => 하나의 값을 표현하며, 정수 / 부동 소수점 숫자 / boolean / 문자 등 네 가지 Scalar 타입을 가지고 있음
- **정수형** => 소수점이 없는 숫자로, 부호가 있으면 i(signed), 없으면 u(unsigned)로 표현
- 확실히 정해진 경우가 아니라면 Rust의 기본값인 i32가 일반적으로 좋은 시작 지점이 됨
- isize나 usize는 주로 컬렉션 종류의 인덱스에 사용
- isize나 usize는 또한 프로그램이 동작하는 컴퓨터 환경에 따라 결정됨 (ex. 64-bit 아키텍처이면 64비트)
- **부동 소수점** => f32. f64 / f64가 기본 타입(CPU상에서 대략 비슷한 속도를 내면서 더 정밀하기 때문)
- **boolean 타입** => bool로 명시
- **문자 타입** => char가 가장 기본적인 알파벳 타입 (이모지, 억양 표시 있는 문자 등)
- **복합 타입** => 여러 값을 하나의 타입으로 묶을 수 있음 (**튜플, 배열**)
- **튜플 타입** => 다양한 타입의 여러 값을 묶어 하나의 복합 타입으로 만들며 고정된 길이를 가짐
```rust
fn main() {
  let tup: (i32, f64, u8) = (500, 6.4, 1);

  // 튜플로부터 개별 값을 얻어올 땐 패턴 매칭 사용
  let (x, y, z) = tup;
  println!("The value of y is: {y}");

  // 또는 아래와 같이 사용할 수 있음
  let x: (i32, f64, i8) = (500, 6.4, 1);
  let five_hundred = x.0;
  let six_point_four = x.1;
  let one = x.2;
}
```
- **배열 타입** => 배열의 모든 요소는 모두 같은 타입이어야 하고 고정된 길이를 가짐
```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
let first = a[0];
let second = a[1];
```
