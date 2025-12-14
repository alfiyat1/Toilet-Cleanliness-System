from flask import Flask, request, redirect, render_template_string
import sqlite3
from datetime import datetime

app = Flask(__name__)

# ---------- Database ----------
def db_connect():
    return sqlite3.connect("database.db")

db = db_connect()
db.execute("""
CREATE TABLE IF NOT EXISTS feedback (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    rating INTEGER,
    comment TEXT,
    time TEXT
)
""")
db.commit()
db.close()

# ---------- Load HTML ----------
def load_html(file):
    with open(file, "r", encoding="utf-8") as f:
        return f.read()

# ---------- Routes ----------
@app.route("/")
def home():
    return render_template_string(load_html("index.html"))

@app.route("/feedback", methods=["GET", "POST"])
def feedback():
    if request.method == "POST":
        rating = request.form["rating"]
        comment = request.form["comment"]
        time = datetime.now().strftime("%Y-%m-%d %H:%M")

        db = db_connect()
        db.execute(
            "INSERT INTO feedback (rating, comment, time) VALUES (?, ?, ?)",
            (rating, comment, time)
        )
        db.commit()
        db.close()

        return redirect("/")

    return render_template_string(load_html("feedback.html"))

@app.route("/admin")
def admin():
    db = db_connect()
    data = db.execute("SELECT * FROM feedback ORDER BY id DESC").fetchall()
    db.close()

    html = load_html("admin.html")
    rows = ""
    for r in data:
        rows += f"<tr><td>{r[0]}</td><td>{r[1]}</td><td>{r[2]}</td><td>{r[3]}</td></tr>"

    return render_template_string(html.replace("{{ROWS}}", rows))

if __name__ == "__main__":
    app.run(debug=True)
