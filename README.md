from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'
db = SQLAlchemy(app)

class Apartment(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    address = db.Column(db.String(200), nullable=False)
    rent = db.Column(db.Float, nullable=False)

    def __repr__(self):
        return f"Apartment('{self.name}', '{self.address}', {self.rent})"

@app.route('/')
def index():
    apartments = Apartment.query.all()
    return render_template('index.html', apartments=apartments)

@app.route('/add', methods=['GET', 'POST'])
def add_apartment():
    if request.method == 'POST':
        name = request.form['name']
        address = request.form['address']
        rent = request.form['rent']
        apartment = Apartment(name=name, address=address, rent=rent)
        db.session.add(apartment)
        db.session.commit()
        return redirect(url_for('index'))
    return render_template('add_apartment.html')

if __name__ == '__main__':
    app.run(debug=True)
