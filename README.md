# LIKES-BY-pbgx--uid-
from flask import Flask, jsonify, request
from flask_sqlalchemy import SQLAlchemy

# إعداد التطبيق وقاعدة البيانات
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///freefire.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

# تعريف جدول اللاعبين
class Player(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), unique=True, nullable=False)
    region = db.Column(db.String(50), nullable=False)
    level = db.Column(db.Integer, nullable=False)
    likes = db.Column(db.Integer, nullable=False)

    def __repr__(self):
        return f'<Player {self.name}>'

# إنشاء قاعدة البيانات والجداول
with app.app_context():
    db.create_all()

# Endpoint لعرض بيانات اللاعب باستخدام المعرف (username)
@app.route('/api/player/player/info', methods=['GET'])
def get_player_data():
    # الحصول على المعرف من الـ query string
    player_name = request.args.get('name')

    # طباعة المعرف للتأكد من الحصول عليه بشكل صحيح
    print(f"Looking for player with name: {player_name}")

    # البحث عن اللاعب باستخدام الاسم
    player = Player.query.filter_by(name=player_name).first()

    if player:
        return jsonify({
            "name": player.name,
            "region": player.region,
            "level": player.level,
            "likes": player.likes
        }), 200
    else:
        return jsonify({"message": "Player not found"}), 404

if __name__ == '__main__':
    app.run(debug=True)
