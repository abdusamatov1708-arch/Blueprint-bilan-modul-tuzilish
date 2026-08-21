# Blueprint-bilan-modul-tuzilish
Plaintext
my_flask_app/
│
├── app/
│   ├── __init__.py          # App Factory (create_app)
│   │
│   ├── main/                # 1-Blueprint papkasi
│   │   ├── __init__.py
│   │   └── routes.py
│   │
│   ├── notes/               # 2-Blueprint papkasi
│   │   ├── __init__.py
│   │   └── routes.py
│   │
│   └── templates/           # HTML shablonlar
│       ├── base.html
│       ├── index.html
│       ├── about.html
│       └── notes.html
│
├── run.py                   # Loyihani ishga tushiruvchi fayl
└── requirements.txt
🛠 Kodlar (Fayllar bo'yicha)
1. run.py (Loyihani ishga tushirish)
Python
from app import create_app

app = create_app()

if __name__ == "__main__":
    app.run(debug=True)
2. app/__init__.py (Application Factory)
Python
from flask import Flask

def create_app():
    app = Flask(__name__)
    app.config['SECRET_KEY'] = 'maxfiy-kalit-soz'

    # 1-Blueprint ni ro'yxatdan o'tkazish (main_bp)
    from app.main.routes import main_bp
    app.register_blueprint(main_bp)

    # 2-Blueprint ni ro'yxatdan o'tkazish (notes_bp, url_prefix bilan)
    from app.notes.routes import notes_bp
    app.register_blueprint(notes_bp, url_prefix='/notes')

    return app
3. app/main/routes.py va app/main/__init__.py
app/main/__init__.py:

Python
from flask import Blueprint

main_bp = Blueprint('main', __name__, template_folder='../templates')

from app.main import routes
app/main/routes.py:

Python
from flask import render_template
from app.main.routes import main_bp # Yoki bevosita import
from flask import render_template

# Yuqoridagi __init__ faylida blueprint allaqachon ochilgan, shuning uchun shunday yozamiz:
from app.main import main_bp

@main_bp.route('/')
def index():
    return render_template('index.html')

@main_bp.route('/about')
def about():
    return render_template('about.html')
4. app/notes/routes.py va app/notes/__init__.py
app/notes/__init__.py:

Python
from flask import Blueprint

notes_bp = Blueprint('notes', __name__, template_folder='../templates')

from app.notes import routes
app/notes/routes.py (Talab qilingan kamida 5 ta xotira-ichi nota Python list ko'rinishida):

Python
from flask import render_template
from app.notes import notes_bp

# Xotirada saqlanadigan kamida 5 ta nota (Python list)
xotira_notalari = [
    {"id": 1, "sarlavha": "Python o'rganish", "matn": "Flask va Blueprint mavzularini tugatish."},
    {"id": 2, "sarlavha": "Sport", "matn": "Har kuni ertalab yugurish va badantarbiya qilish."},
    {"id": 3, "sarlavha": "Kitob o'qish", "matn": "Clean Code kitobidan 2 bob o'qish."},
    {"id": 4, "sarlavha": "Loyiha", "matn": "Fast-food yetkazish web loyihasini yakunlash."},
    {"id": 5, "sarlavha": "Dam olish", "matn": "Hafta oxirida tabiat qo'yniga chiqish."}
]

@notes_bp.route('/')
def notes_list():
    # Notolar ro'yxatini shablonga uzatamiz
    return render_template('notes.html', notolar=xotira_notalari)
5. app/templates/ (HTML Shablonlar va url_for misoli)
app/templates/base.html (Barcha sahifalar uchun umumiy skelet va url_for):

HTML
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <title>Mening Ilovam</title>
</head>
<body>
    <nav>
        <!-- Blueprint orqali url_for ishlatish -->
        <a href="{{ url_for('main.index') }}">Bosh sahifa</a> | 
        <a href="{{ url_for('main.about') }}">Biz haqimizda</a> | 
        <a href="{{ url_for('notes.notes_list') }}">Notolar</a>
    </nav>
    <hr>
    
    <div class="content">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
app/templates/index.html:

HTML
{% extends "base.html" %}
{% block content %}
    <h1>Bosh sahifaga xush kelibsiz!</h1>
    <p>Bu Application Factory va Blueprint yordamida tuzilgan.</p>
{% endblock %}
app/templates/notes.html (Notolar ro'yxati):

HTML
{% extends "base.html" %}
{% block content %}
    <h1>Foydalanuvchi Notolari</h1>
    <ul>
        {% for nota in notolar %}
            <li>
                <strong>{{ nota.sarlavha }}</strong>: {{ nota.matn }}
            </li>
        {% endfor %}
    </ul>
{% endblock %}
