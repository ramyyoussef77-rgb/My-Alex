New Task



You are an expert Android engineering team lead. Build “My Alex” — a location-aware community app for Alexandria, Egypt — following the requirements below. Produce: architecture, tech stack choices, database schema, cloud infra (Firebase/Cloud Functions or alternatives), UI wireframes, sample Kotlin code for key features, CI/CD, and a test plan. Prioritize privacy, offline capability, modular components, and Arabic/English bilingual UX (including RTL handling). Features (MVP first):

• Neighborhood chat rooms by location (geofenced rooms / radius / nearby rooms).
• Moderation system (automated filters + admin dashboard + reporting).
• AI Assistant (local FAQs + RAG from local docs; multilingual).
• Floating assistant icon (chat-head overlay).
• Complaints & crises reporting with GPS + timestamp + photo + escalation workflow.
• Theme switcher (light/dark + time-of-day auto).
• Historical places with AR overlays (ARCore) and offline content.
• Local services directory (crowd-sourced listings + WhatsApp contact links).
• News screen that fetches 4 recent posts every 30 minutes from these Facebook pages (use Facebook Graph API & cache; obey TOS):

https://www.facebook.com/share/1CuNA8fLsW/

https://www.facebook.com/share/19FucQXpny/

https://www.facebook.com/share/14GTdbckaVK/

https://www.facebook.com/share/1B981DguFm/

https://www.facebook.com/share/17AVC5M4Uk/

https://www.facebook.com/share/1CFFKVd3jz/

https://www.facebook.com/share/16mFj19vcE/
• Cloud photo gallery with auto-tagging (use Vision API or similar).
• Offline map with POIs and downloadable MBTiles/Mapbox region.
• Events calendar with sync (ICS/Google Calendar) and push reminders.
• Google Sign-in + basic account management.
Deliver: MVP backlog, sprints, acceptance criteria for each feature, admin/moderation panel, monitoring plan, and sample unit + instrumentation tests. Also include localization files (ar/ en), and an accessibility checklist
I'll help you build "My Alex" - a comprehensive location-aware community app for Alexandria, Egypt. This is a substantial project that requires detailed planning across multiple domains. Let me break this down systematically.

1. Architecture Overview
High-Level Architecture
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Android App   │    │  Backend APIs   │    │  External APIs  │
│                 │    │                 │    │                 │
│ • UI Layer      │◄──►│ • Firebase      │◄──►│ • Facebook API  │
│ • Business      │    │ • Cloud Funcs   │    │ • Google Maps   │
│ • Data Layer    │    │ • Firestore     │    │ • Vision API    │
│ • Offline Cache │    │ • Storage       │    │ • ARCore        │
└─────────────────┘    └─────────────────┘    └─────────────────┘
Modular Architecture (Clean Architecture + MVVM)
// Module structure
app/
├── core/
│   ├── common/
│   ├── network/
│   ├── database/
│   └── ui/
├── features/
│   ├── auth/
│   ├── chat/
│   ├── complaints/
│   ├── ar/
│   ├── news/
│   ├── services/
│   ├── events/
│   └── gallery/
└── shared/
    ├── location/
    ├── ai/
    └── moderation/
2. Tech Stack
Core Technologies
Language: Kotlin 100%
Architecture: Clean Architecture + MVVM
DI: Hilt
Async: Coroutines + Flow
Navigation: Navigation Component
UI: Jetpack Compose + Material 3
