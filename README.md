# Rust Playground

- Rust 학습을 위한 공간
- https://rinthel.github.io/rust-lang-book-ko
- 공식 튜토리얼 자체가 상당히 잘 만들어져 있긴 한데, 전체를 전부 보기는 힘들 것으로 보이고.. 기본적인 부분만 확인 진행해야 될 것으로 보인다.

## Hello, Cargo

```
cargo new hello_cargo
```

- vscode에서 launch.json 설정이 조금 힘든 부분이 있다.
```
<root>
L <project1>
L <project2>
```
구조라면 root에서 launch.json을 실행하지 말고 각 프로젝트 별 launch.json을 생성하고, 각 프로젝트를 별도의 vscode로 열어서 실행하는 방식을 취해야 될 것으로 보인다.

## 추리 게임 튜토리얼

- 일반적인 러스트 개념 학습
    - let, match, 메소드, 연관함수, 외부 크레이트

```
cargo new guessing_game --bin
```

`--bin` 바이너리용 프로젝트 생성 -> 이제는 기본으로 바이너리용 파일로 생성한다. 1.25 이상 버전 부터 변경 점. https://stackoverflow.com/questions/49706460/why-does-cargo-new-create-a-binary-instead-of-a-library

Cargo.toml 파일의 형태도 달라짐. 원래는 authors 공간이 있었는데 삭제되고, 필요시 수동으로 추가 하는 방식으로 변경 됨.

```
cargo run
```
- 터미널로 실행하는 방법
- VsCode로도 실행 가능

### 사용자가 추리한 값을 입력받아 그대로 출력하는 코드

```rust
use std::io;

fn main() {
    println!("Guess the number!");
    println!("Please input your guess.");
    let mut guess = String::new();
    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");
    println!("You guessed: {}", guess)
}
```
- use 는 C#의 using가 같다.
- fn은 함수 시작 키워드
- `let mut guess = String::new();` 값 변수에 저장
    - 러스트에서는 변수가 기본적으로 불변
    - 변수앞에 mut를 붙여야 가변변수를 만들 수 있다!!!!
    - new() 함수는 정적 함수. 새로운 값을 생성하기 위한 일반적인 이름이므로 많은 타입에서 찾아볼 수 있다.
- `io::stdin().read_line(&mut guess).expect("Failed to read line");`
    - read_line 사용자 표준 입력
    - &는 코드의 여러 부분에서 데이터를 여러 번 메모리로 복사하지 않고 접근하기 위한 방법을 제공하는 참조자
- 러스트의 이점 중 하나는 참조자이다. (안전성과 용이성)
    - 참조자는 변수 처럼 기본적으로 불변이다.
    - 따라서 가변으로 바꾸기 위해서는 `&mut guess` 형태를 취해야 한다.
- read_line의 경우 io:Result 값을 넘겨줌.
    - 열거형으로 불리기도 함 (enum)
    - `ok`, `err`
    - 프로그램이 에러가 날 경우 작동을 중지시키고, expect의 메시지를 출력한다.
    - 예외를 처리하는 방법
- `println!("You guessed: {}", guess)`
    - {} 변경자 값이 표시되는 위치를 나타낸다.


### 비밀번호 생성하기
- 크레이트를 사용한다 (패키지 & 라이브러리 개념)
- Cargo.toml 파일의 dependencies에 새로운 패키지 추가
```
cargo build
```

Rust 기본 표기법이 스네이크 표기법인가 보네.. 이건 좀 구린듯..

```
extern crate rand;

use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");
        
    println!("You guessed: {}", guess)
}
```
- `extern crate rand;`
    - 러스트에게 우리가 외부에 의존하는 크레이트를 사용한다고 선언
    - use rand로도 대체 가능한것으로 보임?
    - 없어도 문제 발생 안하는 것으로 보임.
- `rand::thread_rng().gen_range(1, 101);`
    - 시드 설정 후 랜덤값 반환

아래 명령어를 사용하면 문서가 보임 (로컬 프로젝트의 모든 의존 문서들)

```
cargo doc --open
```

### 비밀번호와 추리값을 비교하기

```
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal   => println!("You win!"),
    }
}
```
- `std::cmp::Ordering`
- `Ordering`
    - 열거형
- `match`
    - 표현문 - 흐름제어 연산문 나중에 배우는 걸로..
    - swtich 보다 더 우월한 부분이 있겠지?
    - C#의 패턴매칭과 비슷한 것 아닌가?
- 해당 소스는 컴파일 되지 않음.
- 러스트는 컴파일이 되지 않는 상세 이유도 출력해준다.

```rust
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = guess.trim().parse()
        .expect("Please type a number!");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal   => println!("You win!"),
    }
}
```

```rust
    let guess: u32 = guess.trim().parse()
        .expect("Please type a number!");
```
- guess 변수를 위에서 생성함
- shadow를 허락한다. (같은 값을 가리는) guess의 이름을 재사용한다.
    - 이게 좋은 코딩 습관인지는 모르겠네?

### 반복문을 이용하여 어러 번의 추리 허용
- loop 문 추가 -> while이랑 같은 문법

### 정답 이후에 종료하기
- break 문 추가
```
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

### 잘못된 입력값 처리하기

```
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```
- expect 메소드 호출을 match 표현식으로 바꾸는 것은 에러 발생 시 종료에서 처리로 바꾸는 일반적인 방법 이다.

## 보편적인 프로그래밍 개념
- 많은 프로그래밍 언어가 보편적인 핵심요소를 갖는다.
- Rust 고유의 개념은 다루지 않고, 보편적인 프로그램이 개념을 다룬다.

### 변수와 가변성
- Rust에서 기본 변수는 불변성이다.
    - 이유: 안전성과 손쉬운 동시성으로 인해
- Rust에서는 컴파일러가 변경되지 않는 값에 대한 보증을 해주고, 실제로 이는 바뀌지 않는다.
    - 추적이 필요 없기 때문(바뀌지 않기 때문)에 코드가 더 합리적으로 만들어진다.

#### 변수와 상수 간의 차이점들
- 상수는 mut의 사용이 허용되지 않는다. 기본 설정이 불변성이 아닌 불변성 그 자체다.
- 상수는 어떤 영역에서도 선언할 수 있다.
- 상수는 상수 표현식만 설정될 수 있고 함수 호출의 결과값이나 그 외에 실행 시간에 결정되는 값이 설정될 수 없다.

#### Shadowing
- 와.. 이거 안 쓰일 것 같은데.. 실제로 많이 쓰일 수도 있겠네..
- 우리가 몇 번 값을 변경할 수는 있지만 그 이후에 변수는 불변성을 갖게 됩니다.

```
fn main() {
    let x = 5;

    let x = x + 1;

    let x = x * 2;

    println!("The value of x is: {}", x);
}
```

- 또 다른 mut과 shadowing의 차이는 let키워드를 다시 사용하여 효과적으로 새 변수를 선언하고, 값의 유형을 변경할 수 있으면서도 동일 이름을 사용할 수 있다는 점 입니다

```
fn main() {
    let spaces = "   ";
    let spaces = spaces.len();
}
```
- 같은 변수 이름인데 타입 자체가 변경되는건 권장 되지 않는 부분 아닌가 싶기도 함.
- 어떻게든 안하는게 맞지 않나.. 싶음

### 데이터 타입들
타입의 종류
- 스칼라
- 컴파운드 (복합 타입)

> 러스트는 타입이 고정된 언어이다.
> 모든 변수의 타입이 컴파일 시에 반드시 정해져 있어야 한다.

#### 스칼라
- 하나의 값으로 표현되는 타입
- 정수형, 부동소수점 숫자, boolean, 문자
- 정수형의 경우 확실하게 정해진 경우가 아니라면 러스트 기본 값인 `i32` 일반적인 선택
- 부동소수점은 `f32`, `f64`가 있고 기본 값은 `f64` 이다.
- boolean값은 true, false 둘 중 하나만 가질 수 있다.
- 문자 타입 `char` 러스트에서는 해당 값이 유니코드 스칼라로 표현하는 값이라 아스키 보다 훨신 넓은 데이터를 저장할 수 있다.

#### 복합 타입들
- 튜플
    - 일반적인 튜플과 거의 같음
- 배열
    - 러스트의 배열은 다른 언어와 다르게 고정된 길이를 갖는다. 한번 선언되면 이들은 크기가 커지거나 작아지지 않는다.
    - 힙보다 스택에 할당하는 것을 원할때.

### 함수 동작 원리
- 함수는 러스트에 녹아 들었다.
- 러스트는 스네이크 표기법을 사용한다.
    - 개인적으로 별로 좋아하지 않는다.
- 함수에서 표현식은 세미콜론으로 끝나지 않는다. (반환값)
    - 와 문법이 약간.. 괴상하네
    - return을 써서 값을 반환할수도 있긴 함..

```
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}
```

### 주석
- 일반적인 주석과 같음
```
fn main() {
// Hello, world.
}
```