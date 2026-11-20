# SignCare AI

An Indian Sign Language (ISL) communication helper. It reads signs from a webcam, speaks them aloud, and turns spoken words into text.

This folder has two parts:

1. **`index.html`** – the project website. Open it by double-clicking. It has a working camera preview, speech-to-text, text-to-speech and a confirmed emergency alert.
2. **The Python app** (you build it in the next stages) – does the real sign recognition.

> **Important:** `index.html` does **not** recognise signs. It never shows fake predictions. Recognition needs a model that you train yourself (Stage 3 below).

---

## 1. What needs a trained model

| Feature | Needs a trained model? |
|---|---|
| Sign recognition | Yes |
| Uncertainty check (top guesses) | Yes |
| Personal signs | Yes (retraining) |
| Text-to-speech | No |
| Speech-to-text | No |
| Emergency alert | No (uses confirmed rules) |

## 2. Run the website

1. Put `index.html` in any folder.
2. Double-click it, or run a local server:
   ```
   python -m http.server 8000
   ```
   then open <http://localhost:8000>.
3. Use **Chrome or Edge** so speech recognition works. Allow camera and microphone.

## 3. Install the Python app tools (Windows 11)

Install Python 3.10 or 3.11 from python.org (tick "Add Python to PATH"). MediaPipe and TensorFlow may not work on the newest Python versions.

```
python -m venv venv
venv\Scripts\activate
pip install opencv-python mediapipe numpy scikit-learn tensorflow streamlit streamlit-webrtc SpeechRecognition pyttsx3 pyaudio
```

If `pyaudio` fails: `pip install pipwin` then `pipwin install pyaudio`.

## 4. Folder structure

```
signcare-ai/
├── index.html            # project website
├── README.md
├── requirements.txt
├── app.py                # Streamlit app (main screen)
├── data/
│   ├── raw/              # recorded landmark sequences, one folder per sign
│   └── personal/         # user-recorded signs
├── models/
│   └── sign_model.keras  # created by training
├── src/
│   ├── collect_data.py   # record signs with webcam + MediaPipe
│   ├── preprocess.py     # clean and split data
│   ├── train.py          # train the LSTM
│   ├── recognizer.py     # live prediction + confidence
│   ├── speech.py         # speech-to-text, text-to-speech
│   ├── emergency.py      # emergency phrases and alert
│   └── db.py             # SQLite
├── assets/signs/         # verified sign images or clips
└── signcare.db           # created automatically
```

## 5. Development roadmap

**Stage 1 – Camera and hand tracking.** Open the webcam with OpenCV. Draw the 21 MediaPipe hand points on screen.

**Stage 2 – Collect data.** Pick the starting words below. For each sign, record about 60–100 samples of 30 frames each. Save the 21 points (x, y, z) per frame as numbers. Use 2–3 people, different light and distances.

**Stage 3 – Train.** Split data 70/15/15 (train/validation/test). Train a small LSTM. Test on a person who was *not* in the training data. Save the model.

**Stage 4 – Build the app.** Add Streamlit screens: live translator, speech-to-text, personal signs, emergency, history. Connect them to `recognizer.py`, `speech.py`, `emergency.py` and SQLite.

**Stage 5 – Polish and demo.** Test, fix errors, prepare slides and viva answers.

## 6. Starting vocabulary (18 words, to verify)

Hello, Thank you, Yes, No, Please, Sorry, Help, Water, Food, Doctor, Medicine, Pain, Toilet, Police, Family, Friend, Good, Emergency.

Check **every** sign against the ISLRTC (Indian Sign Language Research and Training Centre) dictionary before recording. Some signs differ by region. Drop any word you cannot verify.

## 7. Datasets

- **ISLRTC dictionary and videos** – the official reference for checking signs.
- **INCLUDE (AI4Bharat)** – a public ISL word-level video dataset. Check its licence and word list before use.
- **Your own recordings** – the main data for a small vocabulary.

Always confirm a dataset's licence and that its labels match ISLRTC.

## 8. How recognition works

1. OpenCV reads a frame.
2. MediaPipe gives 21 hand points.
3. 30 frames are stacked into one sequence.
4. The LSTM returns a score for each sign.
5. If the top score is below a threshold (for example 0.7), the app shows the top 3 guesses and asks the user to confirm.
6. A confirmed answer is spoken and logged. Corrections are saved as new training examples.

## 9. Testing

- Test each module alone first (camera, speech, database).
- Report accuracy per sign, not just one overall number.
- Try different lighting, background and distance.
- Test with someone who did not record training data.
- Test the emergency flow: it must always ask for confirmation.

## 10. Common errors

| Error | Fix |
|---|---|
| Camera will not open | Close other apps using it; try camera index `1` |
| `pyaudio` install fails | Use `pipwin` as shown above |
| TensorFlow import error | Use Python 3.10 or 3.11 |
| Low accuracy | Record more samples, add variety, reduce the number of signs |
| Microphone not heard in browser | Use Chrome or Edge and allow access |

## 11. Demo scenario

A deaf patient visits a clinic. They sign **Pain**, then **Doctor**. The app shows the words and speaks them. The receptionist answers by voice; the app shows the text. One sign is unclear, so the app asks the patient to confirm. Finally the patient signs **Emergency**, confirms, and an alert goes to a family contact.

## 12. Limitations and future work

- Small vocabulary and single signs, not full sentences.
- Sensitive to light and camera angle.
- Trained on few people, so it may not suit everyone.
- Not a replacement for a human interpreter.
- Future: more signs, two-hand and facial cues, sentence-level recognition, mobile app.

## 13. Originality

Sign-language recognition projects already exist. Do not claim this is the first. Say what you built and what is yours: the confirmation step, personal sign learning and the emergency flow.

## 14. Viva questions to prepare

- Why MediaPipe landmarks instead of raw images?
- Why LSTM for gestures?
- What is a confidence score and how did you pick the threshold?
- How did you collect and split your data?
- How do you avoid testing on training data?
- What happens when the model is wrong?
- What are the privacy risks of a camera app?
