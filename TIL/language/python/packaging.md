## [Wheel](https://pythonwheels.com/)

- Build Package
- 공식적으로 권장되는 빌드 방식
- 일반적인 source distribution보다 더 빨리 설치가 가능

### **Advantages of wheels**

1. **Faster installation for pure Python and native C extension packages.**

    순수 파이썬과 native C 확장 패키지 설치가 더 빠르다.

2. Avoids arbitrary code execution for installation. (Avoids setup.py)

    설치를 위한 임의의 코드 실행을 피할 수 있다.

3. Installation of a C extension does not require a compiler on Linux, Windows or macOS.

    C 확장자의 설치가 운영체제의 컴파일러가 필수가 아니다.

4. Allows better caching for testing and continuous integration.

    테스트와 지속적 통합을 위한 캐시가 더 쉽다

5. Creates .pyc files as part of installation to ensure they match the Python interpreter used.

    설치의 일부로써 .pyc 파일을 만들어 파이썬 인터프리터가 사용하는 것을 보장한다.

6. More consistent installs across platforms and machines.

    플랫폼과 기계 전반에 걸쳐 더욱 일관적인 설치.

## [Twine](https://twine.readthedocs.io/en/latest/)

### **Why Should I Use This?**

The goal of twine is to improve PyPI interaction by improving security and testability.

The biggest reason to use twine is that it securely authenticates you to [PyPI](https://pypi.org/) over HTTPS using a verified connection regardless of the underlying Python version, while whether or not python setup.py upload will work correctly and securely depends on your build system, your Python version and the underlying operating system.

Secondly, it allows you to precreate your distribution files. python setup.py upload only allows you to upload something that you’re building with distutils or setuptools, and created in the same command invocation. This means that you cannot test the exact file you’re going to upload to PyPI to ensure that it works before uploading it.

Finally, twine allows you to pre-sign your files and pass the .asc files into the command line invocation (twine upload myproject-1.0.1.tar.gz myproject-1.0.1.tar.gz.asc). This enables you to be assured that you’re typing your gpg passphrase into gpg itself and not anything else, since *you* will be the one directly executing gpg --detach-sign -a <filename>.

twine의 목표는 보안과 테스트 가능성을 향상하여 Pypi interaction을개선하는 것이다. 

1. 파이썬 버전, 운영체제와 관계 없이 보안상 안전하게 Pypi에 업로드할 수 있도록 해준다.
    - TLS를 이용 (HTTPS)
2. distribution 파일을 미리 만들어 둘 수 있다. 
    - `python [setup.py](http://setup.py) upload`의 경우에는 distribution 파일을 생성하는 것이 동시에 일어나기 때문에 업로드 전 이것이 제대로 동작 하는지 확인하기 위해 테스트 해볼 수 없다.
3. 파일을 미리 인증하고 `.asc` 파일을 커맨드 라인을 통해 전달 가능하다.

## setuptools vs distutils

[setup.py](http://setup.py/) 설정을 하도록 해주는 라이브러리는 크게 distutils와 setuptools 2가지가 있다. distutils는 파이썬의 기본 라이브러리에 포함이 되어있고 setuptools는 별도로 인스톨을 해야한다. 그래서 distutils를 사용하는게 default라고 생각할수 있지만 실제로는 setuptools를 사용하는게 default 이다. distutils의 단점들을 보완한게 setuptools라서 대부분 setuptools를 사용하고 있고 파이썬 공식 사이트에서도 setuptools를 권장하고 있다.

## 실전 👊

```bash
~/yool_module > python setup.py sdist bdist_wheel # dist와 build directory가 생김
~/yool_module > ls dist # 가끔 여러번 배포하다보면 다른 버전의 파일들이 섞여 들어가는 경우가 생기니 확인 후 업로드 하는 것을 권장
~/yool_module > twine upload --repository-url https://pypi.fury.io/spoqa -u $GEMFURY_TOKEN -p "" dist/* # Gemfury에 업로드
```

참고 문서

- [https://rampart81.github.io/post/python_package_publish/](https://rampart81.github.io/post/python_package_publish/)