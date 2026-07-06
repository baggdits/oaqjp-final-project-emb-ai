from flask import Flask, request
from EmotionDetection import emotion_detector

app = Flask(__name__)

@app.route("/emotionDetector", methods=["GET"])
def emotionDetector():
    # Get text from query parameter
    text_to_analyze = request.args.get("textToAnalyze")

    # Call your emotion detection function
    result = emotion_detector(text_to_analyze)

    # Handle empty or invalid input
    if result is None or "dominant_emotion" not in result:
        return "Invalid text! Please try again."

    # Format response exactly as required
    response = (
        f"For the given statement, the system response is "
        f"'anger': {result['anger']}, "
        f"'disgust': {result['disgust']}, "
        f"'fear': {result['fear']}, "
        f"'joy': {result['joy']}, "
        f"and 'sadness': {result['sadness']}. "
        f"The dominant emotion is {result['dominant_emotion']}."
    )

    return response


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
