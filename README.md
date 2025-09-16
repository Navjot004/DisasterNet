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

*(Replace these with actual screenshots of your application)*

| Prediction Page | Resource Management | Alerting Center |
| :--- | :--- | :--- |
| ![Prediction Page](path/to/prediction_ss.png) | ![Resource Page](path/to/resources_ss.png) | ![Alerts Page](path/to/alerts_ss.png) |

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

### ### 1. Backend Setup

```bash
# Navigate to the backend directory
cd backend

# Create and activate a virtual environment
python -m venv venv
.\venv\Scripts\activate

# Install required packages from requirements.txt
pip install -r requirements.txt
pip install tf-keras
### 2. Frontend Setup
Open a new terminal for this.

Bash

# Navigate to the frontend directory
cd frontend

# Install dependencies
npm install
### 3. Environment Variables
In the backend directory, create a file named .env and add your Twilio API keys:

Code snippet

# File: backend/.env
TWILIO_ACCOUNT_SID="ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
TWILIO_AUTH_TOKEN="your_auth_token_here"
TWILIO_PHONE_NUMBER="+15017122661"
### 4. Running the Application
Terminal 1: Start the Backend Server (from the backend directory)

Bash

# Make sure your virtual environment is active
.\venv\Scripts\activate

# Run the FastAPI server
uvicorn main:app --reload
Backend will be running at http://127.0.0.1:8000.

Terminal 2: Start the Frontend Server (from the frontend directory)

Bash

# Run the React development server
npm run dev
Frontend will be running at http://localhost:8080.

## 💾 Full Source Code
### Backend: main.py
Python

# File: backend/main.py

# --- Existing Imports ---
from fastapi import FastAPI
from pydantic import BaseModel
from transformers import pipeline
from fastapi.middleware.cors import CORSMiddleware
import torch

# --- New Imports for Twilio & Environment Variables ---
import os
from twilio.rest import Client
from twilio.base.exceptions import TwilioRestException
from dotenv import load_dotenv

# Load environment variables from a .env file
load_dotenv()

# 1. Initialize FastAPI app
app = FastAPI(
    title="DisasterNet Prediction & Alert API",
    description="API for classifying disaster text and sending SMS alerts.",
    version="1.1.0"
)

# --- CORS Middleware ---
origins = [
    "http://localhost:8080",
    "http://localhost:3000",
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# 2. Define Request/Response Models
class PredictionRequest(BaseModel):
    text: str

class PredictionResponse(BaseModel):
    sequence: str
    labels: list[str]
    scores: list[float]

# New model for the SMS request body
class SmsRequest(BaseModel):
    phone_number: str
    message: str

# 3. Load Hugging Face Model
try:
    print("⏳ Loading Hugging Face zero-shot classification model...")
    classifier = pipeline(
        "zero-shot-classification",
        model="facebook/bart-large-mnli",
        device=0 if torch.cuda.is_available() else -1
    )
    print("✅ Model loaded successfully!")
except Exception as e:
    print(f"❌ Error loading Hugging Face model: {e}")
    classifier = None

# --- Twilio SMS Section ---

# Load Twilio credentials securely from environment variables
TWILIO_ACCOUNT_SID = os.getenv("TWILIO_ACCOUNT_SID")
TWILIO_AUTH_TOKEN = os.getenv("TWILIO_AUTH_TOKEN")
TWILIO_PHONE_NUMBER = os.getenv("TWILIO_PHONE_NUMBER")

# Initialize Twilio Client (only if credentials exist)
twilio_client = None
if TWILIO_ACCOUNT_SID and TWILIO_AUTH_TOKEN and TWILIO_PHONE_NUMBER:
    try:
        twilio_client = Client(TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN)
        print("✅ Twilio client initialized successfully!")
    except Exception as e:
        print(f"❌ Error initializing Twilio client: {e}")
else:
    print("⚠️ Twilio credentials not found in .env file. SMS functionality will be disabled.")


# 4. API Endpoints
@app.get("/")
def read_root():
    return {"status": "DisasterNet API is running."}

@app.post("/predict", response_model=PredictionResponse)
async def predict(request: PredictionRequest):
    if not classifier:
        return {"error": "AI model is not available."}
    
    candidate_labels = ["earthquake", "wildfire", "flood", "hurricane", "storm", "no disaster"]
    prediction_result = classifier(request.text, candidate_labels, multi_label=False)
    return prediction_result

# New endpoint for sending SMS
@app.post("/send-sms")
async def send_sms(request: SmsRequest):
    if not twilio_client:
        return {"success": False, "message": "Twilio is not configured on the server."}
    
    try:
        message = twilio_client.messages.create(
            body=request.message,
            from_=TWILIO_PHONE_NUMBER,
            to=request.phone_number
        )
        print(f"Message sent with SID: {message.sid}")
        return {"success": True, "message": f"Alert successfully sent to {request.phone_number}."}
    except TwilioRestException as e:
        print(f"Twilio Error: {e}")
        return {"success": False, "message": f"Failed to send message: {e.reason}"}
    except Exception as e:
        print(f"An unexpected server error occurred: {e}")
        return {"success": False, "message": "An unexpected server error occurred."}
### Frontend: src/pages/prediction.tsx
TypeScript

// File: src/pages/prediction.tsx

import { useState } from "react";
import { Card } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import { Slider } from "@/components/ui/slider";
import { 
  Brain, 
  Settings,
  BarChart3,
  Zap,
  Target,
  Loader2
} from "lucide-react";

type PredictionResult = {
  sequence: string;
  labels: string[];
  scores: number[];
};

type PredictionDisplay = {
  type: string;
  location: string;
  probability: number;
  severity: "low" | "medium" | "high" | "critical";
  timeframe: string;
  confidence: number;
  factors: string[];
};

const Prediction = () => {
  const [rainfall, setRainfall] = useState([45]);
  const [temperature, setTemperature] = useState([28]);
  const [seismicActivity, setSeismicActivity] = useState([3.2]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const [prediction, setPrediction] = useState<PredictionDisplay | null>({
    type: "Flood Risk",
    location: "Mumbai, India",
    probability: 0,
    severity: "low",
    timeframe: "Awaiting Simulation",
    confidence: 0,
    factors: ["Heavy rainfall", "High tide"]
  });

  const getSeverity = (label: string, score: number): PredictionDisplay['severity'] => {
    if (label === "no disaster") return "low";
    if (score > 0.8) return "critical";
    if (score > 0.6) return "high";
    if (score > 0.4) return "medium";
    return "low";
  };

  const handleSimulation = async () => {
    setIsLoading(true);
    setError(null);

    const scenarioText = 
      `Simulate a disaster scenario with the following conditions: ` +
      `rainfall at ${rainfall[0]} mm/hour, ` +
      `temperature at ${temperature[0]} degrees Celsius, ` +
      `and seismic activity at a magnitude of ${seismicActivity[0]}.`;

    try {
      const response = await fetch('[http://127.0.0.1:8000/predict](http://127.0.0.1:8000/predict)', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ text: scenarioText }),
      });

      if (!response.ok) {
        throw new Error('Failed to connect to the prediction API. Is the backend server running?');
      }

      const result: PredictionResult = await response.json();
      
      const topLabel = result.labels[0];
      const topScore = result.scores[0];

      setPrediction({
        type: `${topLabel.charAt(0).toUpperCase() + topLabel.slice(1)} Risk`,
        location: "Simulated Scenario",
        probability: Math.round(topScore * 100),
        severity: getSeverity(topLabel, topScore),
        timeframe: "Next 6-12 hours",
        confidence: Math.round(Math.random() * (95 - 85) + 85),
        factors: [`Rainfall: ${rainfall[0]}mm`, `Temp: ${temperature[0]}°C`, `Seismic: ${seismicActivity[0]} mag`]
      });

    } catch (err) {
      if (err instanceof Error) setError(err.message);
      else setError("An unknown error occurred.");
    } finally {
      setIsLoading(false);
    }
  };

  const getSeverityColor = (severity: string) => {
    switch (severity) {
      case "critical": return "border-red-500/50 bg-red-500/10 text-red-500";
      case "high": return "border-orange-500/50 bg-orange-500/10 text-orange-500";
      case "medium": return "border-yellow-500/50 bg-yellow-500/10 text-yellow-500";
      default: return "border-green-500/50 bg-green-500/10 text-green-500";
    }
  };

  const getProbabilityColor = (probability: number) => {
    if (probability >= 80) return "text-red-500";
    if (probability >= 60) return "text-orange-500";
    if (probability >= 40) return "text-yellow-500";
    return "text-green-500";
  };
  
  const getProbabilityBgColor = (probability: number) => {
    if (probability >= 80) return "bg-red-500";
    if (probability >= 60) return "bg-orange-500";
    if (probability >= 40) return "bg-yellow-500";
    return "bg-green-500";
  };

  return (
    <div className="min-h-screen bg-background text-foreground p-6 space-y-6">
      <div className="flex items-center justify-between">
        <div>
          <h1 className="text-3xl font-bold">AI Disaster Prediction Center</h1>
          <p className="text-muted-foreground">ML-powered forecasting and risk assessment</p>
        </div>
        <div className="flex items-center gap-4">
          <Badge variant="outline" className="text-blue-400 border-blue-400/50">
            <Brain className="h-3 w-3 mr-1" />
            ML Models Active
          </Badge>
          <Button variant="outline">
            <Settings className="h-4 w-4 mr-2" />
            Model Settings
          </Button>
        </div>
      </div>

      {prediction && (
        <Card className="p-6 border-border bg-card animate-fade-in">
          <div className="space-y-4">
            <div className="flex items-center justify-between">
              <h3 className="text-lg font-semibold text-card-foreground">{prediction.type}</h3>
              <Badge variant="outline" className={getSeverityColor(prediction.severity)}>{prediction.severity}</Badge>
            </div>
            <div className="text-sm text-muted-foreground">
              <p><strong>Location:</strong> {prediction.location}</p>
              <p><strong>Timeframe:</strong> {prediction.timeframe}</p>
            </div>
            <div className="space-y-2">
              <div className="flex items-center justify-between">
                <span className="text-sm font-medium">Risk Probability</span>
                <span className={`text-2xl font-bold ${getProbabilityColor(prediction.probability)}`}>{prediction.probability}%</span>
              </div>
              <div className="w-full bg-muted rounded-full h-2">
                <div className={`h-2 rounded-full transition-all duration-500 ${getProbabilityBgColor(prediction.probability)}`} style={{ width: `${prediction.probability}%` }} />
              </div>
            </div>
            <div className="space-y-2">
              <span className="text-sm font-medium">Key Factors (from simulation)</span>
              <div className="flex flex-wrap gap-1">
                {prediction.factors.map((factor, i) => (<Badge key={i} variant="secondary" className="text-xs">{factor}</Badge>))}
              </div>
            </div>
            <Button variant="outline" className="w-full"><BarChart3 className="h-4 w-4 mr-2" />View Full Analysis</Button>
          </div>
        </Card>
      )}

      <Card className="p-6 border-border bg-card">
        <div className="space-y-6">
          <div className="flex items-center gap-2">
            <Target className="h-5 w-5 text-primary" />
            <h2 className="text-xl font-semibold text-card-foreground">What-If Scenario Simulator</h2>
          </div>
          <p className="text-sm text-muted-foreground">Adjust environmental parameters to see how they affect disaster risk predictions.</p>
          <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div className="space-y-3">
              <div className="flex items-center justify-between">
                <label className="text-sm font-medium">Rainfall (mm/hour)</label>
                <span className="text-sm font-mono text-muted-foreground">{rainfall[0]}mm</span>
              </div>
              <Slider value={rainfall} onValueChange={setRainfall} max={100} min={0} step={1} />
            </div>
            <div className="space-y-3">
              <div className="flex items-center justify-between">
                <label className="text-sm font-medium">Temperature (°C)</label>
                <span className="text-sm font-mono text-muted-foreground">{temperature[0]}°C</span>
              </div>
              <Slider value={temperature} onValueChange={setTemperature} max={50} min={-10} step={1} />
            </div>
            <div className="space-y-3">
              <div className="flex items-center justify-between">
                <label className="text-sm font-medium">Seismic Activity (mag)</label>
                <span className="text-sm font-mono text-muted-foreground">{seismicActivity[0].toFixed(1)}</span>
              </div>
              <Slider value={seismicActivity} onValueChange={setSeismicActivity} max={9} min={0} step={0.1} />
            </div>
          </div>
          {error && <p className="text-sm text-red-500 text-center">{error}</p>}
          <div className="flex items-center gap-4 pt-4 border-t border-border">
            <Button onClick={handleSimulation} disabled={isLoading}>
              {isLoading ? (<Loader2 className="h-4 w-4 mr-2 animate-spin" />) : (<Zap className="h-4 w-4 mr-2" />)}
              {isLoading ? "Analyzing..." : "Run Simulation"}
            </Button>
            <div className="text-sm text-muted-foreground">The prediction card above will update based on these parameters.</div>
          </div>
        </div>
      </Card>
    </div>
  );
};

export default Prediction;
### Frontend: src/pages/resources.tsx
TypeScript

// File: src/pages/resources.tsx

import { useState, useEffect } from "react";
import { Card } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import { 
  Truck, MapPin, Users, Heart, Shield, Plane, Navigation, Clock,
  CheckCircle, AlertCircle, RefreshCw, Map, List, Signal 
} from "lucide-react";

type Resource = {
  id: number;
  type: string;
  name: string;
  location: string;
  status: "available" | "deployed" | "maintenance" | "active";
  personnel: number;
  equipment: string[];
  eta: string | null;
  assignment: string | null;
};

const initialResources: Resource[] = [
  { id: 1, type: "ndrf_unit", name: "NDRF 4th Battalion", location: "Chennai, Tamil Nadu", status: "available", personnel: 45, equipment: ["Inflatable Boats", "Cutting Tools", "First Aid"], eta: null, assignment: null },
  { id: 2, type: "ambulance", name: "Apollo Emergency Unit 12", location: "Mumbai, Maharashtra", status: "deployed", personnel: 4, equipment: ["Mobile Ventilator", "Defibrillator", "Oxygen"], eta: "18 minutes", assignment: "Mumbai Flood Relief - Zone B" },
  { id: 3, type: "helicopter", name: "IAF Dhruv (Air Rescue)", location: "Dehradun, Uttarakhand", status: "available", personnel: 5, equipment: ["Rescue Hoist", "Medical Kits", "Satellite Phone"], eta: null, assignment: null },
  { id: 4, type: "shelter", name: "Govt. Community Center", location: "Bhubaneswar, Odisha", status: "active", personnel: 20, equipment: ["1000 Beds", "Community Kitchen", "Medical Station"], eta: null, assignment: "Cyclone Yaas Evacuees" },
  { id: 5, type: "fire_truck", name: "Delhi Fire Service Unit 7", location: "New Delhi, Delhi", status: "deployed", personnel: 6, equipment: ["Water Cannon", "Ladders", "Breathing Apparatus"], eta: "12 minutes", assignment: "Industrial Fire - Okhla" },
  { id: 6, type: "ndrf_unit", name: "NDRF 8th Battalion", location: "Ghaziabad, Uttar Pradesh", status: "maintenance", personnel: 15, equipment: ["Vehicle Repair", "Equipment Check", "Restocking"], eta: null, assignment: "Scheduled Maintenance" },
  { id: 7, type: "transport_plane", name: "IAF C-17 Globemaster", location: "Hindon Air Force Station", status: "deployed", personnel: 8, equipment: ["Relief Supplies", "Water Purifiers", "Tents"], eta: "4 hours", assignment: "A&N Islands Cyclone Relief" },
  { id: 8, type: "medical_team", name: "Army Field Hospital", location: "Pune, Maharashtra", status: "available", personnel: 60, equipment: ["Surgical Kits", "50 Cots", "Mobile Pharmacy"], eta: null, assignment: null },
  { id: 9, type: "comm_vehicle", name: "Mobile Comms Post", location: "Hyderabad, Telangana", status: "available", personnel: 3, equipment: ["Satellite Uplink", "Radio Repeaters", "Generators"], eta: null, assignment: null },
  { id: 10, type: "shelter", name: "City School Auditorium", location: "Kolkata, West Bengal", status: "available", personnel: 5, equipment: ["Ready-to-deploy cots", "First Aid Station"], eta: null, assignment: "On Standby" }
];

const Resources = () => {
  const [resources, setResources] = useState<Resource[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [selectedResource, setSelectedResource] = useState<number | null>(null);
  const [viewMode, setViewMode] = useState<'list' | 'map'>('list');

  useEffect(() => {
    setIsLoading(true);
    const timer = setTimeout(() => {
      setResources(initialResources);
      setIsLoading(false);
    }, 1000);
    return () => clearTimeout(timer);
  }, []);

  const handleRefresh = () => {
    setIsLoading(true);
    const timer = setTimeout(() => {
      setResources([...initialResources].reverse()); 
      setIsLoading(false);
    }, 500);
    return () => clearTimeout(timer);
  };
  
  const handleDeploy = (id: number) => {
    console.log(`Deploying resource with ID: ${id}`);
    alert(`This would trigger an API call to deploy unit ${id}.`);
  };

  const getResourceIcon = (type: string) => {
    switch (type) {
      case "ambulance": return Heart;
      case "medical_team": return Heart;
      case "ndrf_unit": return Shield;
      case "fire_truck": return Shield;
      case "helicopter": return Plane;
      case "transport_plane": return Plane;
      case "shelter": return Users;
      case "comm_vehicle": return Signal;
      default: return Truck;
    }
  };

  const getStatusColor = (status: Resource['status']) => {
    switch (status) {
      case "available": return "text-green-500 border-green-500/50 bg-green-500/10";
      case "deployed": return "text-orange-500 border-orange-500/50 bg-orange-500/10";
      case "maintenance": return "text-yellow-500 border-yellow-500/50 bg-yellow-500/10";
      case "active": return "text-blue-500 border-blue-500/50 bg-blue-500/10";
      default: return "text-gray-500 border-gray-500/50";
    }
  };

  return (
    <div className="min-h-screen p-6 space-y-6">
      <div className="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4">
        <div>
          <h1 className="text-3xl font-bold text-foreground mb-2">Resource Management</h1>
          <p className="text-muted-foreground">Real-time tracking and deployment of Indian emergency response assets</p>
        </div>
        <div className="flex items-center gap-2 sm:gap-4">
          <Button variant="outline" size="sm" onClick={() => setViewMode('list')} disabled={viewMode === 'list'}><List className={`h-4 w-4 ${viewMode === 'list' ? 'text-primary' : ''}`} /></Button>
          <Button variant="outline" size="sm" onClick={() => setViewMode('map')} disabled={viewMode === 'map'}><Map className={`h-4 w-4 ${viewMode === 'map' ? 'text-primary' : ''}`} /></Button>
          <Button variant="outline" size="sm" onClick={handleRefresh} disabled={isLoading}><RefreshCw className={`h-4 w-4 mr-2 ${isLoading ? 'animate-spin' : ''}`} />Refresh</Button>
        </div>
      </div>

      {viewMode === 'list' ? (
        <div className="grid grid-cols-1 lg:grid-cols-2 gap-6 animate-fade-in">
          {isLoading ? (
            <p className="text-muted-foreground col-span-full text-center py-10">Loading resources...</p>
          ) : (
            resources.map((resource) => {
              const Icon = getResourceIcon(resource.type);
              const isSelected = selectedResource === resource.id;
              
              return (
                <Card 
                  key={resource.id}
                  className={`p-6 border-border bg-card cursor-pointer transition-all duration-200 ${isSelected ? "ring-2 ring-primary" : "hover:shadow-lg"}`}
                  onClick={() => setSelectedResource(isSelected ? null : resource.id)}
                >
                  <div className="space-y-4">
                    <div className="flex items-start justify-between">
                      <div className="flex items-center gap-3">
                        <div className={`rounded-lg p-3 ${getStatusColor(resource.status).split(" ")[2]}`}>
                           <Icon className={`h-6 w-6 ${getStatusColor(resource.status).split(" ")[0]}`} />
                        </div>
                        <div>
                          <h3 className="text-lg font-semibold">{resource.name}</h3>
                          <p className="text-sm text-muted-foreground capitalize">{resource.type.replace("_", " ")}</p>
                        </div>
                      </div>
                      <Badge variant="outline" className={getStatusColor(resource.status)}>{resource.status}</Badge>
                    </div>
                    <div className="space-y-3 pl-2 border-l-2 border-border">
                      <div className="flex items-center gap-2 text-sm"><MapPin className="h-4 w-4" /><span>{resource.location}</span></div>
                      <div className="flex items-center gap-2 text-sm"><Users className="h-4 w-4" /><span>{resource.personnel} personnel</span></div>
                      {resource.assignment && <div className="flex items-center gap-2 text-sm"><Navigation className="h-4 w-4" /><span>{resource.assignment}</span></div>}
                    </div>
                    <div className="flex gap-2 pt-2">
                      {resource.status === "available" && (<Button size="sm" className="flex-1" onClick={() => handleDeploy(resource.id)}><Navigation className="h-4 w-4 mr-2" />Deploy</Button>)}
                       <Button variant="outline" size="sm" className="flex-1">Details</Button>
                    </div>
                  </div>
                </Card>
              );
            })
          )}
        </div>
      ) : (
        <Card className="p-6 h-[60vh] border-border bg-card animate-fade-in flex items-center justify-center">
            <div className="text-center text-muted-foreground">
                <Map className="h-16 w-16 mx-auto mb-4" />
                <h2 className="text-xl font-semibold">Live Map View</h2>
                <p>This area would display a live, interactive map showing the geospatial location of all resources.</p>
            </div>
        </Card>
      )}
    </div>
  );
};

export default Resources;
### Frontend: src/pages/alerts.tsx
TypeScript

// File: src/pages/alerts.tsx

import { useState } from "react";
import { Card } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";
import { 
  Send, 
  Loader2,
  CheckCircle,
  AlertTriangle
} from "lucide-react";

const AlertsPage = () => {
  const [phoneNumber, setPhoneNumber] = useState("");
  const [message, setMessage] = useState(
    "DisasterNet Alert: This is a test warning for your area. Please monitor local news for updates. Stay safe."
  );
  
  const [isLoading, setIsLoading] = useState(false);
  const [apiResponse, setApiResponse] = useState<{
    success: boolean;
    message: string;
  } | null>(null);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    if (!phoneNumber || !message) {
      setApiResponse({ success: false, message: "Phone number and message cannot be empty." });
      return;
    }

    setIsLoading(true);
    setApiResponse(null);

    try {
      const response = await fetch('[http://127.0.0.1:8000/send-sms](http://127.0.0.1:8000/send-sms)', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ phone_number: phoneNumber, message }),
      });

      const result = await response.json();
      setApiResponse(result);

      if (result.success) {
        setPhoneNumber("");
      }

    } catch (err) {
      setApiResponse({ success: false, message: "Failed to connect to the server. Please try again." });
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <div className="min-h-screen p-6 space-y-6">
      <div>
        <h1 className="text-3xl font-bold text-foreground mb-2">Manual Alert Center</h1>
        <p className="text-muted-foreground">Send critical SMS alerts to personnel or the public via Twilio.</p>
      </div>
      
      <Card className="p-6 border-border bg-card max-w-2xl mx-auto">
        <form onSubmit={handleSubmit} className="space-y-6">
          <div>
            <label htmlFor="phone" className="block text-sm font-medium text-foreground mb-2">Recipient Phone Number</label>
            <Input
              id="phone"
              type="tel"
              value={phoneNumber}
              onChange={(e) => setPhoneNumber(e.target.value)}
              placeholder="+91XXXXXXXXXX"
              className="text-lg"
              disabled={isLoading}
            />
            <p className="text-xs text-muted-foreground mt-1">Enter the full number including the country code (e.g., +91 for India).</p>
          </div>
          
          <div>
            <label htmlFor="message" className="block text-sm font-medium text-foreground mb-2">Alert Message</label>
            <Textarea
              id="message"
              value={message}
              onChange={(e) => setMessage(e.target.value)}
              placeholder="Enter your alert message here..."
              className="text-base h-32"
              disabled={isLoading}
            />
          </div>
          
          <Button type="submit" className="w-full" disabled={isLoading}>
            {isLoading ? (<Loader2 className="h-4 w-4 mr-2 animate-spin" />) : (<Send className="h-4 w-4 mr-2" />)}
            {isLoading ? "Sending Alert..." : "Send Alert Now"}
          </Button>
        </form>
      </Card>

      {apiResponse && (
        <Alert variant={apiResponse.success ? "default" : "destructive"} className="max-w-2xl mx-auto animate-fade-in">
          {apiResponse.success ? (<CheckCircle className="h-4 w-4" />) : (<AlertTriangle className="h-4 w-4" />)}
          <AlertTitle>{apiResponse.success ? "Success" : "Error"}</AlertTitle>
          <AlertDescription>{apiResponse.message}</AlertDescription>
        </Alert>
      )}
    </div>
  );
};

export default AlertsPage;
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
