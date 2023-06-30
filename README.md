# sotra
これは簡易的なAPIです
from flask import Flask, request
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.sqlite3'
db = SQLAlchemy(app)

class User(db.Model):
  id = db.Column(db.Integer, primary_key=True)
  username = db.Column(db.String(100), unique=True)
  email = db.Column(db.String(100), unique=True)
  password = db.Column(db.String(128))

@app.route('/signup', methods=['POST'])
def signup():
  username = request.form['username']
  email = request.form['email']
  password = request.form['password']

  # ユーザーが存在するかどうかをチェックする
  user = User.query.filter_by(username=username).first()
  if user is not None:
    return 'ユーザーが存在します。'

  # ユーザーを作成してデータベースに保存する
  user = User(username=username, email=email, password=password)
  db.session.add(user)
  db.session.commit()

  return 'ユーザー登録が完了しました。'

if __name__ == '__main__':
  app.run(debug=True)
