import requests
import json

def emotion_detector(text_to_analyze):

    url = "https://sn-watson-emotion.labs.skills.network/v1/watson.runtime.nlp.v1/NlpService/EmotionPredict"

    myobj = {
        "raw_document": {
            "text": text_to_analyze
        }
    }

    header = {
        "grpc-metadata-mm-model-id": "emotion_aggregated-workflow_lang_en_stock"
    }

    # -----------------------------
    # 1. Handle blank input
    # -----------------------------
    if text_to_analyze is None or text_to_analyze.strip() == "":
        return {
            "anger": None,
            "disgust": None,
            "fear": None,
            "joy": None,
            "sadness": None,
            "dominant_emotion": None
        }

    response = requests.post(url, json=myobj, headers=header)

    # -----------------------------
    # 2. Handle HTTP 400 response
    # -----------------------------
    if response.status_code == 400:
        return {
            "anger": None,
            "disgust": None,
            "fear": None,
            "joy": None,
            "sadness": None,
            "dominant_emotion": None
        }

    # -----------------------------
    # 3. Safe JSON parsing
    # -----------------------------
    try:
        response_dict = response.json()
    except Exception:
        return {
            "anger": None,
            "disgust": None,
            "fear": None,
            "joy": None,
            "sadness": None,
            "dominant_emotion": None
        }

    # -----------------------------
    # 4. Extract emotions safely
    # -----------------------------
    try:
        emotions = response_dict["emotionPredictions"][0]["emotion"]
    except (KeyError, IndexError, TypeError):
        return {
            "anger": None,
            "disgust": None,
            "fear": None,
            "joy": None,
            "sadness": None,
            "dominant_emotion": None
        }

    # -----------------------------
    # 5. Compute dominant emotion
    # -----------------------------
    dominant_emotion = max(emotions, key=emotions.get)

    return {
        "anger": emotions.get("anger"),
        "disgust": emotions.get("disgust"),
        "fear": emotions.get("fear"),
        "joy": emotions.get("joy"),
        "sadness": emotions.get("sadness"),
        "dominant_emotion": dominant_emotion
    }
