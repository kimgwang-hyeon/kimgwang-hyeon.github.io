# 김광현 포트폴리오 & 기술 블로그

문제를 선제적으로 해결하는 개발자 김광현의 포트폴리오 및 기술 블로그입니다.

## 프로젝트 구조

```
kimgwang-hyeon.github.io/
├── index.html              # 포트폴리오 메인 페이지
├── assets/                 # 정적 파일
│   ├── css/
│   │   ├── styles.css     # 포트폴리오 스타일
│   │   └── blog.css       # 블로그 스타일
│   └── images/            # 이미지 파일
├── blog/                   # 블로그 메인
│   └── index.html
├── _posts/                 # 블로그 포스트
├── _layouts/               # Jekyll 레이아웃
│   ├── default.html
│   └── post.html
├── projects/               # 프로젝트 상세 페이지
├── _config.yml             # Jekyll 설정
├── Gemfile                 # Ruby 의존성
└── README.md
```

## 로컬에서 실행하기

### 1. Ruby 및 Jekyll 설치

Windows에서는 [RubyInstaller](https://rubyinstaller.org/)를 사용하여 Ruby를 설치합니다.

### 2. 의존성 설치

```bash
bundle install
```

### 3. Jekyll 서버 실행

```bash
bundle exec jekyll serve
```

브라우저에서 `http://localhost:4000` 접속

## 블로그 포스트 작성하기

`_posts` 폴더에 다음 형식으로 파일을 생성합니다:

```
_posts/YYYY-MM-DD-title.md
```

파일 상단에 Front Matter 추가:

```yaml
---
layout: post
title: "포스트 제목"
date: YYYY-MM-DD
tags: [태그1, 태그2]
---
```

## 배포

GitHub Pages에 자동 배포됩니다.

## 연락처

- Email: kyn06114@gmail.com
- GitHub: [@kimgwang-hyeon](https://github.com/kimgwang-hyeon)
