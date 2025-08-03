from flask import Flask, render_template
import requests
import pyttsx3

app = Flask(__name__)

# বাড়ির লোকেশন (Raghunathganj)
HOME_LAT = 24.45825
HOME_LON = 88.04011
API_KEY = "45e422f62950c7cde401ddb9a75c7e4c"

engine = pyttsx3.init()
engine.setProperty('rate', 150)

def speak_banglish(text):
    print("🔊 Voice Alert:", text)
    engine.say(text)
    engine.runAndWait()

def get_weather():
    URL = f"https://api.openweathermap.org/data/2.5/weather?lat={HOME_LAT}&lon={HOME_LON}&appid={API_KEY}&units=metric"
    res = requests.get(URL)
    data = res.json()
    return data

@app.route('/')
def index():
    data = get_weather()
    description = data['weather'][0]['description']
    temp = data['main']['temp']
    wind_speed = data['wind']['speed']

    if 'rain' in description.lower():
        speak_banglish("বৃষ্টি শুরু হয়েছে, সাবধান হও!")

    return render_template('index.html', description=description, temp=temp, wind_speed=wind_speed)

if __name__ == '__main__':
    app.run(debug=True)
