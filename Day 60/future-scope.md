# 🍽️ PantryChef AI — Future Scope

**Current Version:** v1.0.0  
**Live App:** [cookwithpantrychef-ai.vercel.app](https://cookwithpantrychef-ai.vercel.app)  
**Stack:** React + Vite → Vercel | Java 17 + Spring Boot → Railway | PostgreSQL | Gemini AI

This document outlines how PantryChef AI could realistically evolve over the next 12 months —
building on the v1.0.0 foundation without redesigning what already works.

---

## 3-Month Roadmap (v1.1 — v1.3)
### Theme: Smarter Pantry, Richer Conversations

### Month 1 — v1.1: Persistent Chat History
**What:** Save conversation history to PostgreSQL so the AI remembers past conversations across sessions.

**Why:** Currently, chat clears on page refresh. Users lose recipes they generated. This is the single biggest quality-of-life gap in v1.0.

**How:**
- Add `chat_history` table: `id`, `role`, `content`, `timestamp`
- Extend `ChatController` to load and save history per request
- Add "History" tab in frontend showing past conversations
- Let user click any past message to resume that conversation

**Effort:** 3–4 days. Schema is simple. The REST API change is small.

---

### Month 2 — v1.2: Recipe Save & Favourites
**What:** Let users save generated recipes to a personal collection and access them any time.

**Why:** The AI generates great recipes — but users lose them the moment they start a new chat. A saved recipe collection turns PantryChef AI from a one-time tool into a daily habit.

**How:**
- Add `saved_recipes` table: `id`, `title`, `content`, `saved_at`
- Add "Save Recipe" button in the MessageBubble when AI generates a recipe
- Add "My Recipes" tab showing all saved recipes, searchable
- Allow users to delete saved recipes

**Effort:** 4–5 days. Requires recipe detection logic in MessageBubble.

---

### Month 3 — v1.3: Smart Pantry Suggestions
**What:** When the user adds a pantry item, AI proactively suggests 2–3 quick dishes they could make right now — without the user having to ask.

**Why:** The biggest friction in v1.0 is that users must remember to ask. Proactive suggestions make the pantry panel feel alive.

**How:**
- On every `POST /api/pantry` (add ingredient), trigger an async AI call
- Return dish suggestions alongside the new ingredient in the response
- Display as a subtle "✨ You could make X now" banner above the pantry list

**Effort:** 3 days. The Gemini integration already exists.

---

## 6-Month Roadmap (v1.4 — v1.6)
### Theme: Grocery Intelligence & Daily Use

### Month 4 — v1.4: Grocery Delivery Integration
**What:** When the AI identifies missing ingredients, show a "Order on Blinkit" or "Order on Swiggy Instamart" deep link.

**Why:** This closes the loop the app was designed to address. The user knows what's missing — this makes ordering it a one-tap action.

**How:**
- Detect missing ingredient lists in AI responses (already formatted as bullets)
- Append deep-link buttons: `blinkit://search?q={ingredient}`
- For web: `https://blinkit.com/search?q={ingredient}`
- No API key needed — deep links are public

**Effort:** 2 days. Purely frontend change.

---

### Month 5 — v1.5: Nutritional Information
**What:** When the AI generates a recipe, automatically show estimated calories, protein, carbs, and fat per serving.

**Why:** Health-conscious solo cooks want to know what they're eating. This adds meaningful value without changing the core conversation flow.

**How:**
- Integrate [Open Food Facts API](https://world.openfoodfacts.org/) (free, open source)
- After recipe generation, call the API for each ingredient
- Display a simple nutrition card below the recipe bubble

**Effort:** 5–7 days. Requires ingredient name normalisation (hard part).

---

### Month 6 — v1.6: Meal Planning Calendar
**What:** A weekly view where the user can plan meals for each day, using AI suggestions based on pantry contents.

**Why:** Turns PantryChef AI from reactive ("what can I cook tonight?") to proactive ("what should I cook this week?"). Increases daily engagement significantly.

**How:**
- Add `meal_plans` table: `id`, `date`, `meal_type` (breakfast/lunch/dinner), `dish_name`, `recipe_content`
- Add "Plan My Week" button in Chat — AI generates 7 days of meals from pantry
- Add Calendar view tab showing the week at a glance
- User can edit/replace any day's meal

**Effort:** 8–10 days. Biggest feature of the 6-month plan.

---

## 12-Month Roadmap (v2.0)
### Theme: Multi-User Platform & Mobile

### Months 7–8 — v2.0: User Authentication
**What:** Allow multiple users to create accounts, each with their own pantry and preferences.

**Why:** Right now, PantryChef AI is a single-user app. This is the architectural change that turns it into a product anyone can share with a friend, a partner, or a family member.

**How:**
- Add Spring Security + JWT token authentication
- Add `users` table
- Scope all queries by `user_id`
- Add login / register screens in React
- Free option: Supabase Auth (handles JWT, refresh tokens, email verification)

**Effort:** 10–14 days. This is the biggest technical lift in the roadmap.

---

### Months 9–10 — v2.1: Native Mobile App (React Native)
**What:** iOS and Android apps built with React Native, sharing the same Spring Boot backend.

**Why:** Cooking is a kitchen activity. A mobile app that you can reference while standing at the stove — with the phone propped up — is far more useful than opening a browser tab.

**Key features added on mobile:**
- Camera-based pantry scanning (photograph ingredients to auto-add them)
- Voice input for chat ("Hey PantryChef, what can I make?")
- Push notifications for meal plan reminders

**Effort:** 3–4 weeks for a functional MVP mobile app.

---

### Months 11–12 — v2.2: Social & Community Features
**What:** Let users share their favourite AI-generated recipes publicly, follow other home cooks, and discover popular dishes.

**Why:** Every social cooking platform (Cookpad, Yummly) grew through community. AI-generated recipes personalised to real pantries are something no existing platform offers.

**Key features:**
- Public recipe sharing with one click
- Community feed of trending recipes this week
- "Make this" button — imports a shared recipe's ingredients into your pantry
- Comments and ratings on shared recipes

**Effort:** 4–6 weeks for a basic social layer.

---

## Technology Upgrades to Consider

| Upgrade | When | Why |
|---------|------|-----|
| WebSockets for real-time chat | v1.2+ | Streaming AI responses character by character instead of waiting for the full response |
| Redis caching for pantry | v1.4+ | Cache pantry contents to reduce DB calls on every chat message |
| CDN for recipe images | v1.5+ | AI-generated dish images (Gemini image generation) |
| Multi-model support | v2.0 | Let users choose between Gemini, Claude, GPT-4 for their AI companion |
| PWA to native | v2.1 | Upgrade the existing PWA manifest to a full React Native app |

---

## What Will NOT Change

- The core product insight: start with what you have, not what the recipe requires
- The warm, conversational tone of the AI companion
- The zero-cost architecture principle (free tiers where possible)
- The simplicity of the UI — one chat, one pantry, one settings page

---

*PantryChef AI v1.0.0 — Built as part of the AB Talks 60-Day Claude AI Challenge*
