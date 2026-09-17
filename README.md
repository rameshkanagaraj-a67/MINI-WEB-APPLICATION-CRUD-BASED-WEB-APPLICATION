from flask import Flask, render_template, request, redirect
import sqlite3

app = Flask(__name__)

def connect_db():
    return sqlite3.connect('database.db')

@app.route('/')
def index():
    conn = connect_db()
    tools = conn.execute("SELECT * FROM tools").fetchall()
    conn.close()
    return render_template('index.html', tools=tools)

@app.route('/add', methods=['GET', 'POST'])
def add():
    if request.method == 'POST':
        name = request.form['name']
        category = request.form['category']
        quantity = request.form['quantity']

        conn = connect_db()
        conn.execute(
            "INSERT INTO tools (name, category, quantity) VALUES (?, ?, ?)",
            (name, category, quantity)
        )
        conn.commit()
        conn.close()
        return redirect('/')

    return render_template('add.html')

@app.route('/edit/<int:id>', methods=['GET', 'POST'])
def edit(id):
    conn = connect_db()

    if request.method == 'POST':
        name = request.form['name']
        category = request.form['category']
        quantity = request.form['quantity']

        conn.execute(
            "UPDATE tools SET name=?, category=?, quantity=? WHERE id=?",
            (name, category, quantity, id)
        )
        conn.commit()
        conn.close()
        return redirect('/')

    tool = conn.execute(
        "SELECT * FROM tools WHERE id=?", (id,)
    ).fetchone()

    conn.close()
    return render_template('edit.html', tool=tool)

@app.route('/delete/<int:id>')
def delete(id):
    conn = connect_db()
    conn.execute("DELETE FROM tools WHERE id=?", (id,))
    conn.commit()
    conn.close()
    return redirect('/')

if __name__ == '__main__':
    conn = connect_db()
    conn.execute('''
    CREATE TABLE IF NOT EXISTS tools (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT,
        category TEXT,
        quantity INTEGER
    )
    ''')
    conn.commit()
    conn.close()

    app.run(debug=True)# MINI-WEB-APPLICATION-CRUD-BASED-WEB-APPLICATION
Project Title: Student Tool Inventory Management System
