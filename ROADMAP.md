# GOALZY — Future Roadmap Integration Points

Architecture is prepared so V2–V6 features plug in without rewrites.

---

## V1 — Core Productivity Platform ✅ (Current)

**Status:** Phase 0 + Phase 1 complete with mock backend.

| Module | Integration Point |
|--------|-------------------|
| Auth | `AuthRepository` → swap `MockAuthRepository` for Supabase auth |
| Goals/Tasks/Habits | Feature repositories → Supabase tables with RLS |
| Calendar | `CalendarRepository` → sync with device calendar + AI scheduling hook |
| Analytics | `AnalyticsRepository` → aggregate from `life_scores`, `analytics` tables |
| AI Assistant | `AiRepository` → `AiServiceStub` → FastAPI + Gemini |
| Gamification | `GamificationService` → `xp_logs`, `credits`, `achievements` tables |

---

## V2 — AI Auto Pilot

**Architecture ready:**

- `AutopilotPlan` entity in `lib/shared/domain/entities/brain.dart`
- `GoalzyBrainService.buildAiPayload()` aggregates context for scheduling
- `CalendarRepository` accepts AI-generated events

**Integration steps:**

1. Add `AutopilotRepository` implementing plan CRUD
2. Backend endpoint: `POST /autopilot/generate` using Gemini + brain context
3. UI: new tab or dashboard card → `AutopilotScreen`
4. Wire calendar auto-insert from generated plans

**Files to extend:**

- `lib/shared/domain/entities/brain.dart`
- `lib/features/calendar/domain/repositories/calendar_repository.dart`
- New: `lib/features/autopilot/` feature module

---

## V3 — Life Twin AI

**Architecture ready:**

- `GoalzyBrainService` stores goal, habit, productivity context + memory entries
- `AiMemoryEntry.embeddingId` reserved for Qdrant vector IDs
- `AiRepository` context cards surface relevant life data

**Integration steps:**

1. Deploy Qdrant collection `goalzy_memory`
2. FastAPI: embed messages → store → retrieve on chat
3. Extend `GoalzyBrainService.addMemory()` to call vector store
4. Add `LifeTwinScreen` with personality tuning from preferences

---

## V4 — Achievement Universe

**Architecture ready:**

- `Achievement`, `XpLog`, `Streak` entities
- `GamificationService.checkAchievementUnlock()` rule engine
- Profile screen displays achievements grid

**Integration steps:**

1. Expand achievement definitions in Supabase
2. Add 3D/badge assets (Rive/Lottie) to `shared/widgets/animations/`
3. New `AchievementUniverseScreen` with explorable badge map
4. Real-time unlock notifications via Supabase Realtime

---

## V5 — Goalzy Marketplace

**Integration steps:**

1. New feature: `lib/features/marketplace/`
2. Entities: `MarketplaceItem`, `Purchase`, `CreatorProfile`
3. Stripe/RevenueCat for payments
4. Premium gating via `UserProfile.isPremium`

**No rewrites needed** — add feature module + route in `app_router.dart`.

---

## V6 — Goalzy OS Ecosystem

**Integration steps:**

1. Web: `flutter build web` — responsive sidebar already in `AdaptiveLayout`
2. Desktop: Windows/macOS/Linux builds
3. Widget extensions via platform channels
4. Shared `GoalzyBrainService` syncs across devices via Supabase Realtime

---

## Database Tables (Supabase Preparation)

Models and repositories exist for:

| Table | Entity | Repository |
|-------|--------|------------|
| users | `UserProfile` | `AuthRepository` |
| goals | `Goal` | `GoalRepository` |
| goal_milestones | `GoalMilestone` | `GoalRepository` |
| tasks | `TaskItem` | `TaskRepository` |
| task_history | (extend TaskItem) | `TaskRepository` |
| habits | `Habit` | `HabitRepository` |
| habit_logs | `HabitLog` | `HabitRepository` |
| streaks | `Streak` | `GamificationService` |
| xp_logs | `XpLog` | `GamificationService` |
| credits | UserProfile.credits | `GamificationService` |
| achievements | `Achievement` | Profile + Gamification |
| analytics | `AnalyticsSnapshot` | `AnalyticsRepository` |
| life_scores | `LifeScore` | `AnalyticsRepository` |
| focus_sessions | (future entity) | Calendar |
| ai_memory | `AiMemoryEntry` | `GoalzyBrainService` |
| autopilot_plans | `AutopilotPlan` | V2 feature |
| notifications | `AppNotification` | `NotificationRepository` |
| reports | weekly/monthly maps | `AnalyticsRepository` |

---

## AI Stack Integration

```
Flutter App
    ↓ AiRepository
MockAiService (dev) / HTTP Client (prod)
    ↓
FastAPI Backend
    ├── Gemini Flash (chat, suggestions, analysis)
    └── Qdrant (memory retrieval)
```

Replace `aiServiceProvider` in `lib/core/di/providers.dart`.

---

## Voice Assistant (V3+)

Placeholder in AI chat screen (`Icons.mic`). Architecture:

- Add `VoiceService` interface in `core/network/`
- Speech-to-text → `AiRepository.chat()` → text-to-speech
- Wire mic button in `ai_chat_widgets.dart`

---

## Premium Features Gating

`UserProfile.isPremium` controls:

- Unlimited AI messages (`AppConstants.maxFreeAiMessages`)
- Auto Pilot, Life Twin, Future Simulator
- Advanced reports, premium themes

Check in providers before enabling premium actions.
