# 🍽️ PantryChef AI — Daily Build Prompt

**Purpose:** Use this prompt every day during the 30-Day Growth Plan.  
**How to use:** Copy the entire prompt below, replace `[DAY NUMBER]` with today's day (1–30), and paste it into a new Claude conversation at the start of each session.

---

## The Prompt

```
I am continuing development on PantryChef AI — a conversational AI-powered cooking
companion for solo cooks. Today is Day [DAY NUMBER] of my 30-Day Growth Plan.

--- PROJECT CONTEXT ---

Live app: https://cookwithpantrychef-ai.vercel.app
Frontend repo: https://github.com/dverma9/pantrychef-frontend
Backend repo: https://github.com/dverma9/pantrychef-backend

Tech stack:
- Frontend: React 18 + Vite → deployed on Vercel
- Backend: Java 17 + Spring Boot 3.x → deployed on Railway
- Database: PostgreSQL (Railway)
- AI: Google Gemini API (gemini-2.5-flash-lite)
- HTTP client: Axios (frontend), RestTemplate (backend)

Local paths:
- Frontend: C:\Users\deepi\Documents\pantrychef-frontend
- Backend: C:\Users\deepi\Documents\pantrychef-backend

Key files:
- Frontend entry: src/App.jsx, src/App.css
- Chat: src/components/ChatWindow.jsx, src/components/MessageBubble.jsx
- Pantry: src/components/PantryManager.jsx, src/components/IngredientItem.jsx
- Preferences: src/components/PreferencesPanel.jsx
- API clients: src/api/chatApi.js, src/api/pantryApi.js, src/api/preferencesApi.js
- Backend AI service: src/main/java/com/pantrychef/service/ClaudeService.java
- Backend controllers: controller/ChatController.java, PantryController.java,
  PreferenceController.java
- Backend config: src/main/resources/application.properties

Current version: v1.0.0 (or whatever version I most recently tagged)

--- TODAY'S TASK ---

Please read the 30-day-growth-plan.md milestone for Day [DAY NUMBER] and implement it
completely.

Rules:
1. Assume I have little development experience. Guide me step by step.
2. Generate complete files — never snippets, placeholders, or "add this below" instructions.
3. For each file, tell me: the exact file path, whether it is new or replaces an existing
   file, and the notepad command to open it.
4. Provide every terminal command I need to run, with the exact directory to run it from.
5. Only build what is scheduled for today. Do not start tomorrow's work.
6. If today's task requires changes to both frontend and backend, build backend first,
   then frontend.
7. After implementation, give me a test checklist — 3 to 5 specific things to verify in
   the browser before committing.
8. End with the exact git commands to commit and push both repos, with a meaningful
   commit message that includes the day number.
9. Use only free tools and services. Do not introduce paid APIs or libraries.
10. If anything breaks during implementation, debug it completely before moving forward.

--- WHAT I NEED FROM YOU ---

1. Confirm which day's milestone you are implementing and summarise it in 2 sentences.
2. List every file that will be created or modified.
3. Implement everything completely.
4. Give me the test checklist.
5. Give me the commit commands.
```

---

## Tips for Getting the Best Results

**Before starting each day:**
- Pull the latest code from GitHub first:
  ```cmd
  cd C:\Users\deepi\Documents\pantrychef-frontend
  git pull origin main

  cd C:\Users\deepi\Documents\pantrychef-backend
  git pull origin main
  ```
- Have the live app open at `https://cookwithpantrychef-ai.vercel.app` for reference
- Read the Day [N] milestone in `30-day-growth-plan.md` so you know what to expect

**If you get stuck:**
- Paste the exact error message into Claude and ask: *"This error appeared after implementing today's changes. Debug it completely."*
- Share a screenshot if the issue is visual

**At the end of each week (Days 7, 14, 21, 30):**
- Tag a version release on GitHub
- Update the README to reflect new features
- Deploy and verify the live URL works end-to-end

**The prompt stays the same every day** — only `[DAY NUMBER]` changes.

---

*PantryChef AI — AB Talks 60-Day Claude AI Challenge*
