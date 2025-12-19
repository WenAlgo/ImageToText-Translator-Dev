# ImageToText Translator Backend

A Flask-based REST API backend for the ImageToText Translator application that provides OCR (Optical Character Recognition) and Neural Machine Translation capabilities. The system extracts text from handwritten and printed images using deep learning models and translates it into multiple languages.

## Features

-   Text Detection: Uses CRAFT (Character Region Awareness for Text detection) to detect text regions and crop image patches for OCR processing.
-   Text Extraction: Uses TrOCR and EasyOCR engines to extract text from detected regions. Selects the best OCR result based on text type.
-   Text Translation: Translates extracted text to the user-selected target language using Helsinki-NLP MarianMTModel.
-   User Authentication: Provides JWT-based signup and login with Bcrypt password hashing for enhanced security.
-   History Management: Automatically saves extracted and translated text for authenticated users.

## Tech Stack

-   Framework: Flask
-   Authentication: Flask-JWT-Extended, Flask-Bcrypt
-   Database: SQLite
-   Image Processing: OpenCV, Pillow
-   Text Detection: CRAFT-Text-Detector
-   OCR Engines: TrOCR + EasyOCR
-   Translation: MarianMT
-   Language Detection: langdetect

## Setup Instructions

### Virtual Environment Setup

```bash
# Create virtual environment with Python 3.10
python3.10 -m venv .venv

# Activate virtual environment
# For macOS/Linux:
source .venv/bin/activate

# For Windows:
.\.venv\Scripts\activate

# Upgrade pip
pip install --upgrade pip

# Install dependencies
pip install -r requirements.txt
```

### Database Initialization

```bash
# Initialize the SQLite database
python database.py
```

### Running the Application

```bash
# Start the Flask app
python app.py
```

### Troubleshooting

If you encounter the following error:

```
AttributeError: partially initialized module 'cv2' has no attribute 'gapi_wip_gst_GStreamerPipeline' (most likely due to a circular import)
```

This is likely due to conflicting OpenCV versions. Fix it by uninstalling all OpenCV versions and reinstalling a stable version:

```bash
pip uninstall -y opencv-python opencv-contrib-python opencv-python-headless
pip install opencv-contrib-python==4.7.0.72
```

## API Endpoints

Authentication

-   `POST /api/signup` - Register a new user with username, email, and password
-   `POST /api/login` - Login with username/email and password, returns JWT token
-   `POST /api/logout` - Logout (client-side token deletion required) [Protected]
-   `GET /api/status` - Check authentication status and get current user info [Optional Auth]

OCR Processing

-   `POST /api/extract` - Upload image and extract text using CRAFT + OCR engines [Optional Auth]

    -   Form Data Parameters:

        -   image (file, required): Image file to process
        -   input_language (string, optional): Language code or "auto" for automatic detection
        -   text_type (string, optional): "handwritten", "printed", or "auto"

-   Returns: Extracted text, detected language, text type, and selected OCR engine

Translation

-   `POST /api/translate` - Translate text to target language [Optional Auth]

    -   JSON Body:

        -   text (string, required): Text to translate
        -   input_language (string, optional): Source language or "auto"
        -   language (string, required): Target language code

-   Returns: Translated text and detected source language

History Management

-   `GET /api/extract_history` - Get all extraction records with image URLs [Protected]
-   `GET /api/translate_history` - Get all translation records [Protected]
-   `GET /api/image/<timestamp>` - Retrieve original uploaded image by timestamp [Protected]

Note: [Protected] endpoints require JWT token in Authorization header: Bearer `<token>`
