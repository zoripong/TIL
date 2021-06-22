# wsgi 

Web Server Gateway Interface 의 약자

웹 어플리케이션 (Flask)가 웹서버 (아파치, nginx 등)과 통신할 수 있도록 하는 middleware

> [While lightweight and easy to use, Flask’s built-in server is not suitable for production as it doesn’t scale well.](https://flask.palletsprojects.com/en/1.1.x/deploying/)

플라스크 빌트인 서버가 있어서 wsgi를 사용하지 않고 배포할 수 있지만, 확장이 잘 되지 않아 production 환경에는 알맞지 않다.

Gunicorn, uWSGI, Gevent, ASGI(Async) 등을 WSGI Container로 이용할 수 있다.


## gunicorn 

- Gunicorn은 다양한 web server와 상호 작용할 수 있도록 구축되었습니다.
- Gunicorn은 web server와 web application 사이에서 일어나는 모든 일을 처리
  - 한 번에 많은 request를 처리하고 부하를 분산
  - 실행중인 web application의 multi process 유지


## asgi
- asyncio, coroutine 과 같은 비동기 작업에 대해 처리할 수 있도록 지원
- uvicorn과 같은 라이브러리들이 있음
  - 내부적으로 javascript v8에서 사용하는 비동기 모듈 (libuv)을 사용하여 node.js만큼 빠른 속도를 낼 수 있음

