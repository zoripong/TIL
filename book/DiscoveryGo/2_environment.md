# 2. 환경 설정하기
---

- `usr/local/go`에 설치 된 경우 GOROOT 설정은 생략이 가능하다.
- go 라이브러리 설치 필요시 git과 같은 VCS 가 필요하다.
    - [golang.org](http://golang.org) 아래에 있는 소스들이 git으로 접근 가능하게 구현되어 있기 때문에
    - [golang.org](http://golang.org) 외에 다른 곳에 있는 라이브러리를 받기 위해서는 다른 VCS가 필요하다.
- 작업 디렉터리 설정하기

```go
GOPATH (경로는 자유)
|- bin 바이너리, 실행파일들, $PATH에 설정되어 있어야 어디서나 바로 실행 가능
|- pkg 패키지 오브젝트(라이브러리), 컴파일 후 코드들이지만 실행가능한 것은 아님
|- src 소스코드, 이 아래에 패키지 경로를 작성하면 됨
```

- src/gihtub.com/zoripong 과 같은 디렉터리 구조로 만들면 오픈소스로 공개했을 때 다른 gopher들이 go get 명령을 이용하여 소스를 받아갈 수 있다.
- 패키지 이름을 지을 때
    - 디렉토리 구조가 디테일을 나타내므로 자세할 필요가 없다.
    - 함수 이름 역시 `패키지이름.함수` 로 접근하기 때문에 자세할 필요가 없다.
    - util 같은 명확하지 않은 이름이나, 자주 쓰는 기본 라이브러리와 동일한 이름도 피하는게 좋다.
- 도구 사용하기
    - godoc
        - go 프로그램의 문서를 볼 수 있는 도구

        ```go
        godoc -http=localhost:6060
        ```

    - oracle
        - 소스 코드에 대해 여러가지 물어볼 수 있는 도구
        - 커맨드는 어렵기 때문에 편집기와 연동하는 것 권장
    - Vet
        - 소스코드 검사 도구
        - 쉽게 실수 할 수 있는 부분을 지적해줌

        ```go
        go tool vet github.com/zoripong/simple-chat-service/message
        ```

    - Fix
        - deprecated 된 API 호출 등을 고쳐줌

        ```go
        go tool fix github.com/zoripong/simple-chat-service/message
        ```

    - Test
        - 테스트 수행 도구

        ```go
        go test github.com/zoripong/simple-chat-service/message
        ```