# Weather Forecast App

This Weather Forecast App is a simple Flask-based web application that allows users to obtain real-time weather information for a specified location. It uses the WeatherAPI.com service via RapidAPI to fetch weather data.

### How the App Works

1. **Home Page**: When you access the app, you are presented with a home page that provides a text input field and a "Get Weather" button.

2. **Entering Location**: To get weather information, enter the name of the location (e.g., a city or town) for which you want to check the weather. 

3. **Fetching Weather Data**: When you click the "Get Weather" button, the app sends a request to the WeatherAPI.com service, specifying the location you entered. The app includes the API key and host information from the RapidAPI platform to authenticate the request.

4. **Displaying Weather Data**: Once the weather data is retrieved, the app parses the relevant information from the response, including details about the location and current weather conditions. 

5. **Displaying Results**: The app then displays this information on the same page. You can see data such as the location's name, region, country, latitude, longitude, local time, temperature (in both Celsius and Fahrenheit), weather condition, wind speed, wind direction, pressure, humidity, and more.

6. **Error Handling**: If you enter an incorrect or unrecognized location, the app provides an error message asking you to enter a valid place name.




# Code Explaination
Certainly, here's an explanation of how the code in `app.py` works, line by line:

```python
import json
import requests
from fpdf import FPDF
from flask import Flask, request, render_template

from weather.constants import *
```

- The code begins by importing necessary modules and libraries. These include:
  - `json` for working with JSON data.
  - `requests` for making HTTP requests to the WeatherAPI.com service.
  - `FPDF` for generating PDF files (though it's not used in this code).
  - `Flask` for creating a web application.
  - `render_template` for rendering HTML templates.
  - Custom constants are imported from the `weather.constants` module.

```python
app = Flask(__name__)
```

- An instance of the Flask application is created.

```python
@app.route('/')
def home():
    return render_template('home.html')
```

- This code defines a route for the root URL `'/'`. When a user accesses the root URL, the `home` function is executed. It renders an HTML template named `'home.html'`.

```python
@app.route('/predict_weather', methods=['POST'])
def predict_weather():
    if request.method == 'POST':
        location = request.form.get('location')
```

- Another route is defined for `'/predict_weather'`. This route accepts POST requests. When a POST request is received, it checks if the method is indeed POST, and it retrieves the location entered by the user from the form.

```python
try:
    response = fetch_weather_data(location)
    weather_data = parse_weather_data(response)
    return render_template('home.html', **weather_data)
```

- Inside a try-except block, the code tries to fetch weather data for the entered location.
  - It calls the `fetch_weather_data` function, passing the location as an argument, which sends a request to WeatherAPI.com and retrieves the response.
  - It then calls the `parse_weather_data` function, passing the response, which extracts and organizes relevant weather information.
  - Finally, it renders the `'home.html'` template and passes the extracted `weather_data` to it. The `**weather_data` syntax unpacks the dictionary as keyword arguments.

```python
except Exception as e:
    return render_template('home.html', error='Please enter a correct Place name...')
```

- If any exceptions occur during the try block, the code catches them in the `except` block. It then renders the `'home.html'` template again, this time with an error message asking the user to enter a valid place name.

```python
def fetch_weather_data(location):
    url = API_URL
    headers = {
        "X-RapidAPI-Key": API_KEY,
        "X-RapidAPI-Host": API_HOST
    }
    querystring = {"q": location}
    response = requests.get(url, headers=headers, params=querystring)
    response.raise_for_status()
    return response.json()
```

- The `fetch_weather_data` function is defined. It takes a location as a parameter.
  - It sets up the URL for making an API request, including the API key and host.
  - It sends a GET request to the WeatherAPI.com service with the specified location.
  - If the request is successful (status code 200), it returns the JSON response.

```python
def parse_weather_data(data):
    location_data = data['location']
    current_data = data['current']

    return {
        'name': location_data['name'],
        'region': location_data['region'],
        'country': location_data['country'],
        # ...
        # Extracts and organizes relevant weather information from the JSON response.
    }
```

- The `parse_weather_data` function is defined. It takes a JSON response as an argument.
  - It extracts and organizes specific weather data from the JSON response.
  - The extracted data is returned as a dictionary.

```python
if __name__ == '__main__':
    app.run(debug=True)
```

- Finally, the script checks if it's the main module and runs the Flask application with debugging enabled if it is. This allows you to run the app locally with debugging features.