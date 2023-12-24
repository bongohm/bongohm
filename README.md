from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

# Replace with your Line Channel Secret and Access Token
LINE_CHANNEL_SECRET = "your_line_channel_secret"
LINE_CHANNEL_ACCESS_TOKEN = "your_line_channel_access_token"
CHATGPT_API_KEY = "your_chatgpt_api_key"
GOOGLE_SHEETS_API_KEY = "your_google_sheets_api_key"

@app.route("/line-webhook", methods=["POST"])
def line_webhook():
    # Validate Line request
    if not validate_line_request(request):
        return "Invalid request", 400

    # Process Line message
    user_input = extract_user_input(request.json)
    
    # Send user input to ChatGPT API
    chatgpt_response = send_to_chatgpt_api(user_input, CHATGPT_API_KEY)

    # Extract relevant information from ChatGPT response
    extracted_data = extract_data_from_response(chatgpt_response)

    # Save data to Google Sheets
    save_to_google_sheets(extracted_data, GOOGLE_SHEETS_API_KEY)

    # Send response back to Line
    send_response_to_line(request.json["events"][0]["replyToken"], extracted_data)

    return "OK", 200

def validate_line_request(request):
    # Implement Line request validation logic
    return True

def extract_user_input(line_request):
    # Implement logic to extract user input from Line request
    return line_request["events"][0]["message"]["text"]

def send_to_chatgpt_api(user_input, api_key):
    # Implement logic to send user input to ChatGPT API
    response = requests.post(
        "https://api.openai.com/v1/chat/completions",
        headers={"Authorization": f"Bearer {api_key}"},
        json={"messages": [{"role": "system", "content": "You are a helpful assistant."}, {"role": "user", "content": user_input}]},
    )
    return response.json()

def extract_data_from_response(chatgpt_response):
    # Implement logic to extract relevant data from ChatGPT response
    return {"extracted_data": chatgpt_response["choices"][0]["message"]["content"]}

def save_to_google_sheets(data, api_key):
    # Implement logic to save data to Google Sheets
    pass

def send_response_to_line(reply_token, extracted_data):
    # Implement logic to send a response back to Line
    pass

if __name__ == "__main__":
    app.run(port=5000)
