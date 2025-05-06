---
sidebar_position: 3
---

# Sizes and Tensor

nn 언어의 핵심 개념 중 하나는 컴파일 타임에 텐서의 형태(shape)를 추적하는 능력입니다. 이것은 `Size` 변수와 `Tensor` 타입을 통해 가능해집니다.

## Tensor의 기본 개념

텐서(Tensor)는 머신러닝, 특히 딥러닝에서 널리 사용되는 다차원 배열입니다. 스칼라(0차원), 벡터(1차원), 행렬(2차원) 및 그 이상의 차원으로 확장될 수 있습니다.

nn에서 Tensor는 그 형태(shape)를 컴파일 타임에 정확히 알 수 있도록 설계되어 있어, 런타임 에러를 사전에 방지할 수 있습니다.

## Size 변수

Size 변수는 텐서의 차원 크기를 지정하는 데 사용됩니다. 일반적인 프로그래밍 언어의 변수와 달리, Size 변수는 컴파일 타임에 추적되고 계산됩니다.

### Size 변수의 특징

- Size 변수는 정수 값을 가집니다.
- 다항식 형태로 표현될 수 있습니다 (예: `input * 2 + 1`).
- 사칙연산(+, -, *, /)이 지원됩니다.
- 모든 계산은 정수 단위에서 이루어집니다.
- Size 변수는 함수 호출 시 값이 결정될 수 있습니다.

### Size 변수 예시

```nn
// Size 변수 'input'과 'output'을 사용한 함수 정의
Linear[input, output](x: Tensor[input]): Tensor[output] = 
  |> MatMul(Trainable[input, output]('weight'))
  |> Bias(Trainable[output]('bias'))

// Size 변수에 구체적인 값 할당
result = Linear[64, 32](input_tensor)  // input=64, output=32로 지정됨
```

## Tensor 타입

Tensor 타입은 nn 언어에서 다차원 데이터를 표현하는 기본 타입입니다. Tensor의 각 차원 크기는 Size 변수 또는 구체적인 정수값으로 지정됩니다.

### Tensor 선언 문법

```nn
// 기본 형태
x: Tensor[dimension1, dimension2, ...]

// 예시
x: Tensor[64, 64, 3]  // 64x64 크기의 RGB 이미지
y: Tensor[batch, sequence_length, embedding]  // 동적 크기의 텐서
```

### Size 변수와 Tensor의 관계

Tensor 타입 선언에서 사용되는 Size 변수는 함수의 매개변수나 함수 호출을 통해 값이 결정됩니다:

```nn
// H, W, C는 Size 변수로 자동 인식됨
ConvBlock[Channel](x: Tensor[H, W, C]) = 
  |> Conv2D[3, 1, 1, Channel]()
  |> BatchNorm()
  |> ReLU()
```

여기서 `H`, `W`, `C`는 Tensor의 차원을 나타내는 Size 변수로, 함수 내에서 자동으로 인식됩니다. `Channel`은 함수의 Size 매개변수로 명시적으로 선언됩니다.

## 다항식 계산 기능

nn의 강력한 기능 중 하나는 Size 변수에 대한 다항식 계산을 컴파일 타임에 수행할 수 있다는 점입니다.

### 다항식 표현 예시

```nn
// 다항식 형태의 Size 표현
x: Tensor[input * 2 + 1]
y: Tensor[(input + output) / 2]

// UNet에서의 채널 스케일링 예시
UNetEncoder[Channel](x: Tensor[H, W, C]) =
  |> ConvBlock[Channel]()
  |> MaxPool[2]()

// Channel, Channel*2, Channel*4, Channel*8로 점점 증가하는 채널 사용
result = UNet[64](image)  // 기본 채널 수를 64로 설정
```

### 다항식 치환

다항식 내의 변수는 다른 다항식으로 대체될 수 있으며, 이 과정에서 새로운 다항식이 생성됩니다. 예를 들어, `x^2 + 5`에서 `x`를 `y + 3`으로 대체하면 `(y + 3)^2 + 5`가 되고, 이는 `y^2 + 6y + 14`로 계산됩니다.

## 함수(플로우)에서의 Size 변수 사용

nn의 함수(플로우)에서는 Size 변수를 다양한 방식으로 활용할 수 있습니다.

### 함수 선언에서의 Size 매개변수

```nn
// Channel은 Size 매개변수
ConvBlock[Channel](x: Tensor[H, W, C]) = ...

// input과 output은 Size 매개변수
Linear[input, output](x: Tensor[input]): Tensor[output] = ...
```

### 함수 호출에서의 Size 인자

```nn
// Size 인자로 64와 32를 전달
result = Linear[64, 32](input_tensor)

// Size 인자로 다항식 표현 전달
result = ConvBlock[input_dim * 2](tensor)
```



## 컴파일 타임 오류 감지

nn의 가장 큰 장점 중 하나는 많은 오류를 컴파일 타임에 감지할 수 있다는 점입니다.

### Shape Mismatch 오류

PyTorch나 TensorFlow에서는 런타임에 발견되는 Shape Mismatch 오류를 nn에서는 컴파일 타임에 감지할 수 있습니다:

```nn
// 오류: input_dim과 layer_dim의 크기가 일치하지 않음
x: Tensor[input_dim]
layer = Linear[layer_dim, output_dim]
result = layer(x)  // 컴파일 타임 오류 발생
```

### 비정상적인 Shape 오류

Size 변수 계산 결과가 자연수가 아닌 경우(음수, 0, 실수 등)에도 컴파일 타임에 오류가 감지됩니다:

```nn
// 오류: 텐서의 크기는 양의 정수여야 함
x: Tensor[input_dim - 100]  // input_dim이 100보다 작을 경우 오류
```



## 제약사항

현재 nn의 Size 변수는 다음과 같은 제약사항이 있습니다:

- 사칙연산만 지원합니다.
- 모든 계산은 정수 범위 내에서 이루어져야 합니다.
- 비정수 값이나 음수 값은 텐서의 차원으로 사용할 수 없습니다.

향후 함수 호출과 같은 추가 연산이 지원될 예정이지만, 정수 단위 계산이라는 제약은 유지될 것입니다.