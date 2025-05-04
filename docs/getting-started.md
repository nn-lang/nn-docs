---
sidebar_position: 2
---

# Getting Started

## Prerequisites

### Code editor

nn 코드를 작성하기 위해서는 코드 에디터가 필요합니다. 

[Visual Studio Code](https://code.visualstudio.com/)를 추천드리며, 그 외의 선호하시는 에디터도 사용하실 수 있지만 `vscode`의 경우 [공식 extension](https://marketplace.visualstudio.com/items?itemName=nn-lang.nn)을 제공하고 있습니다.

### Command line

nn을 컴파일 및 테스트하기 위해서는 command-line interfaces (CLIs)가 필요합니다.

### Node.js and npm

nn을 설치 및 실행하기 위해서는 [NodeJS](https://nodejs.org/ko) 및 npm[^1]이 필요합니다.

일반적인 환경에서 nodejs와 npm은 같이 제공됩니다.

작동이 보장되는 최소 nodejs 버전은 `20 LTS`입니다. 가장 최신의 LTS 버전을 사용하시는 것을 권장드립니다.

# Install

```sh
npm install -g @nn-lang/nn-cli
```

```sh
nn-cli --help
```

[^1]: 혹은 그에 준하는 패키지 매니저