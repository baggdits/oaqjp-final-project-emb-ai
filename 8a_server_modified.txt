"""Flask application for Emotion Detection API."""

from flask import Flask, request, render_template, jsonify
from EmotionDetection import emotion_detector

app = Flask(__name__)


@app.route("/")
def home():
    """Render home page."""
    return render_template("index.html")


@app.route("/emotionDetector", methods=["GET"])
def emotion_detector_route():
    """Handle emotion detection requests."""

    text_to_analyze = request.args.get("textToAnalyze")

    result = emotion_detector(text_to_analyze)

    if not result or result.get("dominant_emotion") is None:
        return jsonify({
            "message": "Invalid text! Please try again!"
        })

    return jsonify({
        "anger": result.get("anger"),
        "disgust": result.get("disgust"),
        "fear": result.get("fear"),
        "joy": result.get("joy"),
        "sadness": result.get("sadness"),
        "dominant_emotion": result.get("dominant_emotion")
    })


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
