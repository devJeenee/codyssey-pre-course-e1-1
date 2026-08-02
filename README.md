## 1. 프로젝트 개요 및 실행 환경

### 프로젝트 개요

이 저장소는 Codyssey Pre-course E1-1 과정에서 수행한 개발 워크스테이션 구축 및 기초 도구 실습 내용을 기록한 저장소이다.

터미널 명령어, 파일 및 디렉터리 권한, Docker 이미지와 컨테이너, Dockerfile, 포트 매핑, 바인드 마운트, Docker 볼륨, Git 및 GitHub 연동을 직접 실습한다.

실습에서 사용한 핵심 명령어와 실행 결과를 README에 기록하고,
필요한 경우 터미널 및 브라우저 화면을 캡처하여 실행 증거로 첨부한다.
이 문서만 읽어도 전체 수행 과정과 검증 방법을 파악할 수 있도록 작성하는 것을 목표로 한다.

### 실행 환경

- OS: macOS 26.5.2
- Architecture: Apple Silicon (`arm64`)
- Shell: `zsh`
- Terminal: iTerm2
- Docker: 27.4.0
- Git: 2.40.1

## 2. 수행 체크리스트

- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습
- [x] Docker 설치/점검
- [x] hello-world 실행
- [x] Dockerfile 빌드/실행
- [x] 포트 매핑 접속(2회)
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성
- [x] Git 설정 + VS Code GitHub 연동

## 3. 터미널 조작 및 권한 실습

개인 사용자명과 홈 디렉터리 경로는 `<USER>`로 마스킹했다. 명령어 앞의 `$`는 터미널 프롬프트를 의미한다.

### 3.1 터미널 기본 조작

#### 1) 현재 위치 및 파일 목록 확인

`pwd`로 현재 위치를 확인하고, `ls`와 `ls -la`로 일반 목록과 숨김 항목을 확인했다.

```text
$ pwd
/Users/<USER>/codyssey/pre-course-e1-1

$ ls
README.md

$ ls -la
total 16
drwxr-xr-x   4 <USER>  staff   128 Aug  2 19:10 .
drwxr-xr-x   5 <USER>  staff   160 Jul 28 12:37 ..
drwxr-xr-x  14 <USER>  staff   448 Aug  2 19:11 .git
-rw-r--r--   1 <USER>  staff  5907 Aug  2 18:58 README.md
```

`ls`에는 일반 파일만 표시되었고, `ls -la`에는 현재 디렉터리(`.`), 상위 디렉터리(`..`), 숨김 디렉터리인 `.git`과 권한 정보가 함께 표시되었다.

#### 2) 디렉터리 이동 및 파일 생성

`mkdir -p`로 중간 경로를 포함한 실습 디렉터리를 생성하고 `cd`로 이동했다. `touch`로 빈 파일을 만든 뒤 목록에서 파일 크기가 0바이트인지 확인했다.

```text
$ mkdir -p practice/terminal

$ ls
README.md
practice

$ cd practice/terminal

$ pwd
/Users/<USER>/codyssey/pre-course-e1-1/practice/terminal

$ touch empty-file.txt

$ ls -la
total 0
drwxr-xr-x  3 <USER>  staff  96 Aug  2 19:11 .
drwxr-xr-x  3 <USER>  staff  96 Aug  2 19:11 ..
-rw-r--r--  1 <USER>  staff   0 Aug  2 19:11 empty-file.txt
```

`printf`의 출력 리다이렉션(`>`)으로 `note.txt`를 생성하고, `cat`으로 작성된 내용을 확인했다.

```text
$ printf 'Codyssey CLI practice\n' > note.txt

$ cat note.txt
Codyssey CLI practice

$ ls -la
total 8
drwxr-xr-x  4 <USER>  staff  128 Aug  2 19:11 .
drwxr-xr-x  3 <USER>  staff   96 Aug  2 19:11 ..
-rw-r--r--  1 <USER>  staff    0 Aug  2 19:11 empty-file.txt
-rw-r--r--  1 <USER>  staff   22 Aug  2 19:11 note.txt
```

#### 3) 파일 복사 및 이름 변경

`cp`로 `note.txt`를 복사한 뒤 목록에서 원본과 복사본이 모두 존재하는지 확인했다.

```text
$ cp note.txt note-copy.txt

$ ls -la
total 16
drwxr-xr-x  5 <USER>  staff  160 Aug  2 19:11 .
drwxr-xr-x  3 <USER>  staff   96 Aug  2 19:11 ..
-rw-r--r--  1 <USER>  staff    0 Aug  2 19:11 empty-file.txt
-rw-r--r--  1 <USER>  staff   22 Aug  2 19:11 note-copy.txt
-rw-r--r--  1 <USER>  staff   22 Aug  2 19:11 note.txt
```

`mv`로 복사본의 이름을 변경한 뒤 기존 이름이 사라지고 새 이름이 표시되는지 확인했다.

```text
$ mv note-copy.txt renamed-note.txt

$ ls -la
total 16
drwxr-xr-x  5 <USER>  staff  160 Aug  2 19:11 .
drwxr-xr-x  3 <USER>  staff   96 Aug  2 19:11 ..
-rw-r--r--  1 <USER>  staff    0 Aug  2 19:11 empty-file.txt
-rw-r--r--  1 <USER>  staff   22 Aug  2 19:11 note.txt
-rw-r--r--  1 <USER>  staff   22 Aug  2 19:11 renamed-note.txt
```

#### 4) 파일 및 디렉터리 삭제 확인

`rm`으로 이름을 변경했던 파일을 삭제하고, `ls -la`를 다시 실행하여 `renamed-note.txt`가 사라진 것을 확인했다.

```text
$ rm renamed-note.txt

$ ls -la
total 8
drwxr-xr-x  4 <USER>  staff  128 Aug  2 19:11 .
drwxr-xr-x  3 <USER>  staff   96 Aug  2 19:11 ..
-rw-r--r--  1 <USER>  staff    0 Aug  2 19:11 empty-file.txt
-rw-r--r--  1 <USER>  staff   22 Aug  2 19:11 note.txt
```

삭제 실습용 디렉터리와 파일을 만든 뒤, 파일을 먼저 삭제했다. `ls -la delete-me`로 디렉터리가 비어 있는지 확인하고 `rmdir`로 빈 디렉터리를 삭제했다.

```text
$ mkdir delete-me

$ touch delete-me/temp.txt

$ find delete-me -maxdepth 2 -print
delete-me
delete-me/temp.txt

$ rm delete-me/temp.txt

$ ls -la delete-me
total 0
drwxr-xr-x  2 <USER>  staff   64 Aug  2 19:11 .
drwxr-xr-x  5 <USER>  staff  160 Aug  2 19:11 ..

$ rmdir delete-me

$ ls -la
total 8
drwxr-xr-x  4 <USER>  staff  128 Aug  2 19:11 .
drwxr-xr-x  3 <USER>  staff   96 Aug  2 19:11 ..
-rw-r--r--  1 <USER>  staff    0 Aug  2 19:11 empty-file.txt
-rw-r--r--  1 <USER>  staff   22 Aug  2 19:11 note.txt
```

마지막으로 빈 파일을 삭제하고 `ls -la`를 다시 실행하여 최종적으로 `note.txt`만 남은 것을 확인했다.

```text
$ rm empty-file.txt

$ ls -la
total 8
drwxr-xr-x  3 <USER>  staff  96 Aug  2 19:11 .
drwxr-xr-x  3 <USER>  staff  96 Aug  2 19:11 ..
-rw-r--r--  1 <USER>  staff  22 Aug  2 19:11 note.txt

$ cd ../..

$ pwd
/Users/<USER>/codyssey/pre-course-e1-1

$ find practice -maxdepth 3 -print
practice
practice/terminal
practice/terminal/note.txt
```

#### 5) 절대 경로와 상대 경로

- 절대 경로는 파일 시스템의 루트(`/`)부터 파일 위치를 나타낸다. 예: `/Users/<USER>/codyssey/pre-course-e1-1/practice/terminal/note.txt`
- 상대 경로는 현재 작업 중인 디렉터리를 기준으로 파일 위치를 나타낸다. 저장소 루트 기준 예: `practice/terminal/note.txt`

### 3.2 파일 및 디렉터리 권한 변경

`ls -l`과 `ls -ld`로 변경 전·후 권한을 확인했다. 실습이 끝난 뒤 파일은 `644`, 디렉터리는 `755`로 복구했다.

#### 1) 파일 권한 변경: `600` → `644`

```text
$ ls -l practice/terminal/note.txt
-rw-r--r--  1 <USER>  staff  22 Aug  2 19:11 practice/terminal/note.txt

$ chmod 600 practice/terminal/note.txt

$ ls -l practice/terminal/note.txt
-rw-------  1 <USER>  staff  22 Aug  2 19:11 practice/terminal/note.txt

$ chmod 644 practice/terminal/note.txt

$ ls -l practice/terminal/note.txt
-rw-r--r--  1 <USER>  staff  22 Aug  2 19:11 practice/terminal/note.txt
```

#### 2) 디렉터리 권한 변경: `700` → `755`

```text
$ ls -ld practice/terminal
drwxr-xr-x  3 <USER>  staff  96 Aug  2 19:11 practice/terminal

$ chmod 700 practice/terminal

$ ls -ld practice/terminal
drwx------  3 <USER>  staff  96 Aug  2 19:11 practice/terminal

$ chmod 755 practice/terminal

$ ls -ld practice/terminal
drwxr-xr-x  3 <USER>  staff  96 Aug  2 19:11 practice/terminal
```

#### 3) 권한 의미

- `r`, `w`, `x`는 각각 읽기(4), 쓰기(2), 실행(1) 권한을 의미한다.
- 세 자리 숫자는 왼쪽부터 소유자, 그룹, 기타 사용자의 권한을 나타낸다.
- 파일 권한 `600`은 소유자에게만 읽기·쓰기 권한을 준다.
- 파일 권한 `644`는 소유자에게 읽기·쓰기, 그룹과 기타 사용자에게 읽기 권한을 준다.
- 디렉터리 권한 `700`은 소유자에게만 읽기·쓰기·진입 권한을 준다.
- 디렉터리 권한 `755`는 소유자에게 읽기·쓰기·진입 권한, 그룹과 기타 사용자에게 읽기·진입 권한을 준다.
- 파일의 `x`는 파일을 프로그램처럼 실행할 수 있는 권한이고, 디렉터리의 `x`는 해당 디렉터리에 진입하고 내부 항목에 접근할 수 있는 권한이다.

## 4. Docker 환경 점검과 컨테이너 운영

## 5. Dockerfile, 이미지 빌드 및 포트 매핑

## 6. 바인드 마운트와 볼륨 영속성

## 7. Git, GitHub 및 VS Code 연동

## 8. 트러블슈팅 및 최종 검증
