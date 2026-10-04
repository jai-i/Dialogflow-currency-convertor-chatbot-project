# 💱 Currency Converter Chatbot (Dialogflow + Flask Webhook)

A Dialogflow chatbot that converts an amount from one currency to another. Dialogflow handles the natural-language understanding. A small Flask app acts as the **fulfillment webhook**, fetching live exchange rates and returning the answer.

> Example: *"Convert 100 USD to INR"* → **"100 USD is 8300.5 INR"**

---

## 🚀 How It Works

1. The user types a query into the Dialogflow agent (e.g. "How much is 50 euros in dollars?").
2. Dialogflow extracts the parameters:
   - `unit-currency` (amount + source currency)
   - `currency-name` (target currency)
3. Dialogflow sends a `POST` request to this Flask webhook.
4. The webhook fetches the live conversion rate from the [CurrencyConverterAPI](https://www.currencyconverterapi.com/).
5. It calculates the result and returns it as `fulfillmentText`.

```
User ──▶ Dialogflow ──▶ Flask Webhook (/) ──▶ Currency API
                              │
User ◀── Dialogflow ◀─────────┘  (fulfillmentText)
```

---

## 🛠️ Tech Stack

- **Python 3**
- **Flask**: webhook server
- **Requests**: calls the currency API
- **Dialogflow**: NLP / intent handling
- **Heroku**: deployment (via `Procfile`)

---

## 📁 Project Structure

```
.
├── app.py             # Flask webhook (main application)
├── requirements.txt   # Python dependencies
├── Procfile           # Heroku process definition
├── setup.sh           # Heroku setup script
└── .gitignore
```

---

## ⚙️ Local Setup

### 1. Clone the repository
```bash
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>
```

### 2. Create a virtual environment (optional but recommended)
```bash
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Set your API key
Get a free key from [currencyconverterapi.com](https://free.currencyconverterapi.com/) and set it as an environment variable:

```bash
export CURRENCY_API_KEY="your_api_key_here"     # Windows: set CURRENCY_API_KEY=your_api_key_here
```

### 5. Run the server
```bash
python app.py
```
The app runs at `http://127.0.0.1:5000/`.

### 6. Expose it to Dialogflow (for testing)
Dialogflow needs a public HTTPS URL. Use [ngrok](https://ngrok.com/):
```bash
ngrok http 5000
```
Copy the generated `https://....ngrok.io` URL.

---

## 🤖 Dialogflow Configuration

1. Create an agent in the [Dialogflow Console](https://dialogflow.cloud.google.com/).
2. Create an intent (e.g. `currency.convert`) with training phrases such as:
   - *"Convert 100 dollars to rupees"*
   - *"How much is 50 EUR in GBP?"*
3. Make sure the intent has these parameters:
   | Parameter       | Entity            |
   |-----------------|-------------------|
   | `unit-currency` | `@sys.unit-currency` |
   | `currency-name` | `@sys.currency-name` |
4. Enable **Fulfillment → Webhook** and paste your webhook URL (ngrok or Heroku).
5. In the intent, turn on **"Enable webhook call for this intent"**.

---

## ☁️ Deploying to Heroku

```bash
heroku login
heroku create your-app-name
heroku config:set CURRENCY_API_KEY=your_api_key_here
git push heroku main
```

Then use `https://your-app-name.herokuapp.com/` as the webhook URL in Dialogflow.

> **Note:** Make sure `gunicorn` is in `requirements.txt` and your `Procfile` contains:
> `web: gunicorn app:app`

---

## 📬 Sample Webhook Request / Response

**Request (from Dialogflow)**
```json
{
  "queryResult": {
    "parameters": {
      "unit-currency": { "amount": 100, "currency": "USD" },
      "currency-name": "INR"
    }
  }
}
```

**Response**
```json
{
  "fulfillmentText": "100 USD is 8300.5 INR"
}
```

---

## 🔮 Future Improvements

- Error handling for invalid currencies or API failures
- Support for multiple target currencies in one query
- Deploy on Dialogflow-integrated platforms (Telegram, WhatsApp, Slack)

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first.

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

⭐ If you found this project helpful, give it a star!
