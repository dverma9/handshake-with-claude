# 🍽️ PantryChef AI — 30-Day Growth Plan

**Starting Point:** v1.0.0 — fully deployed, all 5 MVP features working  
**End Goal:** v1.4.0 — persistent chat, saved recipes, smart suggestions, grocery links, and a significantly more complete product  
**Stack:** React + Vite | Java 17 + Spring Boot | PostgreSQL | Gemini API | Vercel + Railway

Each day builds directly on the previous one. Every milestone is achievable in 1–3 hours.  
Use `daily-build-prompt.md` to start each day's session with Claude.

---

## Week 1 — Persistent Chat History (Days 1–7)
**Goal:** Chat conversations survive page refresh. Users never lose a recipe they generated.

---

### Day 1 — Database Schema for Chat History
**What:** Add the `chat_history` table to PostgreSQL.

**File:** Create `V2__add_chat_history.sql` in `src/main/resources/db/migration/`
```sql
CREATE TABLE chat_history (
    id          BIGSERIAL       PRIMARY KEY,
    role        VARCHAR(10)     NOT NULL CHECK (role IN ('user', 'assistant')),
    content     TEXT            NOT NULL,
    created_at  TIMESTAMP       NOT NULL DEFAULT NOW()
);
```
**Also:** Add Flyway dependency to `pom.xml` for managed migrations.  
**Commit:** `Day 1: Add chat_history table via Flyway migration`

---

### Day 2 — ChatHistory JPA Entity & Repository
**What:** Create the Java entity and repository for chat history.

**New files:**
- `entity/ChatHistory.java` — JPA entity mapping `chat_history` table
- `repository/ChatHistoryRepository.java` — `findAllByOrderByCreatedAtAsc()`

**Commit:** `Day 2: Add ChatHistory entity and repository`

---

### Day 3 — Backend: Save Messages on Every Chat Call
**What:** Every message sent and every AI reply is saved to the database.

**Modify:** `ChatController.java` — after getting the AI reply, save both the user message and the AI reply to `chat_history` using the repository.

**Commit:** `Day 3: Persist chat messages to database on every exchange`

---

### Day 4 — Backend: Load History Endpoint
**What:** New endpoint to retrieve full chat history.

**New endpoint:** `GET /api/chat/history` → returns all `ChatHistory` rows ordered by `created_at`  
**New endpoint:** `DELETE /api/chat/history` → clears all history (for "New Chat")

**Commit:** `Day 4: Add GET and DELETE /api/chat/history endpoints`

---

### Day 5 — Frontend: Load History on App Start
**What:** When the app loads, fetch chat history from the backend and populate the chat window.

**Modify:** `ChatWindow.jsx` — add `useEffect` that calls `GET /api/chat/history` on mount and sets the messages state.  
**Modify:** `chatApi.js` — add `getChatHistory()` and `clearChatHistory()` functions.

**Commit:** `Day 5: Load persisted chat history on app start`

---

### Day 6 — Frontend: Wire New Chat to Backend Clear
**What:** The "New Chat" button now also calls `DELETE /api/chat/history` so the clear is persistent.

**Modify:** `ChatWindow.jsx` — `handleClearChat()` calls `clearChatHistory()` before resetting state.

**Commit:** `Day 6: New Chat clears history in database`

---

### Day 7 — Test & Polish Chat Persistence
**What:** Full end-to-end test of persistent chat.

**Test scenarios:**
1. Send 5 messages → refresh page → history loads correctly
2. Click "New Chat" → refresh → history is gone
3. Send message after clearing → new history starts correctly
4. Verify pantry changes mid-conversation still reflect in AI responses

**Commit:** `Day 7: Test and polish persistent chat history — v1.1.0`  
**Tag:** `v1.1.0`

---

## Week 2 — Recipe Save & Favourites (Days 8–14)
**Goal:** Users can save any AI-generated recipe and access it any time from a "My Recipes" tab.

---

### Day 8 — Database Schema for Saved Recipes
**What:** Add the `saved_recipes` table.

```sql
CREATE TABLE saved_recipes (
    id          BIGSERIAL       PRIMARY KEY,
    title       VARCHAR(200)    NOT NULL,
    content     TEXT            NOT NULL,
    saved_at    TIMESTAMP       NOT NULL DEFAULT NOW()
);
```
**Commit:** `Day 8: Add saved_recipes table migration`

---

### Day 9 — Backend: Save & List Recipes API
**What:** Two new endpoints.

- `POST /api/recipes` — save a recipe `{ title, content }`
- `GET /api/recipes` — list all saved recipes
- `DELETE /api/recipes/{id}` — delete a saved recipe

**New files:** `SavedRecipe.java`, `SavedRecipeRepository.java`, `RecipeController.java`, `RecipeService.java`  
**Commit:** `Day 9: Add saved recipes CRUD API`

---

### Day 10 — Frontend: Recipe Detection in MessageBubble
**What:** The message formatter detects when an AI response contains a recipe and renders a "💾 Save Recipe" button beneath it.

**Recipe detection heuristic:** Message contains "Ingredients:" AND numbered steps AND is from the AI.

**Modify:** `MessageBubble.jsx` — add a `isSaveableRecipe()` check. If true, render a save button below the formatted content.

**Commit:** `Day 10: Detect recipes in AI responses and show Save button`

---

### Day 11 — Frontend: Save Recipe Flow
**What:** Clicking "💾 Save Recipe" extracts the recipe title and content and saves it via the API.

**Modify:** `MessageBubble.jsx` — `handleSaveRecipe()` calls `POST /api/recipes`.  
**New file:** `src/api/recipesApi.js`  
**Add toast:** "Recipe saved to My Recipes ✓"

**Commit:** `Day 11: Implement save recipe functionality`

---

### Day 12 — Frontend: My Recipes Tab
**What:** Add a fourth tab "📖 Recipes" showing all saved recipes.

**New file:** `src/components/RecipesPanel.jsx` — fetches and displays saved recipes as expandable cards.

**Modify:** `App.jsx` — add fourth tab, wire up on mobile and desktop.

**Commit:** `Day 12: Add My Recipes tab with saved recipe list`

---

### Day 13 — Frontend: Recipe Card UI
**What:** Each saved recipe renders as a card with title, timestamp, expand/collapse, and a delete button.

**Modify:** `RecipesPanel.jsx` — add card expand toggle, formatted recipe content (reuse `formatAIText()`), delete button calling `DELETE /api/recipes/{id}`.

**Commit:** `Day 13: Polish recipe cards — expand, collapse, delete`

---

### Day 14 — Test & Polish Saved Recipes
**What:** Full end-to-end test of save/retrieve/delete flow.

**Test scenarios:**
1. Generate a recipe in chat → save → go to My Recipes → see it
2. Delete a recipe → confirm it disappears
3. Save 5 recipes → verify all appear, all expandable
4. Refresh after saving → recipes persist

**Commit:** `Day 14: Test and polish saved recipes — v1.2.0`  
**Tag:** `v1.2.0`

---

## Week 3 — Smart Pantry Suggestions (Days 15–21)
**Goal:** When the user adds an ingredient, the AI proactively suggests dishes they could now make — without asking.

---

### Day 15 — Backend: Async Suggestion Endpoint
**What:** New backend endpoint that returns quick dish suggestions for the current pantry state.

**New endpoint:** `POST /api/pantry/suggestions` — calls Gemini with a short prompt: *"Given this pantry: {list}, name 3 dishes the user could make right now. Reply with only a JSON array of dish names."*

**Commit:** `Day 15: Add pantry suggestions endpoint`

---

### Day 16 — Frontend: Trigger Suggestions After Add
**What:** After every successful `addIngredient()` call, fetch suggestions from the new endpoint.

**Modify:** `PantryManager.jsx` — after `fetchIngredients()` succeeds post-add, call the suggestions endpoint and store result in state.

**Commit:** `Day 16: Fetch AI suggestions after adding ingredient`

---

### Day 17 — Frontend: Suggestion Banner UI
**What:** Display the suggestions as a subtle banner above the ingredient list.

**New component:** `src/components/SuggestionBanner.jsx` — shows "✨ You could make: Dal Tadka, Jeera Rice, Aloo Sabzi" with a "Ask Chef" button that pre-fills the chat.

**Commit:** `Day 17: Add smart suggestion banner to pantry panel`

---

### Day 18 — Frontend: "Ask Chef" Pre-fill Chat
**What:** Clicking "Ask Chef" on a suggested dish switches to the Chat tab and pre-fills the input with "Give me a recipe for {dish}".

**Modify:** `App.jsx` — lift `chatInput` state up and pass a setter to `SuggestionBanner`.  
**Modify:** `ChatWindow.jsx` — accept an optional `prefilledMessage` prop.

**Commit:** `Day 18: Ask Chef button pre-fills chat with suggested dish`

---

### Day 19 — Suggestion Loading & Empty States
**What:** Add a loading state while suggestions fetch, and graceful empty state if Gemini returns nothing useful.

**Modify:** `SuggestionBanner.jsx` — show a subtle pulsing "Getting suggestions..." while loading. Hide banner entirely if suggestions are empty or fetch fails (silent failure — not critical path).

**Commit:** `Day 19: Add loading and empty states to suggestion banner`

---

### Day 20 — Performance: Debounce Suggestions
**What:** If user adds 3 ingredients rapidly, only trigger the suggestion fetch once (500ms debounce).

**Modify:** `PantryManager.jsx` — use `useRef` + `setTimeout` to debounce the suggestion API call.

**Commit:** `Day 20: Debounce suggestion fetch on rapid ingredient adds`

---

### Day 21 — Test & Polish Smart Suggestions
**What:** Full test of the suggestion flow.

**Test scenarios:**
1. Add one ingredient → see suggestions appear
2. Add 3 quickly → confirm only one suggestion call fires
3. Click "Ask Chef" → confirm chat pre-fills correctly and AI responds
4. Empty pantry → confirm no banner shown
5. Suggestion fetch fails → confirm no error shown to user

**Commit:** `Day 21: Test and polish smart pantry suggestions — v1.3.0`  
**Tag:** `v1.3.0`

---

## Week 4 — Grocery Deep Links & Final Polish (Days 22–30)
**Goal:** One-tap grocery ordering for missing ingredients. Full UI/UX polish pass. v1.4.0 ready.

---

### Day 22 — Detect Missing Ingredient Lists
**What:** The message formatter detects when the AI has listed missing ingredients and marks those bubbles as "actionable".

**Modify:** `MessageBubble.jsx` — add `hasMissingIngredients()` check: message contains "missing" AND bullet list. Pass a flag to render the grocery section.

**Commit:** `Day 22: Detect missing ingredient responses in chat`

---

### Day 23 — Parse Missing Ingredients from AI Response
**What:** Extract the ingredient names from the bullet list into a usable array.

**Modify:** `MessageBubble.jsx` — `parseMissingIngredients(text)` returns `["Semolina", "Sugar", "Ghee"]` from the AI's bullet list.

**Commit:** `Day 23: Parse missing ingredient names from AI response`

---

### Day 24 — Blinkit & Swiggy Deep Links
**What:** Below every missing ingredient list, render "Order on Blinkit" and "Order on Instamart" buttons for each item.

**Links:**
- Blinkit: `https://blinkit.com/s/?q={ingredient}`
- Swiggy Instamart: `https://www.swiggy.com/instamart/search?query={ingredient}`

**Modify:** `MessageBubble.jsx` — render a `GroceryLinks` section after missing ingredient bullets.  
**New component:** `src/components/GroceryLinks.jsx`

**Commit:** `Day 24: Add Blinkit and Swiggy Instamart deep links for missing ingredients`

---

### Day 25 — "Add to Pantry" from Chat
**What:** When the AI lists ingredients (whether available or missing), show an "Add to Pantry" button that adds that item directly without switching tabs.

**Modify:** `MessageBubble.jsx` — parse ingredient names, show "+ Add to Pantry" button per item.  
**Wire up:** Call `POST /api/pantry` from the chat bubble — no tab switch needed.

**Commit:** `Day 25: Add to Pantry directly from chat ingredient lists`

---

### Day 26 — UI Consistency Pass
**What:** Review every screen at 375px, 768px, and 1440px. Fix any spacing, font size, or layout inconsistency found.

**Areas to audit:** empty states, loading skeletons, error banners, toast positions, recipe cards, suggestion banner, grocery links section.

**Commit:** `Day 26: UI consistency pass across all breakpoints`

---

### Day 27 — Performance Audit
**What:** Check bundle size, Lighthouse score, and API response times.

**Tools:**
- `npm run build` → check `dist` folder sizes
- Chrome DevTools → Lighthouse → run on live URL
- Railway logs → check p95 API response times

**Target:** Lighthouse Performance > 90, Accessibility > 95  
**Fix any issues found.**

**Commit:** `Day 27: Performance audit and fixes`

---

### Day 28 — Accessibility Audit
**What:** Full keyboard navigation test and screen reader check.

**Test:** Tab through the entire app using only the keyboard. Every interactive element should be reachable and clearly focused.

**Common fixes:**
- Missing `aria-label` on icon buttons
- Focus trap in modals (if any added)
- Colour contrast check on grey text

**Commit:** `Day 28: Accessibility audit and fixes`

---

### Day 29 — Update All Documentation
**What:** Update README, ARCHITECTURE.md, API.md, and future-scope.md to reflect v1.4.0.

**Checklist:**
- New endpoints documented in API.md
- Architecture diagram updated (new tables, new components)
- README features table updated
- `future-scope.md` 3-month items marked ✅ complete

**Commit:** `Day 29: Update all documentation for v1.4.0`

---

### Day 30 — Tag v1.4.0 & Write Release Notes
**What:** Create the GitHub release with a proper changelog.

**Release notes format:**
```
## v1.4.0 — 30 Days Later

### New Features
- Persistent chat history across sessions
- Save and manage favourite recipes
- Smart pantry suggestions after adding ingredients
- One-tap grocery ordering via Blinkit and Swiggy Instamart
- Add to Pantry directly from chat

### Improvements
- UI consistency across all breakpoints
- Performance improvements (Lighthouse > 90)
- Accessibility improvements (tab navigation, ARIA)

### Bug Fixes
- [any bugs fixed during the month]
```

**Commit:** `Day 30: Release notes, changelog, v1.4.0`  
**Tag:** `v1.4.0`

---

## Summary

| Week | Theme | Version | Key Deliverable |
|------|-------|---------|----------------|
| Week 1 (Days 1–7) | Persistent Chat | v1.1.0 | Chat survives refresh |
| Week 2 (Days 8–14) | Saved Recipes | v1.2.0 | My Recipes tab |
| Week 3 (Days 15–21) | Smart Suggestions | v1.3.0 | Proactive dish ideas |
| Week 4 (Days 22–30) | Grocery Links + Polish | v1.4.0 | End-to-end ordering flow |

---

*PantryChef AI — Built as part of the AB Talks 60-Day Claude AI Challenge*  
*v1.0.0 → v1.4.0 in 30 days*
