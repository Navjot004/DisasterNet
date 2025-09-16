Of course. Here is a comprehensive document in markdown format that consolidates all the key information, code, and presentation materials for your DisasterNet project.

This file acts as a complete project guide.

Markdown

# DisasterNet: The Complete Project Guide 🚀

This document contains the full specification, source code, and presentation materials for the **DisasterNet** project, created for the Code-Elite Hackathon 2025.

## ## Table of Contents
1.  [Project Overview](#-project-overview)
2.  [Key Features](#-key-features)
3.  [Tech Stack & Architecture](#️-tech-stack--architecture)
4.  [Project Workflows](#-project-workflows)
5.  [Setup and Installation Guide](#️-setup-and-installation-guide)
6.  [Full Source Code](#-full-source-code)
    - [Backend: `main.py`](#backend-mainpy)
    - [Frontend: `prediction.tsx`](#frontend-srcpagespredictiontsx)
    - [Frontend: `resources.tsx`](#frontend-srcpagesresourcestsx)
    - [Frontend: `alerts.tsx`](#frontend-srcpagesalertstsx)
7.  [Hackathon Presentation Guide](#-hackathon-presentation-guide)
    - [The Pitch Script](#the-pitch-script)
    - [Presentation (PPT) Outline](#presentation-ppt-outline)
    - [Q&A Preparation Guide](#qa-preparation-guide)
8.  [Competitive Analysis](#-competitive-analysis)
9.  [Future Vision](#-future-vision)
10. [Team Members](#-team-members)

---

## ## 🌐 Project Overview

**DisasterNet** is a full-stack, AI-powered command center for emergency managers. It is designed to predict threats, manage on-the-ground resources, and send critical alerts in real-time.

> The core challenge in disaster management isn't a lack of data; it's a lack of real-time, actionable intelligence. DisasterNet transforms chaotic data into clear, life-saving decisions.

### ### 📸 Screenshots

<img width="1882" height="918" alt="image" src="https://github.com/user-attachments/assets/6a047209-5d1a-430c-be07-4bd4e848edd6" />
<img width="1889" height="912" alt="image" src="https://github.com/user-attachments/assets/323133dc-7b18-465c-a7c0-9faedb6495fd" />


---

## ## ✨ Key Features

* **🤖 AI Prediction Center:** A "What-If" scenario simulator that uses a Hugging Face AI model to predict disaster risks based on user-defined parameters.
* **🚚 Resource Management:** A centralized dashboard to track the status and location of critical emergency assets like NDRF units, medical teams, and shelters, localized for the Indian context.
* **📱 SMS Alerting System:** A manual alert center integrated with the Twilio API to send critical SMS warnings to personnel or the public.
* **🗺️ Geospatial View (Concept):** A toggleable map view to provide a common operational picture of all resource locations.
* **✅ Professional UI/UX:** Built with modern tools like ShadCN UI and Lucide Icons for a clean, intuitive, and responsive user experience.

---

## ## 🛠️ Tech Stack & Architecture

This project uses a modern, full-stack architecture with a clear separation between the frontend and backend.

| Category | Technologies |
| :--- | :--- |
| **Frontend** | ⚛️ React, 📘 TypeScript, ⚡ Vite, 💨 TailwindCSS, 🎨 ShadCN UI |
| **Backend** | 🐍 Python 3, 🚀 FastAPI |
| **AI/ML** | 🤗 Hugging Face Transformers |
| **Alerting** | 💬 Twilio |
| **Deployment** | ▲ Vercel (Frontend), 🚂 Render/Heroku (Backend) |

---

## ## 🔄 Project Workflows

### ### Workflow 1: AI Prediction Loop

This flow explains how a user can proactively assess risk using the AI model.

1.  **User (Disaster Manager)** adjusts sliders on the React Frontend.
2.  **Frontend** constructs a descriptive text sentence from the parameters.
3.  **Frontend** sends a JSON request to the Backend (`/predict`).
4.  **Backend (FastAPI)** receives the text and passes it to the **Hugging Face AI Model**.
5.  **AI Model** classifies the risk and returns a JSON result.
6.  **Backend** sends the prediction back to the Frontend.
7.  **Frontend UI** updates in real-time to display the new risk assessment.

### ### Workflow 2: Manual Alerting Loop

This flow explains how a user can reactively send a critical SMS alert.

1.  **User (Disaster Manager)** enters a phone number and message on the React Frontend.
2.  **Frontend** sends a JSON request to the Backend (`/send-sms`).
3.  **Backend (FastAPI)** receives the request and calls the **Twilio API**.
4.  **Twilio** sends the SMS to the recipient's phone.
5.  **Twilio** returns a delivery status to the Backend.
6.  **Backend** sends a confirmation message back to the Frontend.
7.  **Frontend UI** displays a "Message Sent" confirmation to the user.

---

## ## ⚙️ Setup and Installation Guide

### ### Prerequisites

* Node.js (v18 or higher)
* Python (v3.10 or higher)
* A free Twilio account.

## 🎤 Hackathon Presentation Guide
### The Pitch Script
"Good afternoon, judges.

Every minute in a disaster, a life hangs in the balance. The difference between a timely warning and a delayed one can be measured in lives and communities saved. But the core problem in disaster management today isn't a lack of data; it's a flood of chaotic, slow, and disconnected information. This is why we built DisasterNet.

DisasterNet is an AI-powered, real-time command center that transforms this chaos into clarity. It's designed for one thing: to help decision-makers predict threats, respond faster, and save lives.

Let me show you our 'What-If' Scenario Simulator... (Transition to live demo) ...As you can see, the AI has processed the scenario and classified it as a high-probability Flood Risk. This entire process—from raw data to actionable insight—took less than a second.

Our innovative approach combines a polished React UI with a robust Python backend, leveraging a zero-shot classification model from Hugging Face. DisasterNet was built to be innovative, feasible, and scalable, with a clear focus on user experience for decision-makers under pressure.

The future of disaster management is not just about reacting; it's about predicting. With DisasterNet, we can equip our heroes on the front lines with the intelligence they need to be proactive.

Thank you."

### Presentation (PPT) Outline
Slide 1: Title Slide (Project Name, Hackathon, Team)

Slide 2: The Problem (Delayed responses, lack of real-time awareness)

Slide 3: Our Solution: DisasterNet (Intro, Core Features: Predict, Manage, Alert, Chatbot)

Slide 4: Live Demo (Showcase the Prediction Simulator)

Slide 5: Technology & Architecture (React, FastAPI, Hugging Face, Twilio)

Slide 6: Future Roadmap & Business Model (CAP Integration, Live Data Feeds; SaaS for Gov't, Free for NGOs)

Slide 7: Team & Thank You (Q&A)

### Q&A Preparation Guide
Be ready to answer questions about:

Technical: Why zero-shot AI? Why FastAPI? How do you handle conflicting data?

Scalability: How would this scale to a national level? What are the limitations of the public AI model? How do you ensure accuracy?

Future/Business: What's your business model? What are the next 3 features you'd build? Who are your competitors?

## 📊 Competitive Analysis
Your key difference is the accessible, interactive "What-If" AI simulator.

Government Agencies (IMD, NDMA): They are official sources of data and public warnings. They broadcast information. You provide a tool to interact with information and simulate possibilities. You complement them, not compete with them.

Large Enterprise Platforms (Everbridge): These are heavy, expensive solutions for mass notification and logistics. Your project is a lightweight, focused tool for predictive simulation, making AI insights more accessible.

## 🌟 Future Vision
CAP Integration: Make the platform compliant with the Common Alerting Protocol (CAP) to integrate with official government broadcast systems like the NDMA.

Live Data Feeds: Ingest real-time data from sources like the IMD, NASA FIRMS (for wildfires), and USGS (for earthquakes).

Live Map View: Implement a fully interactive map using React-Leaflet to show the geospatial location of all resources and events.

WhatsApp Alerts: Expand the alerting system to include WhatsApp Business API for richer notifications.

## 👥 Team Members
Sneha 
Neeru
Mansi
Navjot Singh


