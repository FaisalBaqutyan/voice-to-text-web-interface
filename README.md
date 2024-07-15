# voice-to-text-web-interface


in this section, i will create a voice-to-text web interface that converts the voice into text and then send it to a database 

i will use Python and PHPMyAdmin 

first, i created a database in PHPMyAdmin called voice_to_text then I made a table called texts


## required python libraries

i used 3 python libraries which are 

'SpeechRecognition' for voice to text conversion

'pymysql' for MySQL database interaction

'Flask' for creating a web interface

to download them just oben the python terminal and paste this code 

```
pip install SpeechRecognition pymysql flask
```


## voice to text conversion 

next, i write this Python code to convert the voice into text 

```
import speech_recognition as sr

def convert_voice_to_text():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        print("Say something...")
        audio = recognizer.listen(source)
        
        text = recognizer.recognize_google(audio)
        print(f"Text: {text}")

        return text
```


## save the text to the database 

i write this code in Python that saves the text into the database

```
import pymysql 

def save_text_to_db(text):
    connection = pymysql.connect(
        host='localhost',           
        user='root',       
        password='',   
        database='voice_to_text' 
    )
    
    try:
        with connection.cursor() as cursor:
            sql = "INSERT INTO texts (text) VALUES (%s)"
            cursor.execute(sql, (text,))
        connection.commit()
    finally:
        connection.close()
```

## creating a web interface

then i created a simple web interface using Flask 

```
from flask import Flask, render_template, request, jsonify

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/convert', methods=['POST'])
def convert():
    text = convert_voice_to_text()
    if text:
        save_text_to_db(text)
        return jsonify({"text": text, "status": "success"})
    else:
        return jsonify({"status": "error", "message": "Voice not recognized"})

if __name__ == '__main__':
    app.run(debug=True)
```


## creating an HTML template 

finally, i created the HTML template in different file called index.html

```
!DOCTYPE html>
<html>
<head>
    <title>Voice to Text</title>
</head>
<body>
    <h1>Voice to Text Converter</h1>
    <button onclick="convertVoice()">Convert Voice</button>
    <p id="result"></p>
    <script>
        function convertVoice() {
            fetch('/convert', {
                method: 'POST'
            })
            .then(response => response.json())
            .then(data => {
                if(data.status === 'success') {
                    document.getElementById('result').innerText = data.text;
                } else {
                    document.getElementById('result').innerText = data.message;
                }
            })
            .catch(error => console.error('Error:', error));
        }
    </script>
</body>
</html>
```

and that's how the web interface looks like when i run the Python code and write http://localhost:5000 in the web browser 

![Screenshot 2024-07-15 122417](https://github.com/user-attachments/assets/7521f6b5-70b3-43eb-9a62-817e4f232876)
