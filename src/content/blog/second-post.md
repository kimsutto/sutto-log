---
title: '개발 환경 세팅 가이드'
description: '생산성을 높이는 개발 환경 세팅 방법을 공유합니다. 에디터, 터미널, Git 설정 등 실용적인 팁을 담았습니다.'
pubDate: '2026-02-12'
heroImage: '../../assets/blog-placeholder-3.jpg'
---

새 맥북을 받거나 개발 환경을 초기화했을 때, 매번 같은 세팅을 반복하게 됩니다. 이 글에서는 제가 사용하는 개발 환경 세팅을 정리했습니다.

## 에디터 설정

### VS Code 필수 확장 프로그램

개발 효율을 높여주는 VS Code 확장 프로그램들입니다:

- **ESLint** - 코드 품질 검사
- **Prettier** - 코드 포매팅
- **GitLens** - Git 히스토리 시각화
- **Auto Rename Tag** - HTML 태그 자동 리네임

### settings.json 추천 설정

```json
{
  "editor.fontSize": 14,
  "editor.fontFamily": "JetBrains Mono",
  "editor.tabSize": 2,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

## 터미널 설정

### Oh My Zsh

macOS에서는 **Oh My Zsh**를 설치하면 터미널이 훨씬 편리해집니다:

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

추천 플러그인:
- `zsh-autosuggestions` - 명령어 자동 완성
- `zsh-syntax-highlighting` - 구문 하이라이팅

## Git 설정

### 기본 설정

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global init.defaultBranch main
```

### 유용한 Git Alias

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --all"
```

## Node.js 버전 관리

여러 프로젝트에서 다른 Node.js 버전을 사용할 때는 **nvm**을 추천합니다:

```bash
# nvm 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# 최신 LTS 설치
nvm install --lts
nvm use --lts
```

## 마무리

개발 환경은 개인의 취향이 많이 반영되는 영역입니다. 이 글을 참고삼아 자신만의 최적화된 환경을 만들어보세요! 🔧
