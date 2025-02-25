
1. Fetch Real-Time Data
Use a financial data API to get real-time stock prices. Some popular APIs include:

Alpha Vantage
IEX Cloud
Yahoo Finance API
2. Calculate Indicators
Use TA-Lib to calculate your indicators based on the latest data.

3. Generate Trading Signals
Create logic to generate buy, sell, or hold signals based on your indicators.

4. Send E-mail
Step-by-Step Guide
1. Set Up Email Credentials
You'll need an email account to send the emails. For this example, we'll use Gmail, but you can use any email provider that supports SMTP.

2. Install Required Libraries
Make sure you have the smtplib and email libraries installed. These are standard libraries in Python, so you don't need to install them separately.

3. Fetch Real-Time Data and Generate Signals
Use the same logic as before to fetch data and generate buy/sell signals.

4. Send Email
Use the `smtplib` library to send the email with your trading signals.

Example Code
Step 1: Fetch Real-Time Data and Generate Signals
```python
import yfinance as yf
from datetime import datetime, timedelta
import talib
import numpy as np

# Fetch historical data
ticker = 'AAPL'
end_date = datetime.now()
start_date = end_date - timedelta(days=365)

data = yf.download(ticker, start=start_date, end=end_date)
close_prices = data['Close'].values

# Calculate indicators
sma_short = talib.SMA(close_prices, timeperiod=50)
sma_long = talib.SMA(close_prices, timeperiod=200)
macd, macdsignal, macdhist = talib.MACD(close_prices, fastperiod=12, slowperiod=26, signalperiod=9)
rsi = talib.RSI(close_prices, timeperiod=14)

# Generate signals
def generate_signal(sma_short, sma_long, macd, macdsignal, rsi):
    if sma_short[-1] > sma_long[-1] and macd[-1] > macdsignal[-1] and rsi[-1] < 30:
        return 'Buy'
    elif sma_short[-1] < sma_long[-1] and macd[-1] < macdsignal[-1] and rsi[-1] > 70:
        return 'Sell'
    else:
        return 'Hold'

signal = generate_signal(sma_short, sma_long, macd, macdsignal, rsi)
```

Step 2: Send Email
```python
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText

# Email credentials
sender_email = 'your_email@gmail.com'
sender_password = 'your_email_password'
receiver_email = 'recipient_email@example.com'

# Create the email content
subject = f"Trading Signal for {ticker}"
body = f"Signal for today: {signal}"

msg = MIMEMultipart()
msg['From'] = sender_email
msg['To'] = receiver_email
msg['Subject'] = subject
msg.attach(MIMEText(body, 'plain'))

# Send the email
server = smtplib.SMTP('smtp.gmail.com', 587)
server.starttls()
server.login(sender_email, sender_password)
text = msg.as_string()
server.sendmail(sender_email, receiver_email, text)
server.quit()

print("Email sent successfully!")
```