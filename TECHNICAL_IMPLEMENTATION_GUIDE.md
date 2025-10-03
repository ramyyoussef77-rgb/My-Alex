# Technical Implementation Guide - My Alex (إسكندريتي)

## AI Assistant Integration Architecture

### 1. AI Service Layer
```typescript
// AI Service Interface
interface AIService {
  processQuery(query: string, context: UserContext): Promise<AIResponse>;
  generateResponse(type: ResponseType, data: any): Promise<string>;
  validateResponse(response: string): Promise<boolean>;
}

// User Context Interface
interface UserContext {
  userId: string;
  location: GeoLocation;
  language: 'ar' | 'en';
  preferences: UserPreferences;
  sessionHistory: ConversationHistory[];
}
```

### 2. Response Types & Templates
```typescript
enum ResponseType {
  LOCAL_INFO = 'local_info',
  CULTURAL_HERITAGE = 'cultural_heritage',
  SERVICE_DISCOVERY = 'service_discovery',
  MARKETPLACE_HELP = 'marketplace_help',
  NEWS_UPDATE = 'news_update',
  EMERGENCY_ASSISTANCE = 'emergency_assistance'
}

interface AIResponse {
  type: ResponseType;
  content: string;
  confidence: number;
  sources: string[];
  suggestedActions: Action[];
  relatedTopics: string[];
}
```

### 3. Knowledge Base Integration
```typescript
// Knowledge Base Structure
interface KnowledgeBase {
  localServices: Service[];
  culturalSites: CulturalSite[];
  historicalData: HistoricalInfo[];
  currentEvents: Event[];
  emergencyContacts: Contact[];
}

// Service Discovery
interface Service {
  id: string;
  name: string;
  category: ServiceCategory;
  location: GeoLocation;
  contactInfo: ContactInfo;
  rating: number;
  reviews: Review[];
  availability: Availability;
}
```

### 4. Multilingual Support
```typescript
// Language Management
interface LanguageManager {
  detectLanguage(text: string): 'ar' | 'en';
  translateText(text: string, targetLang: 'ar' | 'en'): string;
  formatResponse(response: AIResponse, language: 'ar' | 'en'): string;
  handleRTL(text: string): string;
}

// Arabic-specific handling
interface ArabicSupport {
  handleRTL: boolean;
  arabicNumbers: boolean;
  culturalContext: CulturalContext;
  religiousConsiderations: ReligiousContext;
}
```

## Database Schema for AI Features

### 1. AI Conversations Table
```sql
CREATE TABLE ai_conversations (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  session_id UUID,
  query TEXT NOT NULL,
  response TEXT NOT NULL,
  response_type VARCHAR(50),
  confidence_score DECIMAL(3,2),
  language VARCHAR(2),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_ai_conversations_user_id ON ai_conversations(user_id);
CREATE INDEX idx_ai_conversations_session_id ON ai_conversations(session_id);
CREATE INDEX idx_ai_conversations_created_at ON ai_conversations(created_at);
```

### 2. Knowledge Base Tables
```sql
-- Local Services
CREATE TABLE local_services (
  id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  name_ar VARCHAR(255) NOT NULL,
  category VARCHAR(100) NOT NULL,
  description TEXT,
  description_ar TEXT,
  location POINT NOT NULL,
  address TEXT,
  address_ar TEXT,
  phone VARCHAR(20),
  whatsapp VARCHAR(20),
  website VARCHAR(255),
  rating DECIMAL(3,2) DEFAULT 0,
  review_count INTEGER DEFAULT 0,
  is_verified BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Cultural Sites
CREATE TABLE cultural_sites (
  id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  name_ar VARCHAR(255) NOT NULL,
  type VARCHAR(100) NOT NULL, -- monument, museum, historical_site
  description TEXT,
  description_ar TEXT,
  location POINT NOT NULL,
  historical_period VARCHAR(100),
  cultural_significance TEXT,
  cultural_significance_ar TEXT,
  visiting_hours TEXT,
  entry_fee DECIMAL(10,2),
  ar_content_url VARCHAR(255),
  images JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### 3. AI Response Templates
```sql
CREATE TABLE ai_response_templates (
  id UUID PRIMARY KEY,
  response_type VARCHAR(50) NOT NULL,
  language VARCHAR(2) NOT NULL,
  template TEXT NOT NULL,
  variables JSONB,
  context_requirements JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);
```

## API Endpoints for AI Features

### 1. AI Chat Endpoint
```typescript
// POST /api/ai/chat
interface ChatRequest {
  message: string;
  sessionId?: string;
  context?: UserContext;
  language?: 'ar' | 'en';
}

interface ChatResponse {
  response: string;
  sessionId: string;
  confidence: number;
  responseType: ResponseType;
  suggestedActions: Action[];
  relatedTopics: string[];
}
```

### 2. Service Discovery Endpoint
```typescript
// GET /api/ai/services/discover
interface ServiceDiscoveryRequest {
  query: string;
  location: GeoLocation;
  radius?: number;
  category?: string;
  language: 'ar' | 'en';
}

interface ServiceDiscoveryResponse {
  services: Service[];
  totalCount: number;
  searchSuggestions: string[];
  relatedCategories: string[];
}
```

### 3. Cultural Information Endpoint
```typescript
// GET /api/ai/culture/info
interface CulturalInfoRequest {
  siteId?: string;
  query?: string;
  location?: GeoLocation;
  language: 'ar' | 'en';
}

interface CulturalInfoResponse {
  sites: CulturalSite[];
  historicalContext: HistoricalContext;
  relatedEvents: Event[];
  arContent: ARContent[];
}
```

## Frontend Integration

### 1. AI Chat Component
```typescript
// React Native AI Chat Component
interface AIChatProps {
  onResponse: (response: AIResponse) => void;
  language: 'ar' | 'en';
  context: UserContext;
  floating?: boolean;
}

const AIChat: React.FC<AIChatProps> = ({ onResponse, language, context, floating }) => {
  const [messages, setMessages] = useState<Message[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  
  const sendMessage = async (message: string) => {
    setIsLoading(true);
    try {
      const response = await aiService.chat({
        message,
        language,
        context
      });
      setMessages(prev => [...prev, { type: 'user', content: message }, { type: 'ai', content: response.response }]);
      onResponse(response);
    } catch (error) {
      // Handle error
    } finally {
      setIsLoading(false);
    }
  };
  
  return (
    <View style={styles.container}>
      {/* Chat UI implementation */}
    </View>
  );
};
```

### 2. Floating AI Assistant
```typescript
// Floating AI Assistant Component
const FloatingAI: React.FC = () => {
  const [isExpanded, setIsExpanded] = useState(false);
  const [position, setPosition] = useState({ x: 20, y: 100 });
  
  return (
    <Animated.View
      style={[
        styles.floatingContainer,
        {
          transform: [{ translateX: position.x }, { translateY: position.y }]
        }
      ]}
    >
      <TouchableOpacity
        onPress={() => setIsExpanded(!isExpanded)}
        style={styles.floatingButton}
      >
        <Text style={styles.aiIcon}>🤖</Text>
      </TouchableOpacity>
      
      {isExpanded && (
        <AIChat
          language={userLanguage}
          context={userContext}
          onResponse={handleAIResponse}
        />
      )}
    </Animated.View>
  );
};
```

## AI Response Processing Pipeline

### 1. Query Analysis
```typescript
class QueryAnalyzer {
  async analyzeQuery(query: string, context: UserContext): Promise<QueryAnalysis> {
    const intent = await this.detectIntent(query);
    const entities = await this.extractEntities(query);
    const language = await this.detectLanguage(query);
    const confidence = await this.calculateConfidence(query, intent);
    
    return {
      intent,
      entities,
      language,
      confidence,
      context
    };
  }
  
  private async detectIntent(query: string): Promise<Intent> {
    // Use NLP to detect user intent
    // Categories: question, request, complaint, emergency, etc.
  }
  
  private async extractEntities(query: string): Promise<Entity[]> {
    // Extract location names, service types, time references, etc.
  }
}
```

### 2. Response Generation
```typescript
class ResponseGenerator {
  async generateResponse(analysis: QueryAnalysis): Promise<AIResponse> {
    const responseType = this.determineResponseType(analysis);
    const template = await this.getTemplate(responseType, analysis.language);
    const data = await this.gatherData(analysis);
    const response = await this.fillTemplate(template, data);
    
    return {
      type: responseType,
      content: response,
      confidence: analysis.confidence,
      sources: data.sources,
      suggestedActions: this.generateActions(analysis),
      relatedTopics: this.findRelatedTopics(analysis)
    };
  }
}
```

### 3. Quality Assurance
```typescript
class ResponseValidator {
  async validateResponse(response: AIResponse): Promise<ValidationResult> {
    const checks = [
      this.checkAccuracy(response),
      this.checkCompleteness(response),
      this.checkCulturalSensitivity(response),
      this.checkLanguageQuality(response)
    ];
    
    const results = await Promise.all(checks);
    const overallScore = results.reduce((sum, result) => sum + result.score, 0) / results.length;
    
    return {
      isValid: overallScore > 0.8,
      score: overallScore,
      issues: results.filter(r => r.score < 0.8).map(r => r.issue)
    };
  }
}
```

## Monitoring & Analytics

### 1. AI Performance Metrics
```typescript
interface AIMetrics {
  responseTime: number;
  accuracy: number;
  userSatisfaction: number;
  languageDistribution: { ar: number; en: number };
  responseTypeDistribution: Record<ResponseType, number>;
  errorRate: number;
  conversationLength: number;
}

// Analytics Collection
class AIAnalytics {
  async trackQuery(query: string, response: AIResponse, metrics: AIMetrics): Promise<void> {
    await this.analyticsService.track('ai_query', {
      queryLength: query.length,
      responseType: response.type,
      confidence: response.confidence,
      responseTime: metrics.responseTime,
      language: response.language
    });
  }
}
```

### 2. Continuous Learning
```typescript
class LearningSystem {
  async analyzeUserFeedback(feedback: UserFeedback): Promise<void> {
    // Analyze user ratings, corrections, and usage patterns
    // Update response templates and knowledge base
    // Improve intent detection and entity extraction
  }
  
  async updateKnowledgeBase(newData: any): Promise<void> {
    // Regularly update local services, events, and cultural information
    // Validate and integrate new data sources
    // Maintain data quality and accuracy
  }
}
```

This technical implementation guide provides the foundation for building a robust, scalable AI assistant that can effectively serve the Alexandria community while maintaining high standards of accuracy, cultural sensitivity, and user experience.