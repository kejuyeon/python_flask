# python_flask

# Flask

> Flask는 Werkzeug 와 Jinja2 라이브러리에 의존적이다. Werkzeug는 웹어플리케이션과 다양한 
> 서버 사이의 개발과 배포를 위한 표준 파이썬 인터페이스인 WSGI를 구현한 툴킷이다. Jinja2 는
> HTML 템플릿을 렌더링 하는 템플릿엔진이다.


# virtualenv

> 개발 과정에서 꼭 사용해야만 하는 툴
> virtualenv를 이용하여 Python을 아무런 문제 없이 각각의 프로젝트 환경에 맞게 다중설치가 가능

## 설치
`$ sudo easy_install virtualenv `

or

`$ sudo pip install virtualenv`

## 시작

```
from flask import Flask

app = Flask(__name__)

if __name__ == '__main__':
    app.run()
```

`$ python hello.py`

http://127.0.0.1:5000/ 이동 후 정상 출력되면 끗

#### 디버그 모드

```
app.debug = True
app.run()
```

or

`app.run(debug=True)`