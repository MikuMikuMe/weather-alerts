# weather-alerts

Creating a complete Python program for a project like "weather-alerts" involves several components. The key elements are fetching weather data, processing user preferences, and sending alerts. Here's a basic skeleton of how such a program might look. For this demonstration, we'll use OpenWeatherMap API for weather data and SMTP for sending emails.

Before you start, ensure you have the following:
- An API key from OpenWeatherMap.
- SMTP server credentials for sending emails.

```python
import requests
import smtplib
from email.mime.text import MIMEText

# Constants
API_KEY = "your_openweathermap_api_key"
BASE_URL = "http://api.openweathermap.org/data/2.5/weather"
SMTP_SERVER = "smtp.example.com"
SMTP_PORT = 587
EMAIL_USER = "your_email@example.com"
EMAIL_PASSWORD = "your_email_password"

# Sample user data
users = [
    {
        "name": "Alice",
        "email": "alice@example.com",
        "location": "London",
        "temperature_threshold": 5.0,
    },
    {
        "name": "Bob",
        "email": "bob@example.com",
        "location": "New York",
        "temperature_threshold": 0.0,
    }
]

def get_weather(location):
    """Fetch weather data for a given location."""
    try:
        params = {
            'q': location,
            'appid': API_KEY,
            'units': 'metric'
        }
        response = requests.get(BASE_URL, params=params)
        response.raise_for_status()

        data = response.json()
        return {
            'temperature': data['main']['temp'],
            'conditions': data['weather'][0]['description'],
        }
    except requests.exceptions.RequestException as e:
        print(f"Error fetching weather data: {e}")
        return None

def send_email(subject, body, to_email):
    """Send an email using SMTP."""
    try:
        msg = MIMEText(body)
        msg['Subject'] = subject
        msg['From'] = EMAIL_USER
        msg['To'] = to_email

        with smtplib.SMTP(SMTP_SERVER, SMTP_PORT) as server:
            server.starttls()
            server.login(EMAIL_USER, EMAIL_PASSWORD)
            server.sendmail(EMAIL_USER, to_email, msg.as_string())

        print(f"Email sent to {to_email}")
    except Exception as e:
        print(f"Failed to send email: {e}")

def generate_alert(user, weather):
    """Generate weather alert based on user preferences and weather data."""
    if weather:
        if weather['temperature'] < user['temperature_threshold']:
            subject = f"Weather Alert for {user['location']}"
            body = (
                f"Hello {user['name']},\n\n"
                f"The current temperature in {user['location']} is {weather['temperature']}°C with {weather['conditions']}.\n"
                f"It's below your threshold of {user['temperature_threshold']}°C.\n"
                "Please take necessary precautions.\n\n"
                "Best regards,\nWeather Alert Service"
            )
            send_email(subject, body, user['email'])

def main():
    """Main function to execute the weather alert system."""
    for user in users:
        weather = get_weather(user['location'])
        generate_alert(user, weather)

if __name__ == "__main__":
    main()
```

### Important Points:
- **API Key Security**: Never hard-code your API keys, passwords, or any sensitive information. Use environment variables or configuration files.
- **SMTP Credentials**: Similarly, secure your SMTP credentials. Consider setting up an application-specific password.
- **Error Handling**: Proper error handling in API requests and email sending ensures that issues are logged and can be debugged.
- **User Preferences**: The user data (like temperature thresholds) is hard-coded here for demonstration, but in a real application, this would come from a database or user profiles.
- **Improvement Considerations**:
  - Implement logging instead of `print` statements.
  - Expand the program to support more weather conditions (e.g., precipitation alerts).
  - Include rate limiting to avoid excess API calls, especially in production.
  - Implement a user interface for managing preferences.