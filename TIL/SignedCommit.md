# commit signature verification

GPG 혹은 S/MIME 을 통해서 commit과 tag에 서명할 수 있다.

서명된 tag와 commit은 Gtihub에서 verified 마크가 표시되며, 사람들은 신뢰할만한 사람으로부터의 변경이라는 것을 알 수 있다.

# 사용하기

## GPG key가 있는지 확인

- [https://www.gnupg.org/download/](https://www.gnupg.org/download/) 설치
- 터미널 열기
- 아래 커맨드를 통해서 public/private 키가 모두 있는 GPG 키를 리스팅
    - private key는 signing 하는데 필수

```jsx
gpg --list-secret-keys --keyid-format LONG
```

## GPG Key 생성

```jsx
$ gpg --full-generate-key
```

- RSA and RSA
- 4096 bits
- 0
- 개인정보 및 passpahrase 입력

```jsx
$ gpg --list-secret-keys --keyid-format LONG
/Users/spoqa/.gnupg/pubring.kbx
-------------------------------
sec   rsa4096/1E11F9E843A7DCB2 2021-03-19 [SC]
      B44559BE3C542975C6D1E5071E11F9E843A7DCB2
uid                 [ultimate] zoripong <devuri404@gmail.com>
ssb   rsa4096/12BE2A9FE80887EE 2021-03-19 [E]

$ gpg --armor --export 1E11F9E843A7DCB2
... <-- github에 등록 할 내용
```

# ref

- [https://docs.github.com/en/github/authenticating-to-github/managing-commit-signature-verification](https://docs.github.com/en/github/authenticating-to-github/managing-commit-signature-verification)
