---
sidebar_position: 1
---

# Introduction

> nn docs에 오신 것을 환영합니다.

`nn`은 뉴럴 네트워크 모델 작성을 돕기 위해 작성된 언어입니다.

마치 타입스크립트가 자바스크립트에게 선사했던 마법처럼, `nn` 또한 비슷한 경험을 제공합니다.

## Why nn

기존의 pytorch, tensorflow, jax와 같은 메이저 머신 러닝 라이브러리가 공통적으로 겪는 문제가 있습니다.

```python
x = torch.randn(64, 64, 3) # input tensor
x = x.permute(2, 0, 1).unsqueeze(0) # rearrange dimension
x = model(x) # pass model

# then, what is the shape of `x`?
```

위 예시 코드에서의 정답은 '모른다' 입니다.

기존의 라이브러리는 python(혹은 드물게 다른 범용 프로그래밍 언어)에 의존합니다.

이로 인해 값의 대략적인 타입은 추적할 수 있어도 코드를 실행하기 전까지는 실제 값의 형태는 알 수 없습니다.

다음은 간략화된 nn 코드입니다.

```nn
x: Tensor[64, 64, 3] # input tensor

y = Conv2D[3, 1, 1, 16](x) # pass convolution
```

이 상태에서 lsp 등의 지원을 받아 y에 hover를 해보면

```nn
(value) y: Tensor[64, 64, 16]
```
와 같은 결과를 얻을 수 있습니다.

컴파일 타임에 모든 값의 형태를 추적하며, [다항식 계산 기능](/docs/polynomial)이 존재해 설령 다항식 형태로 있더라도 추적이 가능합니다. 

## More features

`nn`은 재귀 및 순환 호출을 엄격하게 금지합니다. 이로 인해 튜링 완전성이 제거되며[^1] (lean 4 증명)[^2],

범용 프로그래밍 언어에서 런타임을 통해 검사해야 했던 작업을 에디터 및 컴파일 타임에 간편하게 수행할 수 있습니다.


[^1]: [geohot blog#ok-so](https://geohot.github.io//blog/jekyll/update/2023/05/24/the-tiny-corp-raised-5M.html#ok-so) 글의 단락에서 왜 이 속성이 만족될 수 있고, 만족된다면 어떤 이점이 있는지 확인할 수 있습니다.
[^2]: 미완성