## 파이썬 프로젝트

파이썬과 DB를 이용하여 간단한 어플리케이션을 개발하는 과제를 부여받았다. 우선 프로젝트 실행 계획서를 작성해 보았다.

**프로젝트 보고서: 소개팅 매칭 웹 애플리케이션**

---

**1. 개요**

이 보고서는 소개팅 매칭을 위한 웹 애플리케이션에 대한 설명과 기술적인 세부사항을 제공한다. 이 프로젝트는 Flask 웹 프레임워크를 사용하여 개발되었으며, MySQL 데이터베이스를 통해 사용자 정보를 관리하고 소개팅 매칭을 수행한다.

---

**2. 목표**

이 프로젝트의 목표는 다음과 같다:

- 사용자가 프로필을 입력하고 저장할 수 있는 기능을 제공한다.
- 입력된 사용자 프로필을 기반으로 매칭된 사용자를 찾아준다.
- 매칭된 사용자와의 연락을 가능하게 한다.

---

**3. 기술적인 세부사항**

- **Flask 웹 프레임워크**: Flask는 파이썬으로 작성된 마이크로 웹 프레임워크로, 웹 애플리케이션을 빠르고 간편하게 구축할 수 있다.
- **SQLAlchemy**: SQLAlchemy는 파이썬에서 사용하는 SQL 툴킷이며, 데이터베이스와의 상호작용을 용이하게 만들어준다.
- **MySQL 데이터베이스**: MySQL은 오픈 소스 관계형 데이터베이스 관리 시스템(RDBMS)으로, 사용자 정보를 저장하고 관리하는 데 사용된다.

---

**4. 주요 기능**

- **프로필 입력**: 사용자는 웹 페이지에서 프로필을 입력할 수 있다. 이 프로필에는 닉네임, 성별, 나이, 목적, 직업, 외모, 성격, MBTI 유형, 취미, 매력, 이상형, 연애 스타일, 재산, 연락처 등의 정보가 포함된다.
- **매칭 알고리즘**: 사용자가 입력한 프로필 정보를 기반으로 매칭된 사용자를 찾아준다. 성별은 다르면서 취미, 외모, 성격, 이상형, 연애 스타일 등이 유사한 사용자를 매칭한다.
- **연락처 공개**: 매칭된 사용자와의 연락이 필요한 경우, 사용자가 연락처를 공개할 수 있다.

---

**5. 실행 및 테스트**

프로젝트를 실행하고 테스트하는 방법은 다음과 같다:

1. 소스코드를 다운로드하고 필요한 종속성을 설치한다.
2. MySQL 데이터베이스를 설정하고 연결 정보를 Flask 애플리케이션 설정에 추가한다.
3. Flask 애플리케이션을 실행하고 웹 브라우저에서 접속하여 프로필을 입력하고 매칭된 사용자를 확인한다.

---

**6. 결론**

이 보고서에서는 소개팅 매칭 웹 애플리케이션에 대한 개요와 기술적인 세부사항을 제공하였다. 이 프로젝트는 Flask와 MySQL을 사용하여 구축되었으며, 사용자가 입력한 프로필을 기반으로 매칭된 사용자를 찾아주는 기능을 제공한다. 사용자는 웹 페이지를 통해 프로필을 입력하고 매칭된 사용자와의 연락을 할 수 있다.

DB 는 MySQL을 사용하였고 현재 진행상황은 코드와 기능 구현 까지 마쳤으며, DB와 연동까진 아직 하지못하였다.

아래는 관련 코드이다.
```Python
from flask import Flask, request, render_template, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:0000@localhost/simulation'
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    gender = db.Column(db.String(10), nullable=False)
    age = db.Column(db.Integer, nullable=False)
    purpose = db.Column(db.String(120), nullable=False)
    occupation = db.Column(db.String(120), nullable=False)
    appearance = db.Column(db.String(120), nullable=False)
    personality = db.Column(db.String(120), nullable=False)
    mbti = db.Column(db.String(10), nullable=False)
    hobbies = db.Column(db.String(120), nullable=False)
    charm = db.Column(db.String(120), nullable=False)
    ideal_type = db.Column(db.String(120), nullable=False)
    dating_style = db.Column(db.String(120), nullable=False)
    asset = db.Column(db.Integer, nullable=False)
    contact = db.Column(db.String(20), nullable=False)

with app.app_context():
    db.create_all()

def find_matches(user):
    opposite_gender = '남자' if user.gender == '여자' else '여자'
    matches = User.query.filter_by(gender=opposite_gender) \
                        .filter_by(appearance=user.appearance, occupation=user.occupation,
                                    charm=user.charm, ideal_type=user.ideal_type, dating_style=user.dating_style) \
                        .all()
    return matches

@app.route('/')
def index():
    return '안녕하세요! 이 곳은 홈페이지입니다.'

@app.route('/favicon.ico')
def favicon():
    return redirect(url_for('static', filename='favicon.ico'))

@app.route('/profile', methods=['GET', 'POST'])
def profile():
    if request.method == 'POST':
        username = request.form['username']
        gender = request.form['gender']
        age = request.form['age']
        purpose = request.form['purpose']
        occupation = request.form['occupation']
        appearance = request.form['appearance']
        personality = request.form['personality']
        mbti = request.form['mbti']
        hobbies = request.form['hobbies']
        charm = request.form['charm']
        ideal_type = request.form['ideal_type']
        dating_style = request.form['dating_style']
        asset = request.form['asset']
        contact = request.form['contact']
        user = User(username=username, gender=gender, age=age, purpose=purpose, occupation=occupation,
                    appearance=appearance, personality=personality, mbti=mbti, hobbies=hobbies,
                    charm=charm, ideal_type=ideal_type, dating_style=dating_style, asset=asset, contact=contact)
        db.session.add(user)
        db.session.commit()
        matches = find_matches(user)
        return render_template('matches.html', matches=matches)
    return render_template('profile_form.html')

@app.route('/match/<int:match_id>', methods=['GET', 'POST'])
def match(match_id):
    match_user = User.query.get(match_id)
    if request.method == 'POST':
        if request.form.get('confirm'):
            return redirect(url_for('contact_shared', match_id=match_id))
    return render_template('match.html', match_user=match_user)

@app.route('/contact_shared/<int:match_id>')
def contact_shared(match_id):
    match_user = User.query.get(match_id)
    return render_template('contact_shared.html', match_user=match_user)

if __name__ == '__main__':
    app.run(debug=True)
```
