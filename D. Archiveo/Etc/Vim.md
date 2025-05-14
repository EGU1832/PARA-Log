#### ShortKey

`<F1>` 도움말
`<F2>` 이전 탭으로 이동
`<F3>` 다음 탭으로 이동
`<F4>` 작업 중간에 저장
`<F5> `(Phython) 저장 후 실행(실행 시간 기제)
`<F6>` (C) gcc 컴파일 후 실행(a.out, 실행 시간 기제)
`<F7>` (Project) make 후 실행(a.out, 실행 시간 기제)
`<F8>` (C++) g++ 컴파일 후 실행(a.out, 실행 시간 기제)
`<F9>` NerdTree 열기
`<F12>` 시스템 클립보드에 복사
"find :FufFile `<CR>`

`tn` 새 탭 열기
`tq` 현재 탭 닫기

`zn` 모든 접힌 영역 닫기
`zm` 모든 접힌 영역 열기
`zc` 커서가 위치한 접힌 영역 닫기
`zo` 커서가 위치한 접힌 영역 열기

문서 1번째줄부터 10번째줄까지 변경시킬문자를 변결될문자로 변경
	`:1,10s/변경시킬문자/변경될문자/g`
	`:%s/변경시킬문자/변경될문자/g`

`gg=G` 파일 전체 자동 들여쓰기
커서로 줄 선택 후 `n>` n만큼 tab으로 들여쓰기 
커서로 줄 선택 후 `n<` n만큼 tab으로 내어쓰기

`ggVG`: 모든 행 선택
	`gg`: 파일의 시작으로 이동
	`V`: Visual 블록 모드로 진입
	`G`: 파일의 끝까지 블록을 선택

`u` 뒤로가기
`<c-r>` 앞으로 가기

`:sh / :shell` 컴파일 하기 위한 쉘 열기

snippet 수정
\\wsl.localhost\Ubuntu\home\egu1832\.vim\plugged\vim-snippets\UltiSnips

#### Shell

`tar -cvf [파일명.tar] [폴더명]` tar로 압축하기
`tar -xvf [파일명.tar]` tar 압축 풀기
`tar -zcvf [파일명.tar.gz] [폴더명]` tar.gz로 압축하기
`tar -zxvf [파일명.tar.gz]` tar.gz 압축 풀기

`ssh cse20221623@cspro9.sogang.ac.kr` cspro 접속

`scp [전송할 파일] [아이디@전송할 서버주소]:[저장될 서버의 디렉토리]` 파일 전송

#### Plugin NERDTree

`<F9>` NERDTree 켜기

`u` 현재 작업 디렉터리를 아래로 내리기
`r` 새로고침
`i` 해당 파일을 창 분할해서 열기
`t` 해당 파일을 탭 분할해서 열기
`m` 디렉토리 편집
	`a` 새로 만들기
	`m` 이동하기
	`d` 지우기
	`c` 복사하기
`cd + CD` 작업 디렉토리 옮기기

#### xterm-256color ANSI256
https://www.ditig.com/256-colors-cheat-sheet