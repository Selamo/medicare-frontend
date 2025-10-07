# MediCare AI - API Documentation

**Base URL:** `https://medicare-ai-4.onrender.com`

**Version:** 2.0.0

**Powered by:** LangChain + Google Gemini

---

## Overview

MediCare AI is a medical AI assistant API designed for healthcare applications in Cameroon. It provides three core functionalities:

1. **AI Chat** - Conversational medical information assistant
2. **Medical Record Analysis** - Analyze text and image-based medical records
3. **Medical Research** - Search credible medical research sources

The API supports both English (`en`) and French (`fr`) languages.

---

## Application Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    MediCare AI Application                   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
         ┌────────────────────────────────────────┐
         │  User Interaction Entry Points         │
         └────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
   ┌─────────┐         ┌──────────┐         ┌──────────┐
   │   Chat  │         │ Analysis │         │ Research │
   │ Feature │         │ Feature  │         │ Feature  │
   └─────────┘         └──────────┘         └──────────┘
        │                     │                     │
        │              ┌──────┴──────┐              │
        │              │             │              │
        │              ▼             ▼              │
        │         Text Analysis  Image Analysis     │
        │                                           │
        └───────────────────┬───────────────────────┘
                            │
                            ▼
                 ┌────────────────────┐
                 │  Gemini AI Engine  │
                 │  (Google Gemini)   │
                 └────────────────────┘
```

**Typical User Journeys:**

1. **Chat Journey:** User asks medical question → `/api/chat` → AI responds with information
2. **Text Analysis Journey:** User pastes medical text → `/api/analyze-text` → Structured analysis returned
3. **Image Analysis Journey:** User uploads medical image → `/api/analyze-image` → Text extracted & analyzed
4. **Research Journey:** User searches medical topic → `/api/research` → Credible sources + summary returned

---

## Authentication

Currently, the API does not require authentication. All endpoints are publicly accessible.

---

## Common Headers

All requests should include:

```
Content-Type: application/json
```

For file uploads:
```
Content-Type: multipart/form-data
```

---

## Endpoints

### 1. Health Check

**Endpoint:** `GET /api/health`

**Purpose:** Check if the API service is running

**Request:** No parameters required

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2025-10-03T14:30:00.000Z",
  "message": "MediCare AI Backend is running!"
}
```

**Use Case:** Use this endpoint to verify API availability before making other requests.

---

### 2. AI Chat

**Endpoint:** `POST /api/chat`

**Purpose:** Have a conversational interaction with the medical AI assistant

**Request Body:**
```json
{
  "message": "What are the symptoms of malaria?",
  "language": "en"
}
```

**Field Descriptions:**
- `message` (required): User's question or message (1-1000 characters)
- `language` (optional): `"en"` or `"fr"` (default: `"en"`)

**Response:**
```json
{
  "response": "Malaria symptoms typically include high fever, chills, sweating, headache, nausea, and body aches. Symptoms usually appear 10-15 days after the mosquito bite. In severe cases, it can cause organ failure. If you suspect malaria, seek immediate medical attention for proper diagnosis and treatment.",
  "language": "en",
  "timestamp": "2025-10-03T14:35:00.000Z"
}
```

**Response Fields:**
- `response`: AI-generated response to the user's question
- `language`: Language used for the response
- `timestamp`: When the response was generated

**Use Case:** Build a chat interface where users can ask medical questions and receive evidence-based information.

---

### 3. Analyze Medical Text

**Endpoint:** `POST /api/analyze-text`

**Purpose:** Analyze medical records or reports provided as text

**Request Body:**
```json
{
  "text": "Patient: John Doe, Age: 45\nBlood Pressure: 145/95 mmHg\nBlood Sugar: 180 mg/dL (fasting)\nCholesterol: 240 mg/dL\nDiagnosis: Hypertension, Pre-diabetes",
  "context": "Patient has family history of diabetes",
  "language": "en"
}
```

**Field Descriptions:**
- `text` (required): The medical text to analyze (minimum 1 character)
- `context` (optional): Additional context for better analysis
- `language` (optional): `"en"` or `"fr"` (default: `"en"`)

**Response:**
```json
{
  "summary": "The patient presents with elevated blood pressure (145/95), high fasting blood sugar (180 mg/dL), and elevated cholesterol (240 mg/dL), indicating hypertension and pre-diabetes with cardiovascular risk factors.",
  "key_findings": [
    "Blood pressure 145/95 mmHg indicates Stage 1 Hypertension",
    "Fasting blood sugar of 180 mg/dL suggests pre-diabetes or uncontrolled diabetes",
    "Total cholesterol of 240 mg/dL is in the high-risk category",
    "Multiple cardiovascular risk factors present"
  ],
  "recommendations": [
    "Lifestyle modifications: Reduce salt intake, maintain healthy diet",
    "Regular exercise: At least 150 minutes per week",
    "Monitor blood pressure and blood sugar regularly",
    "Consider medication consultation with physician",
    "Weight management if overweight"
  ],
  "next_steps": [
    "Schedule appointment with primary care physician within 1-2 weeks",
    "Get comprehensive metabolic panel and HbA1c test",
    "Start keeping a food and blood pressure diary",
    "Discuss medication options with healthcare provider"
  ],
  "disclaimer": "This analysis is for informational purposes only. Always consult qualified healthcare professionals for medical advice.",
  "language": "en",
  "timestamp": "2025-10-03T14:40:00.000Z"
}
```

**Response Fields:**
- `summary`: Brief overview of the medical record
- `key_findings`: List of important medical findings
- `recommendations`: Health recommendations based on the findings
- `next_steps`: Actionable steps the patient should take
- `disclaimer`: Legal disclaimer (always present)
- `language`: Language used for analysis
- `timestamp`: When the analysis was completed

**Use Case:** Build a form where users can paste medical records and receive structured analysis.

---

### 4. Analyze Medical Image

**Endpoint:** `POST /api/analyze-image`

**Purpose:** Extract text from medical images (prescriptions, lab reports, etc.) and optionally analyze them

**Request:** `multipart/form-data`

**Form Fields:**
- `file` (required): Image file (JPEG, PNG, etc.)
- `language` (optional): `"en"` or `"fr"` (default: `"en"`)
- `extract_text_only` (optional): `true` or `false` (default: `false`)

**Example Request (using FormData in JavaScript):**
```javascript
const formData = new FormData();
formData.append('file', imageFile);
formData.append('language', 'en');
formData.append('extract_text_only', 'false');
```

**Response (when `extract_text_only=false`):**
```json
{
  "extracted_text": "Patient: Jane Smith\nDate: 2025-10-01\nTest: Complete Blood Count\nWBC: 8.5 x10^9/L (Normal: 4.0-11.0)\nRBC: 4.2 x10^12/L (Normal: 4.0-5.5)\nHemoglobin: 12.5 g/dL (Normal: 12.0-16.0)\nPlatelet: 220 x10^9/L (Normal: 150-400)",
  "analysis": {
    "summary": "Complete blood count results showing all values within normal ranges, indicating healthy blood cell production and no signs of anemia or infection.",
    "key_findings": [
      "White blood cell count is normal (8.5 x10^9/L)",
      "Red blood cell count is within normal range",
      "Hemoglobin level is adequate (12.5 g/dL)",
      "Platelet count is normal"
    ],
    "recommendations": [
      "Continue maintaining a balanced diet rich in iron and vitamins",
      "Stay hydrated",
      "Regular check-ups as recommended by physician"
    ],
    "next_steps": [
      "Keep this report for medical records",
      "Discuss results with your doctor during next appointment",
      "No immediate action required based on these normal results"
    ],
    "disclaimer": "This analysis is for informational purposes only. Always consult qualified healthcare professionals for medical advice.",
    "language": "en",
    "timestamp": "2025-10-03T14:45:00.000Z"
  }
}
```

**Response (when `extract_text_only=true`):**
```json
{
  "extracted_text": "Patient: Jane Smith\nDate: 2025-10-01\nTest: Complete Blood Count\nWBC: 8.5 x10^9/L...",
  "analysis": {
    "summary": "Text extraction completed",
    "key_findings": [],
    "recommendations": [],
    "next_steps": [
      "Review the extracted text",
      "Analyze if needed"
    ],
    "disclaimer": "Text extraction only - no analysis performed",
    "language": "en",
    "timestamp": "2025-10-03T14:45:00.000Z"
  }
}
```

**Response Fields:**
- `extracted_text`: All text extracted from the image
- `analysis`: Full analysis object (same structure as text analysis)

**Use Case:** Build an image upload interface where users can photograph medical documents and receive both extracted text and analysis.

---

### 5. Extract Text Only

**Endpoint:** `POST /api/extract-text`

**Purpose:** Extract text from medical images without analysis (faster response)

**Request:** `multipart/form-data`

**Form Fields:**
- `file` (required): Image file

**Response:**
```json
{
  "extracted_text": "Patient Name: John Doe\nPrescription:\n- Paracetamol 500mg - Take 1 tablet every 6 hours\n- Amoxicillin 250mg - Take 1 capsule 3 times daily for 7 days\nDr. Smith\nLicense: MD12345",
  "timestamp": "2025-10-03T14:50:00.000Z"
}
```

**Use Case:** When you only need text extraction without analysis (useful for OCR functionality).

---

### 6. Medical Research Search

**Endpoint:** `POST /api/research`

**Purpose:** Search credible medical research sources and get summarized results

**Request Body:**
```json
{
  "query": "diabetes management in tropical climates",
  "max_results": 5,
  "language": "en"
}
```

**Field Descriptions:**
- `query` (required): Search query (3-200 characters)
- `max_results` (optional): Number of results to return (1-10, default: 5)
- `language` (optional): `"en"` or `"fr"` (default: `"en"`)

**Response:**
```json
{
  "query": "diabetes management in tropical climates",
  "results": [
    {
      "title": "Diabetes Care in Hot Climates: Special Considerations",
      "url": "https://pubmed.ncbi.nlm.nih.gov/example1",
      "content": "Managing diabetes in tropical climates requires special attention to hydration, insulin storage, and blood sugar monitoring frequency. High temperatures can affect insulin stability and increase the risk of dehydration, which impacts blood glucose levels. Patients should store insulin in cool, dry places and increase fluid intake...",
      "score": 0.95
    },
    {
      "title": "Heat Impact on Glycemic Control",
      "url": "https://nih.gov/example2",
      "content": "Research shows that ambient temperature significantly affects glycemic control in diabetic patients. Heat stress can alter insulin absorption rates and increase insulin sensitivity. Healthcare providers in tropical regions should educate patients about temperature-related blood sugar fluctuations...",
      "score": 0.89
    },
    {
      "title": "Tropical Medicine: Diabetes Management Guidelines",
      "url": "https://who.int/example3",
      "content": "WHO guidelines for diabetes management in tropical settings emphasize the importance of climate-adapted care plans. Key recommendations include frequent blood glucose monitoring during hot seasons, proper medication storage, and awareness of heat-related complications...",
      "score": 0.85
    }
  ],
  "summary": "Managing diabetes in tropical climates requires special considerations including proper insulin storage in cool conditions, increased hydration to prevent dehydration-related blood sugar fluctuations, and more frequent monitoring during hot weather. Healthcare providers should educate patients on heat-related impacts on glycemic control and medication effectiveness.",
  "timestamp": "2025-10-03T14:55:00.000Z"
}
```

**Response Fields:**
- `query`: The search query that was executed
- `results`: Array of research results
  - `title`: Article title
  - `url`: Link to the source
  - `content`: Excerpt from the article (up to 500 characters)
  - `score`: Relevance score (0.0 to 1.0)
- `summary`: AI-generated summary of the key findings
- `timestamp`: When the search was performed

**Trusted Sources Include:**
- PubMed (pubmed.ncbi.nlm.nih.gov)
- NIH (nih.gov)
- WHO (who.int)
- CDC (cdc.gov)
- Mayo Clinic (mayoclinic.org)
- WebMD (webmd.com)
- Healthline (healthline.com)
- Medical News Today (medicalnewstoday.com)

**Use Case:** Build a research section where users can search for evidence-based medical information with cited sources.

---

## Error Handling

All endpoints return standard HTTP status codes:

**Success Codes:**
- `200 OK`: Request successful

**Error Codes:**
- `400 Bad Request`: Invalid input (validation error)
- `500 Internal Server Error`: Server-side processing error

**Error Response Format:**
```json
{
  "detail": "Error description here"
}
```

**Example Error Responses:**

**400 Bad Request:**
```json
{
  "detail": "File must be an image"
}
```

**500 Internal Server Error:**
```json
{
  "detail": "Chat error: Connection timeout"
}
```

**Frontend Error Handling Strategy:**
1. Always wrap API calls in try-catch blocks
2. Display user-friendly error messages
3. For 500 errors, suggest retrying or contacting support
4. For 400 errors, guide user to correct their input

---

## Rate Limiting & Performance

- The API is hosted on Render's free tier
- First request after inactivity may take 30-60 seconds (cold start)
- Subsequent requests are typically fast (1-3 seconds)
- No explicit rate limits, but avoid excessive concurrent requests

**Frontend Best Practices:**
1. Show loading indicators for all API calls
2. Implement request timeouts (30-60 seconds)
3. Cache health check results
4. Debounce chat inputs to avoid excessive requests

---

## Interactive API Documentation

Visit the hosted interactive docs:
- **Swagger UI:** `https://medicare-ai-4.onrender.com/docs`
- **ReDoc:** `https://medicare-ai-4.onrender.com/redoc`

These provide interactive API testing capabilities directly in the browser.

---

## Example Frontend Integration

### JavaScript/TypeScript Example

```javascript
// API Client Configuration
const API_BASE_URL = 'https://medicare-ai-4.onrender.com';

// Chat Example
async function sendChatMessage(message, language = 'en') {
  try {
    const response = await fetch(`${API_BASE_URL}/api/chat`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        message: message,
        language: language
      })
    });
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    return data.response;
  } catch (error) {
    console.error('Chat error:', error);
    throw error;
  }
}

// Image Upload Example
async function analyzeImage(imageFile, language = 'en') {
  try {
    const formData = new FormData();
    formData.append('file', imageFile);
    formData.append('language', language);
    formData.append('extract_text_only', 'false');
    
    const response = await fetch(`${API_BASE_URL}/api/analyze-image`, {
      method: 'POST',
      body: formData
    });
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Image analysis error:', error);
    throw error;
  }
}

// Research Example
async function searchResearch(query, maxResults = 5) {
  try {
    const response = await fetch(`${API_BASE_URL}/api/research`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        query: query,
        max_results: maxResults,
        language: 'en'
      })
    });
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Research error:', error);
    throw error;
  }
}
```

---

## Data Flow Diagrams

### Chat Flow
```
User Input → Frontend
              ↓
         POST /api/chat
              ↓
      Gemini AI Processing
              ↓
      Response Generated
              ↓
    Display to User
```

### Image Analysis Flow
```
User Uploads Image → Frontend
                        ↓
              POST /api/analyze-image
                        ↓
              Image → Text Extraction (Gemini Vision)
                        ↓
              Extracted Text → Medical Analysis (Gemini)
                        ↓
              Structured Analysis
                        ↓
              Display Results to User
```

### Research Flow
```
User Search Query → Frontend
                      ↓
              POST /api/research
                      ↓
              Tavily API Search
                      ↓
              Filter Trusted Sources
                      ↓
              AI Summarization (Gemini)
                      ↓
              Display Results + Summary
```

---

## Important Notes

1. **Disclaimers:** All medical analysis responses include disclaimers. Always display these prominently in your UI.

2. **Language Support:** The API supports both English and French. Implement language toggle in your frontend.

3. **Image Requirements:** 
   - Supported formats: JPEG, PNG, WebP, GIF
   - Maximum size: 10MB (configurable in backend)
   - Clear, high-resolution images work best

4. **Context Window:** For text analysis, there's no strict character limit, but very large documents may be truncated.

5. **No User Authentication:** Currently no user management. Consider implementing session management on frontend if needed.

6. **Data Privacy:** No data is stored permanently. Each request is stateless.

---

## Support & Contact

For issues or questions about the API:
- Check interactive documentation: `/docs`
- Review this documentation
- Test endpoints using the Swagger UI

---

## Version History

- **v2.0.0** (Current) - LangChain integration with Google Gemini
- Supports chat, text analysis, image analysis, and research features
- Bilingual support (English/French)

---

**Last Updated:** October 3, 2025