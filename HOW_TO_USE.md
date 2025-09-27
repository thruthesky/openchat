
# filesystem mpc 를 통한 llms.txt 활용법

- 왜?
  - 로컬 폴더에 있는 LLMs 정보를 조회(참조)하므로 속도가 빠름.


## 소스 코드 클론

git clone https://github.com/thruthesky/openchat oc

## Claude Code 설정


### FileSystem MCP 설치
예:
```
claude mcp add fsmcp -- npx -y @modelcontextprotocol/server-filesystem /Users/thruthesky/apps/openchat
```

- 앞에 `claude` 는 클로드 코드 프로그램
- `mcp` 는 모델 컨텍스트 프로토콜
- `add` 는 MCP 추가
- `fsmcp` 는 MCP 이름. 이 이름은 원하는데로 정할 수 있음.
- `--` 뒤에 있는 명령어(옵션)들을 정리해서, Claude 설정 파일에 저장을 한다.
- 주로 `npx -y` 옵션을 쓰는데, 그 뒤에 오는 패키지를 실행하라는 뜻이다.
- `@modelcontextprotocol/server-filesystem` 는 파일시스템 MCP 패키지 이름으로
  - 클로드가 `npx -y` 명령으로 이 패키지를 설치하고 실행한다.
- 맨 마지막에 `openchat` 폴더를 지정한다.


### 프롬프트 요청 방법:

예제:
```
fsmcp 를 사용해서, 현재 작업 폴더의 llms.txt를 열어 헤딩(# ## ### #### ##### ###### )
  섹션별로 핵심을 요약하고, 그 안의 docs/**/*.md 링크/경로를 목록화해.
```