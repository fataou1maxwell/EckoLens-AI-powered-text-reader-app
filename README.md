# EckoLens-AI-powered-text-reader-app
# See it. Hear it. Understand it

EckoLens: An AI-powered text reader app at the intersection of AI, computer vision, speech synthesis, and real-world usability

This AI-powered text reader app can:

- Detect printed or digital text from a camera or image.
- Automatically detect the language of the text.
- Translate it (if needed) into a chosen language.
- Read it aloud in natural-sounding speech.

The goal is to make information accessible to everyone — regardless of language, literacy level, or visual ability — through a simple, intuitive interface.

---

### Features

✅ **Real-Time OCR (Optical Character Recognition)**  
Detects text from images or camera streams using **Amazon Textract** or **Amazon Rekognition**.

✅ **Automatic Language Detection**  
Identifies the language of the extracted text via **Amazon Comprehend**.

✅ **Instant Translation**  
Translates the text to any target language using **Amazon Translate**.

✅ **Lifelike Voice Synthesis**  
Generates natural, human-like speech through **Amazon Polly**, supporting multiple voices and accents.

✅ **Multi-Platform Compatibility**  
Deployable on:
- Mobile devices (iOS, Android)  
- Web applications (React + AWS Amplify)  
- Edge devices (Raspberry Pi / Jetson Nano)

✅ **Scalable Serverless Backend**  
Built using **AWS Lambda** + **API Gateway** for cost efficiency and infinite scalability.

✅ **Optional Offline Mode**  
Future versions may use **Tesseract OCR** and local TTS models for offline environments.

---

## 1. Brand Rationale

EckoLens blends two powerful ideas:
- Ecko (Echo) → symbol of voice, reflection, and intelligent response.
- Lens → represents vision, focus, and perception.

Together, they express your core value:
“A lens that sees the world — and echoes it back through understanding.”

#### Brand Identity

| **Element** | **Recommendation** |
|--------------|--------------------|
| **Logo concept** | A minimalist circular lens with subtle soundwave rings emanating from it (a literal “echo lens”). |
| **Color palette** | Deep indigo or electric blue for tech + soft silver or white accents for clarity. |
| **Typography** | Sans-serif font (e.g., Inter, Poppins, or IBM Plex Sans). |
| **Iconography** | Use small wave or mic icons to visually suggest “speech.” |
| **Tagline** | “See text. Hear meaning.” (can evolve later to “See it. Hear it. Understand it.”) |

#### Visual Style Guide

To ensure design consistency across the project (GitHub visuals, web UI, and app interface), follow the color and typography guidelines below.

---

##### Primary Colors

| Role | Color | Hex Code | Notes |
|------|--------|----------|-------|
| **Primary (Tech Indigo)** | ![#283593](https://placehold.co/15x15/283593/283593.png) | `#283593` | Core identity color; represents intelligence and depth. |
| **Accent (Electric Blue)** | ![#2979FF](https://placehold.co/15x15/2979FF/2979FF.png) | `#2979FF` | Used for highlights, buttons, and brand accents. |
| **Light Accent (Silver White)** | ![#ECEFF1](https://placehold.co/15x15/ECEFF1/ECEFF1.png) | `#ECEFF1` | Used for contrast and clean layout backgrounds. |
| **Text Color (Charcoal)** | ![#212121](https://placehold.co/15x15/212121/212121.png) | `#212121` | For readable dark text on light backgrounds. |

---

##### Typography

| Use Case | Font Family | Weight | Example |
|-----------|--------------|---------|----------|
| **Headers / Titles** | `Poppins` | 600–700 | *EckoLens – See Text. Hear Meaning.* |
| **Body Text** | `Inter` | 400–500 | Simple, clean, and easy to read on both web and mobile. |
| **Monospace (Code Snippets)** | `IBM Plex Mono` | 400 | Ideal for developer documentation and terminal output. |

> ```html
> <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500&family=Poppins:wght@600;700&family=IBM+Plex+Mono&display=swap" rel="stylesheet">
>```
---

## 2. The Idea of the Project

### 2.1. System Architecture Overview

Here’s a high-level pipeline:
> ```
> Device Camera → Text Detection (OCR) → Language Detection → Translation → Speech Synthesis → Audio Output
> ```
And all of that will be built using AWS-managed AI services for faster, cost-effective development.

---

### 2.2. Step-by-Step Technical Plan (AWS-Based)

#### Step 1: Choosing the Device

You have two great options depending on your goals:

| **Option** | **Device Type** | **Pros** | **Ideal Use** |
|-------------|------------------|-----------|----------------|
| **Mobile App** | iPhone or Android | Easy to deploy, camera access, native TTS | End-user app, scalable |
| **Raspberry Pi 5 with Camera Module** | Small IoT device | Portable, hackable, great for demos/prototypes | Research or hardware prototype |
| **Jetson Nano / Orin** | Edge AI device | Can process offline | Offline/low-latency applications |

---

#### Step 2: Capture Text (OCR with AWS Rekognition or Textract)

Use:
- Amazon Textract → for extracting text from printed documents or camera images.
- Amazon Rekognition → for real-time text detection in images or video streams.

> Example AWS flow:
```
import boto3

client = boto3.client('textract')

response = client.detect_document_text(
    Document={'Bytes': image_bytes}
)

text_blocks = [block['Text'] for block in response['Blocks'] if block['BlockType'] == 'LINE']
```

Since we are building a mobile app, you can use AWS Amplify + Cognito for authentication and AWS Lambda for running the OCR logic.

---

#### Step 3: Detect the Language

Use Amazon Comprehend for automatic language detection:
```
import boto3

comprehend = boto3.client('comprehend')

response = comprehend.detect_dominant_language(Text=detected_text)
language_code = response['Languages'][0]['LanguageCode']
```
> Example result: `es` (Spanish), `fr` (French), `zh` (Chinese).
> 
---

#### Step 4: Translate the Text

We will use Amazon Translate:
```
translate = boto3.client('translate')

translated_text = translate.translate_text(
    Text=detected_text,
    SourceLanguageCode=language_code,
    TargetLanguageCode='en'
)['TranslatedText']
```
This lets users hear any foreign text in their preferred language.

---

#### Step 5: Convert Text to Speech

We will use Amazon Polly for lifelike voice output.
```
polly = boto3.client('polly')

audio = polly.synthesize_speech(
    Text=translated_text,
    OutputFormat='mp3',
    VoiceId='Joanna'
)
```
Users can choose voices by language (Joanna for English, Mathieu for French, etc.).
Play back the MP3 on the device using native audio players or a small web player.

---

#### Step 6: Integrate All Steps

We can combine everything into an AWS Lambda function:
```capture_image → Lambda (OCR + Comprehend + Translate + Polly) → return audio stream
```
We will use API Gateway to expose the Lambda as an API endpoint that your mobile or IoT device calls.

---

#### Step 7: Mobile App or Frontend

**If we are using AWS Amplify + React Native:**
- Use the device camera (React Native Camera).
- Capture image → upload to S3.
- Call API Gateway → get audio back.
- Play result with react-native-sound.

**If using a physical device (e.g., Raspberry Pi):**
- Use picamera2 to capture an image.
- Use Python scripts with Boto3 SDK.
- Output audio via Pi’s speaker.

---

## 3. AWS Architecture Diagram

The **EckoLens** system architecture connects visual input, language understanding, and speech synthesis in one seamless AI pipeline. It seamlessly combines **computer vision, natural language processing, translation, and speech synthesis** using the AWS AI ecosystem.

```
[Camera]
│
▼
[Image Upload to S3 Bucket for image storage]
│
▼
[Lambda function]
│
▼
[AWS Textract / Rekognition]
→ Extracts visible text
│
▼
[Amazon Comprehend]
→ Detects language
│
▼
[Amazon Translate]
→ Translates to target language
│
▼
[Amazon Polly]
→ Generates voice output (MP3)
│
▼
[Audio Playback on Device]
```

**Optional enhancements:**
- Store translation/audio history in DynamoDB.
- Cache frequent texts with S3 or Redis for speed.

All API calls and data flows are managed by **AWS Lambda** and exposed securely via **API Gateway**.  
User authentication (optional) is handled using **Amazon Cognito**.  
Static web and mobile hosting are powered by **AWS Amplify**.

---

## 4. AWS Services Used

| **Function**            | **Service**              | **Purpose** |
|--------------------------|--------------------------|--------------|
| Text Detection (OCR)     | Amazon Textract / Rekognition | Extract printed or scene text |
| Language Detection       | Amazon Comprehend         | Detect source text language |
| Translation              | Amazon Translate          | Translate text to target language |
| Text-to-Speech           | Amazon Polly              | Generate realistic audio |
| Serverless Compute       | AWS Lambda                | Run backend logic |
| API Management           | Amazon API Gateway        | Expose secure endpoints |
| Storage                  | Amazon S3                 | Store uploaded images & audio |
| Authentication (optional)| Amazon Cognito            | Manage user accounts |
| Frontend Hosting         | AWS Amplify               | Host and deploy frontend app |

### AWS Services Summary

| **Function** | **AWS Service** | **Description** |
|---------------|------------------|------------------|
| **Text Extraction** | Textract / Rekognition | Extract text from images |
| **Language Detection** | Comprehend | Detect source language |
| **Translation** | Translate | Convert to target language |
| **Text-to-Speech** | Polly | Generate realistic voice |
| **Storage** | S3 | Store images/audio |
| **Authentication** | Cognito | User auth and tokens |
| **Backend** | Lambda + API Gateway | Serverless processing |
| **Frontend** | Amplify / React Native | Cross-platform mobile app |

**Optional Enhancements:**

- Voice Selection: Allow users to choose male/female voices or accents.
- Offline Mode: Integrate open-source OCR (Tesseract) + local TTS models for use without internet.
- Auto-Read Mode: Continually read text as the camera moves (use Rekognition streaming).
- Accessibility Mode: For visually impaired users with voice-controlled UI.

---

## 5. Development Strategy

| **Stage** | **Goal** | **Tools** |
|------------|-----------|-----------|
| **MVP (Cloud-based)** | Proof-of-concept web app or mobile app | AWS Textract, Translate, Polly |
| **Prototype (Edge device)** | Offline Raspberry Pi device | Tesseract OCR (local) + Edge TTS |
| **Production (Mobile)** | Scalable app with multi-language UI | AWS Amplify, Cognito, API Gateway, Lambda |

---

## 6. Step-by-Step Roadmap (Recommended Order)

| **Week** | **Milestone** |
|-----------|----------------|
| **1** | Create AWS account, IAM setup, Textract & Polly sandbox tests |
| **2** | Build a Python prototype that takes an image → outputs MP3 |
| **3** | Add language detection + translation |
| **4** | Wrap the backend in AWS Lambda & API Gateway |
| **5** | Build simple web/mobile interface using AWS Amplify |
| **6** | Optimize performance and polish UX (auto playback, camera UI) |
| **7–8** | Optional: deploy to Raspberry Pi or mobile store |

---

## 5. Technical Stack

| **Layer** | **Technology** | **Description** |
|------------|----------------|----------------|
| Frontend | React / React Native / AWS Amplify | User interface for web or mobile |
| Backend | AWS Lambda (Python) | Core logic orchestrating AI workflows |
| AI Services | Textract, Comprehend, Translate, Polly | Managed AI models for OCR, NLP, TTS |
| Storage | S3 + DynamoDB (optional) | Persistent media and session data |
| Authentication | Cognito | User sign-up / sign-in (optional) |

---

## 6. Installation & Setup

### 6.1. Clone the Repository

> git clone https://github.com/<your-username>/clearness-vision.git
> cd clearness-vision
```
---

### 6.2. Set Up Python Environment

> ```
> python3 -m venv venv
> source venv/bin/activate
> pip install boto3 flask
> ```

### 6.3. Configure AWS Credentials

Ensure you have an AWS account and the AWS CLI installed. Then configure your credentials:
```aws configure
```

### 6.4. You will be prompted for your:
- AWS Access Key ID
- AWS Secret Access Key
- Region (e.g., us-east-1)

### 6.5. Deploy Lambda Function

You can deploy using AWS SAM, Serverless Framework, or directly from the AWS Console.
Example deployment with SAM:
> ```
> sam build
> sam deploy --guided
> ```

### 6.6. Test Locally

To test locally with a sample image:
```
python app.py --image sample.jpg --target_lang en
```

### Example Python Flow (MVP)

```
import boto3

# Initialize clients
textract = boto3.client('textract')
translate = boto3.client('translate')
polly = boto3.client('polly')
comprehend = boto3.client('comprehend')

# Step 1: OCR
with open('sample.jpg', 'rb') as f:
    img_bytes = f.read()
text_response = textract.detect_document_text(Document={'Bytes': img_bytes})
detected_text = " ".join([b['Text'] for b in text_response['Blocks'] if b['BlockType'] == 'LINE'])

# Step 2: Language Detection
lang = comprehend.detect_dominant_language(Text=detected_text)['Languages'][0]['LanguageCode']

# Step 3: Translation
translated = translate.translate_text(Text=detected_text, SourceLanguageCode=lang, TargetLanguageCode='en')['TranslatedText']

# Step 4: Text-to-Speech
speech = polly.synthesize_speech(Text=translated, OutputFormat='mp3', VoiceId='Joanna')
with open('output.mp3', 'wb') as f:
    f.write(speech['AudioStream'].read())

print("✅ Audio saved as output.mp3")
```

### 6.7. Frontend Integration

We can build a simple interface using AWS Amplify and React Native:
- Capture an image with the camera.
- Upload it to S3.
- Call the API Gateway endpoint.
- Stream back the MP3 for playback.
- Amplify automatically handles authentication, storage, and API integration.

### 6.8. Security and Best Practices
- Use IAM roles with least-privilege access for each AWS service.
- Store credentials securely using AWS Secrets Manager.
- Encrypt all S3 objects with SSE-S3 or KMS.
- Enable CloudWatch for logging and metrics.
- Use HTTPS for all API communications.

---

## 7. Development Roadmap
```
| **Milestone** | **Goal** | **Status** |
|----------------|-----------|-------------|
| **MVP Prototype (Cloud)** | Python CLI that reads image and outputs audio | ✅ Done |
| **Web Interface** | Browser-based version with file upload | 🟡 In progress |
| **Mobile App** | iOS/Android with camera input | 🔜 Planned |
| **Offline Mode** | On-device OCR + TTS with open models | 🔜 Planned |
| **Accessibility Features** | Voice controls and haptic feedback | 🔜 Planned |
```

---

## 8. Contributors

Project Lead: Fataou Maxwell O.

---

## 9. License

---

## 10. Acknowledgments

- AWS AI & ML Services
- Amazon Textract
- Amazon Polly
- Amazon Translate
- Amazon Comprehend
- AWS Amplify







