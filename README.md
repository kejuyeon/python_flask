# Python + Flask

# Flask

> Flask는 Werkzeug 와 Jinja2 라이브러리에 의존적이다. Werkzeug는 웹어플리케이션과 다양한 
> 서버 사이의 개발과 배포를 위한 표준 파이썬 인터페이스인 WSGI를 구현한 툴킷이다. Jinja2 는
> HTML 템플릿을 렌더링 하는 템플릿엔진이다.


# virtualenv

> 개발 과정에서 꼭 사용해야만 하는 툴
> virtualenv를 이용하여 Python을 아무런 문제 없이 각각의 프로젝트 환경에 맞게 다중설치가 가능

## Install

### install virtualenv

```
$ sudo easy_install virtualenv
```

or

```
$ sudo pip install virtualenv
```


##### 프로젝트 폴더 만들기

```
$ mkdir myproject
$ cd myproject
$ virtualenv venv
```

설치 후 실행

```
$ . venv/bin/activate
```


#### install Flask

```
$ pip install Flask
```

#### 시스템 전체에 적용하여 설치 (노추천함)

~~$ sudo pip install Flask~~




## 시작

```
from flask import Flask

app = Flask(__name__)

if __name__ == '__main__':
    app.run()
```

#### Run

```
$ python hello.py
```

http://127.0.0.1:5000/ 이동 후 정상 접속되면 끗

#### 디버그 모드

```
app.debug = True
app.run()
```

or

```
app.run(debug=True)
```


## route() 

route() 데코레이터는 함수와 URL을 연결

```
...
@app.route('/')
def index():
    return 'index page'

@app.route('/hello')
def hello():
    return 'hello world'
...
```

실행이 잘되면 끗

#### URL을 동적으로 구성, 함수에 여러 룰을 붙일 수 있다.

```
...
@app.route('/user/<username>')
def user(username):
    return 'User %s' % username

@app.route('/post/<int:post_id>')
def show_post(post_id):
    return 'Post id : %d' % post_id

@app.route('/project/')
def projects():
    retrun 'Project'
...
```


## render_template

hello.html 생성
```
...
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello World!</h1>
{% endif %}
...
```

hello.py 에 추가
```
from flask import Flask, render_template
app = Flask(__name__)

@app.route('/hello')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
...
```

> templates/hello.html 파일을 불러옴
> name 값을 불러올수 있음

## 데이터 전달 (POST, GET)

login.html 생성
```
{% if username %}
<p class="username">{{ username }}</p>
{% endif %}
<form >
    <span>username : </span> <input type='text' name='username'/>
    <input type='submit' value='Send'/>
</form>
```


```
...
from flask import Flask, render_template, request
@app.route('/login')
@app.route('/login', methodes=['GET', 'POST'])
def login():
    retrun render_template('login.html', username=request.form['username'])
...
```


* Session 저장

```
from flask import Flask, render_template, request, session
@app.route('/login')
@app.route('/login', methods=['GET', 'POST'])
def login()
    if request.method == 'POST':
        session['username'] = request.form['username']
    return render_template('login.html')

...
app.secret_key = 'session secret_key'

```
secret key 설정을 안해주면 error

login.html 수정
```
{% if session.usernsme %}
<p>{{ session.username }}</p>
{% endif %}
...
```

# 예제

## 로그인
## 글쓰기 디비 사용













