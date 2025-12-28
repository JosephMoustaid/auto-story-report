# AutoStory - Technical Documentation (Part 2)

## Backend Implementation

### API Architecture

#### RESTful API Design

**Base URL:** `http://localhost:5000/api/v1`

**Design Principles:**
- Resource-based URLs
- HTTP method semantics (GET, POST, PUT, DELETE)
- Stateless communication
- JSON request/response format
- Versioned endpoints (/v1, /v2)
- Consistent error responses

### API Endpoints Overview

#### 1. **Authentication Endpoints** (`/auth`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/auth/register` | Create new user account | No |
| POST | `/auth/login` | Authenticate user | No |
| GET | `/auth/logout` | Invalidate token | Yes |
| GET | `/auth/me` | Get current user | Yes |
| PUT | `/auth/updatedetails` | Update user profile | Yes |
| PUT | `/auth/updatepassword` | Change password | Yes |
| POST | `/auth/forgotpassword` | Request password reset | No |

**Example Request:**
```json
POST /api/v1/auth/register
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "securepassword123",
  "role": "user"
}
```

**Example Response:**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "name": "John Doe",
    "email": "john@example.com",
    "role": "user"
  }
}
```

#### 2. **AI Generation Endpoints** (`/ai`, `/gemini`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/ai/generate-vehicle-story` | Generate complete story package | Yes |
| POST | `/gemini/generate` | Generate text from prompt | Yes |
| POST | `/gemini/chat` | Chat with AI assistant | Yes |
| POST | `/gemini/car-story` | Generate car story by ID | Yes |
| POST | `/gemini/compare-cars` | Compare multiple vehicles | Yes |
| POST | `/gemini/recommend` | Get vehicle recommendations | Yes |
| POST | `/gemini/explain-specs` | Explain technical specs | Yes |
| POST | `/gemini/timeline` | Generate vehicle timeline | Yes |
| POST | `/gemini/analyze-image` | Analyze vehicle image | Yes |
| GET | `/gemini/status` | Check AI service status | No |

**Example Request:**
```json
POST /api/v1/ai/generate-vehicle-story
{
  "carData": {
    "make": "Ford",
    "model": "Mustang",
    "year": 1967,
    "engine": { "Type": "V8", "Horsepower": 390 }
  },
  "tone": "emotional",
  "language": "en",
  "outputFormats": {
    "text": true,
    "video": true,
    "pdf": true
  },
  "images": [
    {
      "url": "https://upload.wikimedia.org/...",
      "source": "Wikipedia"
    }
  ]
}
```

**Example Response:**
```json
{
  "success": true,
  "story": "The 1967 Ford Mustang represents an iconic era...",
  "video": {
    "url": "/exports/video_1234567890.mp4",
    "duration": 45,
    "format": "mp4"
  },
  "pdf": {
    "url": "/exports/story_1234567890.pdf",
    "filename": "Ford_Mustang_1967.pdf"
  },
  "generationTime": 45.2,
  "metadata": {
    "tone": "emotional",
    "language": "en",
    "wordCount": 342
  }
}
```

#### 3. **Dataset Endpoints** (`/dataset`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/dataset/search` | Search vehicles | No |
| GET | `/dataset/cars/:id` | Get car by ID | No |
| GET | `/dataset/analytics` | Get database statistics | No |
| GET | `/dataset/top` | Get top vehicles by criteria | No |
| GET | `/dataset/decades` | Group cars by decade | No |
| GET | `/dataset/makes` | List all manufacturers | No |
| GET | `/dataset/makes/:make` | Get manufacturer stats | No |
| GET | `/dataset/random` | Get random vehicles | No |
| POST | `/dataset/compare` | Compare vehicles | No |

**Search Query Parameters:**
- `query` - Search term (make, model, year)
- `make` - Filter by manufacturer
- `yearFrom` / `yearTo` - Year range
- `minHorsepower` / `maxHorsepower` - Power range
- `bodyType` - Vehicle body type
- `limit` - Results per page (default: 20)
- `page` - Page number (default: 1)
- `sortBy` - Sort field (year, horsepower, make)
- `sortOrder` - asc or desc

#### 4. **Export Endpoints** (`/export`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/export/comparison-report` | Generate comparison report | Yes |
| POST | `/export/markdown` | Export as Markdown | Yes |
| POST | `/export/json` | Export as JSON | Yes |
| GET | `/export/download/:filename` | Download file | Yes |

#### 5. **Health & Monitoring** (`/health`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/health` | API health check | No |
| GET | `/health/db` | Database connection status | No |
| GET | `/health/ai` | AI service status | No |

### Backend Services

#### 1. **AI Service** (`src/services/aiService.js`)

**Responsibilities:**
- Gemini API integration
- Prompt engineering and optimization
- Response parsing and formatting
- Error handling and retries
- Token usage tracking

**Key Methods:**
```javascript
- generateStory(carData, options)
- chatWithAI(message, history)
- analyzeImage(imageData)
- compareCars(carIds)
- getRecommendations(preferences)
```

**Prompt Engineering:**
```javascript
const prompt = `
You are an expert automotive storyteller.
Generate a ${tone} story about the ${year} ${make} ${model}.

Vehicle Details:
- Engine: ${engine}
- Power: ${horsepower} HP
- Generation: ${generation}

Tone: ${tone} (technical/emotional/marketing)
Language: ${language}
Length: 300-500 words

Make it engaging and highlight unique features.
`;
```

#### 2. **Video Generation Service** (`src/services/videoService.js`)

**Technologies:**
- Puppeteer for browser automation
- HTML5 Canvas for rendering
- CSS animations for transitions
- FFmpeg for video encoding (future)

**Process:**
1. Create HTML template with car data
2. Insert images and styling
3. Launch headless browser (Puppeteer)
4. Record animated presentation (30-60 seconds)
5. Export as MP4
6. Return video URL

**Features:**
- Smooth transitions
- Text animations
- Image zoom effects
- Background music
- Custom branding

#### 3. **PDF Generation Service** (`src/services/exportService.js`)

**Technologies:**
- HTML to PDF conversion
- Custom templates
- Image embedding
- Professional layouts

**Process:**
1. Generate HTML from template
2. Inject car data and images
3. Apply CSS styling
4. Convert to PDF
5. Save to filesystem
6. Return download URL

**Template Structure:**
- Cover page with hero image
- Technical specifications table
- Story content with formatting
- Contact information
- Professional footer

#### 4. **Image Service** (`src/services/imageService.js`)

**Image Sources:**
- Wikipedia API
- Wikimedia Commons
- Fallback placeholders

**Process:**
1. Query Wikipedia for vehicle
2. Extract thumbnail images
3. Try multiple query variations
4. Fallback to Wikimedia Commons
5. Use placeholder if none found

**Caching:**
- Future: Redis cache for image URLs
- Reduce API calls
- Faster generation

#### 5. **Dataset Service** (`src/services/datasetService.js`)

**Responsibilities:**
- MongoDB queries
- Data aggregation
- Search indexing
- Statistics calculation

**Database Schema (MongoDB):**
```javascript
const carSchema = new mongoose.Schema({
  Make: String,
  Model: String,
  YearFrom: Number,
  YearTo: Number,
  Generation: String,
  BodyType: String,
  Engine: {
    Type: String,
    Displacement: Number,
    Horsepower: Number,
    Torque: Number,
    FuelType: String
  },
  Performance: {
    TopSpeed: Number,
    Acceleration: Number
  },
  Dimensions: {
    Length: Number,
    Width: Number,
    Height: Number,
    Wheelbase: Number
  },
  Weight: Number,
  Transmission: String,
  Chassis: String,
  Country: String
});
```

### Middleware Stack

#### 1. **Authentication Middleware** (`src/middleware/auth.js`)

```javascript
const protect = async (req, res, next) => {
  let token;
  
  if (req.headers.authorization?.startsWith('Bearer')) {
    token = req.headers.authorization.split(' ')[1];
  }
  
  if (!token) {
    return next(new ErrorResponse('Not authorized', 401));
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = await User.findById(decoded.id);
    next();
  } catch (err) {
    return next(new ErrorResponse('Not authorized', 401));
  }
};
```

#### 2. **Error Handler Middleware** (`src/middleware/error.js`)

```javascript
const errorHandler = (err, req, res, next) => {
  let error = { ...err };
  error.message = err.message;
  
  // Log for debugging
  console.error(err);
  
  // Mongoose bad ObjectId
  if (err.name === 'CastError') {
    error = new ErrorResponse('Resource not found', 404);
  }
  
  // Mongoose duplicate key
  if (err.code === 11000) {
    error = new ErrorResponse('Duplicate field value', 400);
  }
  
  // Mongoose validation error
  if (err.name === 'ValidationError') {
    const message = Object.values(err.errors).map(e => e.message);
    error = new ErrorResponse(message, 400);
  }
  
  res.status(error.statusCode || 500).json({
    success: false,
    error: error.message || 'Server Error'
  });
};
```

#### 3. **Rate Limiting** (`src/middleware/rateLimit.js`)

```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  message: 'Too many requests, please try again later'
});
```

#### 4. **Request Validation** (`src/middleware/validation.js`)

```javascript
const validateCarData = (req, res, next) => {
  const { carData } = req.body;
  
  if (!carData || !carData.make || !carData.model) {
    return res.status(400).json({
      success: false,
      error: 'Make and model are required'
    });
  }
  
  next();
};
```

### Database Design

#### MongoDB Collections

**1. Users Collection**
```javascript
{
  _id: ObjectId,
  name: String,
  email: String (unique, indexed),
  password: String (hashed with bcrypt),
  role: String (enum: ['user', 'admin']),
  bio: String,
  preferences: Object,
  createdAt: Date,
  updatedAt: Date
}
```

**2. Cars Collection**
```javascript
{
  _id: ObjectId,
  Make: String (indexed),
  Model: String (indexed),
  YearFrom: Number (indexed),
  YearTo: Number,
  Generation: String,
  BodyType: String,
  Engine: {
    Type: String,
    Displacement: Number,
    Horsepower: Number (indexed),
    Torque: Number,
    FuelType: String
  },
  // ... other fields
}
```

**3. Generation History (Future)**
```javascript
{
  _id: ObjectId,
  userId: ObjectId (ref: Users),
  carId: ObjectId (ref: Cars),
  story: String,
  videoUrl: String,
  pdfUrl: String,
  options: {
    tone: String,
    language: String
  },
  createdAt: Date
}
```

#### Indexes
```javascript
// Performance optimization
db.cars.createIndex({ Make: 1, Model: 1 });
db.cars.createIndex({ YearFrom: -1 });
db.cars.createIndex({ "Engine.Horsepower": -1 });
db.users.createIndex({ email: 1 }, { unique: true });
```

---

## AI & Machine Learning Integration

### Google Gemini AI

#### Model Selection

**Gemini Pro (Text Generation)**
- Context window: 32,000 tokens
- Output: Up to 8,192 tokens
- Capabilities: Text generation, analysis, reasoning
- Use case: Story generation, chat, comparisons

**Gemini Pro Vision (Image Analysis)**
- Multimodal: Text + Image input
- Image formats: JPEG, PNG, WebP
- Use case: Vehicle image analysis, feature detection

#### Prompt Engineering Strategy

**1. System Prompts**
```
You are an expert automotive historian and storyteller with deep knowledge
of vehicle engineering, design, and cultural impact. Your writing is
engaging, accurate, and tailored to the audience.
```

**2. Few-Shot Learning**
```
Example 1:
Car: 1967 Ford Mustang
Story: "The 1967 Ford Mustang emerged as an icon..."

Example 2:
Car: 2020 Tesla Model 3
Story: "The 2020 Tesla Model 3 revolutionized..."

Now generate for: {user_car}
```

**3. Tone Adaptation**
```javascript
const toneInstructions = {
  technical: "Focus on specifications, engineering details, and performance metrics. Use precise technical language.",
  emotional: "Emphasize the driving experience, cultural impact, and emotional connection. Use vivid, evocative language.",
  marketing: "Highlight unique selling points, competitive advantages, and customer benefits. Use persuasive language."
};
```

**4. Language Localization**
```javascript
const languagePrompts = {
  en: "Write in clear, professional English.",
  es: "Escribe en español profesional y claro.",
  fr: "Écrivez en français professionnel et clair.",
  de: "Schreiben Sie auf professionellem, klarem Deutsch.",
  // ... other languages
};
```

#### Content Generation Pipeline

```
1. Data Collection
   ↓
2. Context Building (car specs, images, history)
   ↓
3. Prompt Construction (tone, language, format)
   ↓
4. API Call (Gemini Pro)
   ↓
5. Response Parsing & Validation
   ↓
6. Post-Processing (formatting, cleanup)
   ↓
7. Output Delivery
```

#### Error Handling & Fallbacks

**Rate Limiting:**
- Gemini API: 60 requests per minute
- Implementation: Queue system with retry logic
- Backoff strategy: Exponential (1s, 2s, 4s, 8s)

**Content Safety:**
- Automatic filtering of inappropriate content
- Manual review flags for edge cases
- Family-friendly default settings

**Quality Assurance:**
- Minimum word count validation (200 words)
- Grammar and spelling checks
- Fact verification against database

#### AI Chat System

**Conversation Context:**
```javascript
const chatHistory = [
  { role: "user", content: "Tell me about the 1967 Mustang" },
  { role: "assistant", content: "The 1967 Mustang was..." },
  { role: "user", content: "How does it compare to modern cars?" }
];
```

**Context Window Management:**
- Keep last 10 messages
- Summarize older context
- Maintain conversation coherence

**Special Commands:**
- `/compare [car1] vs [car2]` - Detailed comparison
- `/recommend [criteria]` - Get recommendations
- `/explain [term]` - Technical explanations
- `/timeline [make] [model]` - Historical timeline

---

## Design Decisions & Technology Choices

### Why We Chose These Technologies

#### 1. **React for Frontend**

**Reasons:**
✅ **Component Reusability** - Modular architecture
✅ **Large Ecosystem** - Extensive library support
✅ **Performance** - Virtual DOM optimization
✅ **Community** - Active community, abundant resources
✅ **Hiring** - Easy to find React developers

**Alternatives Considered:**
- Vue.js: Simpler but smaller ecosystem
- Angular: Too heavy for our use case
- Svelte: Newer, less mature ecosystem

#### 2. **Node.js + Express for Backend**

**Reasons:**
✅ **JavaScript Everywhere** - Same language frontend/backend
✅ **Non-blocking I/O** - Perfect for API-heavy application
✅ **NPM Ecosystem** - Vast package repository
✅ **Scalability** - Easy to scale horizontally
✅ **Real-time Support** - WebSocket compatibility

**Alternatives Considered:**
- Python/Django: Slower for real-time operations
- Java/Spring Boot: More verbose, steeper learning curve
- Go: Faster but less ecosystem maturity

#### 3. **MongoDB for Database**

**Reasons:**
✅ **Schema Flexibility** - Easy to evolve data model
✅ **JSON-like Storage** - Natural fit for JavaScript
✅ **Horizontal Scaling** - Sharding support
✅ **Rich Query Language** - Powerful aggregation pipeline
✅ **Document-Oriented** - Matches our data structure

**Alternatives Considered:**
- PostgreSQL: More rigid schema, complex joins
- MySQL: Less flexible for unstructured data
- Firebase: Vendor lock-in concerns

#### 4. **Google Gemini AI**

**Reasons:**
✅ **Multimodal** - Text + Image analysis
✅ **Large Context Window** - 32K tokens
✅ **Cost-Effective** - Competitive pricing
✅ **Latest Technology** - State-of-the-art model
✅ **Google Backing** - Reliable infrastructure

**Alternatives Considered:**
- OpenAI GPT-4: More expensive, API limits
- Claude: Limited availability
- Open-source LLMs: Infrastructure overhead

#### 5. **Three.js for 3D Graphics**

**Reasons:**
✅ **WebGL Abstraction** - Easier than raw WebGL
✅ **Cross-browser** - Wide compatibility
✅ **Performance** - Optimized rendering
✅ **Community** - Large user base, examples
✅ **Features** - Lights, cameras, materials, loaders

**Alternatives Considered:**
- Babylon.js: Similar capabilities, smaller community
- A-Frame: VR-focused, less flexible
- Unity WebGL: Large bundle size

### Architectural Decisions

#### 1. **REST API over GraphQL**

**Rationale:**
- Simpler to implement and maintain
- Standard HTTP caching works well
- Easier for third-party integrations
- Team familiarity

**Trade-offs:**
- Over-fetching in some scenarios
- Multiple requests for related data
- Could add GraphQL later if needed

#### 2. **JWT Authentication**

**Rationale:**
- Stateless - scales horizontally
- Works across domains
- Compact and self-contained
- Industry standard

**Trade-offs:**
- Can't invalidate before expiration
- Slightly larger than session IDs
- Mitigated with short expiration + refresh tokens

#### 3. **Monolithic over Microservices**

**Rationale:**
- Faster initial development
- Simpler deployment
- Lower operational complexity
- Easier debugging

**Future Plan:**
- Extract AI service as microservice
- Separate video generation service
- Use API gateway pattern

#### 4. **Client-Side Rendering (CSR)**

**Rationale:**
- Rich interactivity requirements
- Real-time 3D rendering
- Authenticated user sessions
- API-driven architecture

**Trade-offs:**
- SEO challenges (solved with meta tags)
- Initial load time (mitigated with code splitting)
- Could add SSR for public pages later

---

*End of Part 2. Shall I continue with Part 3 (Challenges, Prototype Status, Future Vision, Hackathon)?*
