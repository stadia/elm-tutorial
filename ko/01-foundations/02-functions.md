# 함수 기본

이 챕터는 Elm 기본 문법 중에서도 중요한 다음 것들을 다룹니다: 함수, 함수 시그내쳐, 부분 적용, 파이프 연산자

## 함수

Elm 은 두 종류의 함수를 지원합니다:

- 익명 함수
- 이름이 있는 함수

### 익명 함수

익명 함수란 이름에서 보듯이, 이름이 없는 함수입니다:

```elm
\x -> x + 1

\x y -> x + y
```

백슬래시와 화살표 사이에 함수의 인자들이, 화살표 오른편에는 함수의 내용이 들어갑니다.


### 이름이 있는 함수

Elm 의 이름이 있는 함수는 이렇게 생겼습니다:

```elm
add1 : Int -> Int
add1 x =
  x + 1
```

- 첫 줄은 함수 시그내쳐입니다. Elm 에서는 필수는 아니지만, 함수의 목적을 명확히 하기 때문에 가급적 사용합니다.
- 이하는 함수의 구현입니다. 구현은 시그내쳐의 아래에 따라와야 합니다.

이 시그내쳐는 이렇게 말하고 있습니다: 인자로 정수 (Int) 를 받고 결과로 또다른 정수를 리턴함.

호출은 이렇게 합니다:

```
add1 3
```

Elm 에서 우리는 (괄호 대신) *공백* 으로 함수 호출을 표현합니다.

다음은 또 다른 이름 있는 함수입니다:

```elm
add : Int -> Int -> Int
add x y =
  x + y
```

이 함수는 두 인자를 받고 (둘 다 Int) 다른 Int 를 리턴합니다. 이렇게 호출할 수 있습니다:

```elm
add 2 3
```

### 인자가 없는 경우

Elm 에서 인자가 없는 함수는 상수입니다:

```elm
name =
  "Sam"
```

### 함수가 적용되는 원리

위에서 본 것처럼 두 인자를 받는 함수는 이런 식입니다:

```elm
divide : Float -> Float -> Float
divide x y =
    x / y
```

우리는 이 시그내쳐를 보고 두 실수를 인자로 받아 다른 실수를 리턴하는 함수라고 알 수 있습니다:

```elm
divide 5 2 == 2.5
```

하지만, 완전히 맞는 말은 아닙니다. Elm 에서는 사실 모든 함수는 하나의 인자만을 받고, 다른 함수를 리턴하기도 합니다.
위 함수를 사용해서 설명해 보겠습니다.

```elm
-- 이렇게 호출하면:

divide 5 2

-- 이와 같습니다:

((divide 5) 2)

-- First `divide 5` is evaluated.
-- 앞의 `divide 5` 가 처리됩니다.
-- 인자 `5` 가 `divide` 에 적용되고, 중간 함수가 리턴됩니다.

divide 5 -- -> 중간 함수

-- 이 중간 함수를 `divide5` 라고 부릅시다.
-- 이 중간 함수의 시그내쳐와 구현을 볼 수 있다면 다음과 같을 겁니다:

divide5 : Float -> Float
divide5 y =
  5 / y

-- 이는 `5` 가 이미 적용되어 있는 함수라고 볼 수 있습니다.

-- 그리고 다음 인자 `2` 가 적용됩니다.

divide5 2

-- 그러면 최종 결과가 리턴됩니다.
```

우리가 괄호를 쓸 필요가 없는 이유는 함수 호출이 **왼쪽 결합** 형태이기 때문입니다.

### 괄호로 묶기

다른 함수 호출의 결과를 함수에 전달할 때는 괄호로 호출을 묶어줄 필요가 있습니다:

```elm
add 1 (divide 12 3)
```

여기서는 `divide 12 3` 이 `add` 의 두번째 인자로 전달되고 있습니다.

다른 많은 언어에서는 이렇게 쓰겠지만요:

```js
add(1, divide(12, 3))
```

## 부분 적용

위에서 보았듯이 모든 함수는 하나의 인자만을 받고 다른 함수나 결과를 리턴합니다.
그 말은 `add` 같은 함수를 호출할 때도 하나의 인자만 사용할 수 있다는 것이죠. (예: `add 2` 는 *부분 적용된 함수* 를 리턴함)
이 리턴된 함수는 `Int -> Int` 의 시그내쳐를 가지고 있습니다.

`add 2` 는 첫 인자에 `2` 가 적용된 다른 함수를 리턴합니다. 두번째 인자를 리턴된 함수에 적용하면 `2 + ` 두번째 인자 가 됩니다:

```elm
add2 = add 2
add2 3 -- result 5
```

부분 적용은 여러분의 코드를 더 읽기 쉽게 해 주고, 함수간 상태를 전달하는 데 있어 Elm 에서 매우 유용합니다.

## 파이프 연산자

위에서 보았듯이 함수 호출은 중첩 가능합니다:

```elm
add 1 (multiply 2 3)
```

이건 단순한 예지만, 더 복잡해지면 어떨 지 봅시다:

```elm
sum (filter (isOver 100) (map getCost records))
```

이런 코드는 안쪽에서부터 계산되는 형태라 읽기 불편합니다. 파이프 연산자는 이런 경우에 더 읽기 쉬운 방식으로 표현 할 수 있게 해 줍니다:

```elm
3
    |> multiply 2
    |> add 1
```

이는 앞에서 설명한 부분 적용과 밀접한 관계가 있습니다. 예시에서 `3` 은 부분 적용된 함수 `multiply 2` 에 전달됩니다. 그 결과는 다시 또다른 부분 적용 함수 `add 1` 에 전달됩니다.

파이프 연산자를 사용해서 위 복잡한 예시를 이렇게 쓸 수 있습니다:

```elm
records
    |> map getCost
    |> filter (isOver 100)
    |> sum
```
