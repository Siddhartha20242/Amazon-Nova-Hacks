         Introducing Mudra (Originally coming from sanskrit meaning 'gesture' or 'sign')


There are 1.4 billion people worldwide living with some kind of disabilities which can affect 
every single moment of communication and requires exhausiting effort or expesnvie equipment 
which costs about $8000 - $15000 and months of training. Mudra costs nothing and works on any
phone from day one.

          Some disabilities that can affect communication are:
1. ALS (Asymptrophic Lateral Sclerosis)
2. Cerebral Palsy
3. Stroke
4. Severe Autism
5. Locked in Syndrome


                        What our App does ?
- We point a phone camera at a non-verbal person. "Mudra" watches their face continously that
is eye muscles, eyebrow position, jaw, cheeks. 

It watches their hands - pointing, reaching, pushing away, tapping etc.
It watches their body - leaning forward, pulling back, shoulder tension etc.

                            Then what ?
Nova multimodal understands all of this simultaneously and interprets the most likely intent. 
Nova2 Sonic speaks it immediately. Our agent "Mudra" learns. Every person with a disability
has unique expression. "Mudra" builds a disability has unique expressions. Mudra builds a personal expression profile for each user and it is stored in AWS and it gets accurate more and 
more every day.




                               Workflow
                               1. The User Signals
When: A non-verbal person makes a gesture, facial expression, or change in body posture (e.g., a slow blink, a head tilt, or pointing).
What Happens: The person around him's device, using the WebRTC getUserMedia API, captures a live video feed, which is displayed in the CameraMonitor component.


2. Frame Capture and Transmission
When: Every 2 seconds. What Happens: The useCameraStream hook captures a frame from the video feed. The frame is converted to base64 JPEG and checked for sufficient lighting/face visibility.
The frame is sent via a POST request to the AWS Backend endpoint /analyze/frame (FastAPI on AWS Lambda) along with the patient's ID.


3. Nova Multimodal Analysis (In the Backend)
When: The backend receives a frame. What Happens: The Nova Multimodal Embeddings model (via Amazon Bedrock) analyzes the image. It generates a detailed description of all observable physical signals (face, eye, hand, body) and a corresponding embedding vector. If a personal profile exists in DynamoDB, the system compares the new embedding against stored calibration embeddings. The visual analysis description, and any matched "known signals," are passed to the reasoning layer. The raw embedding is saved to DynamoDB for future longitudinal learning.


4. Nova 2 Lite Reasoning & Intent Classification
When: The reasoning layer receives the visual analysis and personal context. What Happens:
The Nova 2 Lite model (via Amazon Bedrock) classifies the intent. It chooses from options like pain, hunger, thirst, tired, happy, discomfort, etc. It outputs a structured response: INTENT | CONFIDENCE | REASONING. Emergency Logic: If "pain" or "severe distress" is classified with a confidence above 70%, the Emergency Alert logic is triggered immediately. Confirmation Logic: If the same intent appears 3 times in a row within 10 seconds, its confidence is boosted, and the intent is confirmed.

5. Caregiver Output and Speech Generation
When: An intent is confirmed or the emergency logic is triggered. What Happens: The confirmed intent text is sent to the Nova 2 Sonic model (via Amazon Bedrock). Nova 2 Sonic generates audio bytes with a warm, natural voice, adjusting the tone based on the urgency level (e.g., urgent tone for pain). The audio is returned to the frontend.
The frontend's useVoiceOutput hook automatically plays the audio via the Web Audio API.

6. Frontend Updates When: The new intent and confidence score are received from the backend.
What Happens: The Zustand sessionStore updates. The IntentDisplay component shows the new interpreted intent text in a large, centered card. The ConfidenceBar component updates with a smooth, animated transition, shifting its color (low confidence = red, high confidence = green).
If the emergency logic was triggered, the entire screen flashes red, and the EmergencyAlert component displays the "POSSIBLE DISTRESS" message.



                            Full Tech Stack
                            
Frontend Framework      → React + TypeScript + Vite
Styling                 → Tailwind CSS + Framer Motion
State Management        → Zustand
Camera Access           → WebRTC getUserMedia API
Audio Output            → Web Audio API
Backend Framework       → Python FastAPI
Serverless Compute      → AWS Lambda + API Gateway
Core AI Model 1         → Amazon Nova Multimodal Embeddings (image/video scene understanding)
Core AI Model 2         → Amazon Nova 2 Lite (intent reasoning + personal learning)
Core AI Model 3         → Amazon Nova 2 Sonic (speech output — warm natural voice)
Model Access Layer      → Amazon Bedrock
Personal Profiles DB    → Amazon DynamoDB
Frame + Asset Storage   → Amazon S3
Frontend Hosting        → AWS Amplify
Logging + Monitoring    → Amazon CloudWatch
Auth                    → AWS Cognito
Agent Framework         → Strands Agents (as suggested by hackathon)
IaC Deployment          → AWS CDK (infrastructure as code)


Demo Link: 

Blog Post Link:








































