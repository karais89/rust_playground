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