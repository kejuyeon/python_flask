# Study <img height="35" src="https://www.python.org/static/img/python-logo@2x.png"></img> + <img height="35" src="http://flask-docs-kr.readthedocs.io/ko/latest/_images/logo-full1.png"></img>
>>>>>>> 76666d8a434b536c2d88e8a352c9c53ec5a8d630

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
함께 만들어 봅시다.

login.py
```
@app.route('/')
@app.route('/', methods=['GET', 'POST'])
def index():
    if request.method == 'POST':
        print(request.form['user_id'])
        session['user_id'] = request.form['user_id']
    return render_template('main.html')

@app.route('/logout')
def logout():
    session.pop('user_id', None)
    return redirect(url_for('index'))
```


login.html
```
{% if not session.user_id %}
    <form action="{{ url_for( 'index' ) }}" method="POST">
        <input type="text" name="user_id" placeholder="id"/><br/>
        <input type="password" name="user_pass" placeholder="password"/><br/>
        <input type="submit" value="Login">
    </form>
{% else %}
    <p class="username">{{ session.user_id }}</p> 접속했습니다.
    <a href="{{ url_for('logout') }}">logout</a>
{% endif %}
```

## 글쓰기 예제 (디비 사용)

python 파일 수정
```
from flask import ..., _app_ctx_stack
from sqlite3 import dbapi2 as sqlite3
```

```
# 데이터 베이스 설정
DATABASE = '/tmp/flaskr.db'
DEBUG = True
SECRET_KEY = 'development key'
USERNAME = 'admin'
PASSWORD = 'default'

# 설정값 불러오기
app.config.from_object(__name__)
# app.config.from_envvar('FLASKR_SETTINGS', silent=True)
```

schema.sql 파일 생성
```
drop table if exists entries;
create table entries (
  id integer primary key autoincrement,
  title string not null,
  text string not null
);
```

DB 접속 및 sql 파일을 불러와 데이터 베이스 생성
```
def init_db():
    ''' create databace '''
    with app.app_context():
        top = _app_ctx_stack.top
        if not hasattr(top, 'sqlite_db'):
            sqlite_db = sqlite3.connect(app.config['DATABASE'])
            sqlite_db.row_factory = sqlite3.Row
            top.sqlite_db = sqlite_db
        db = top.sqlite_db
        with app.open_resource('schema.sql', mode='r') as f:
            db.cursor().executescript(f.read())
        db.commit()

def getDB():
    top = _app_ctx_stack.top
    if not hasattr(top, 'sqlite_db'):
        sqlite_db = sqlite3.connect(app.config['DATABASE'])
        sqlite_db.row_factory = sqlite3.Row
        top.sqlite_db = sqlite_db
    return top.sqlite_db

@app.route('/')
@app.route('/', methods=['GET', 'POST'])
def index():
    db = getDB()
    if request.method == 'POST':
        db.execute('insert into entries (title, text) values (?, ?)', [request.form['title'], request.form['content']])
        db.commit()
    cur = db.execute('select * from entries')
    noties = cur.fetchall()
    return render_template('main.html', noties=noties)
```

write.html
```
<h1>Write</h1>

<form method="POST">
    <input type="text" name="title" placeholder="제목을 입력해주세요"><br>
    <textarea name="content" placeholder="내용을 입력해주세요."></textarea><br>
    <input type="submit" value="Submit">
</form>

{% if noties %}
{% for notice in noties%}
    <span>{{notice.title}}</span> / <span>{{notice.text}}</span> <br>
{% endfor %}
{% endif %}
```











