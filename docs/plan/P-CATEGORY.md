# P-CATEGORY — Category System

> Category list, priority levels, fixed WHY options, Phase/Action templates

← [Plan Overview](../1-PLAN.md)

---

## 6.1 Category List

> Fixed 9 categories. Custom category creation is not allowed — maintains the Simplicity principle + ensures AI recommendation quality.

| Category | Icon | Color | Hex |
| -------- | ---- | ----- | --- |
| 운동 | 💪 | Sea Foam | `#5C9E9E` |
| 공부 | 📚 | Amber Sand | `#C9A86C` |
| 취업 | 💼 | Slate Blue | `#5E7B8B` |
| 재테크 | 💰 | Terracotta | `#B87C6B` |
| 관계 | 💕 | Dusty Violet | `#7B6B8D` |
| 취미 | 🎨 | Sage | `#6B9B7A` |
| 습관 | 🧘 | Soft Blue Grey | `#8FA3B0` |
| 독서 | 📖 | Warm Sand | `#A68B5B` |
| 기타 | ✨ | Muted Lavender Grey | `#9B9EB0` |

## 6.2 Priority

| Type | Display | Meaning |
| ---- | ------- | ------- |
| 필수 (Essential) | 🔴 / solid border | Must-do item |
| 선택 (Optional) | ⚪ / dashed border | Nice-to-have item |

## 6.3 Fixed WHY Options (per Category)

> Fixed options displayed during Goal creation Step 3 "왜 ~를 하고 싶으세요?" (Why do you want to do ~?). Shown always in MVP, separate from AI recommendations (P1).

| Category | Fixed WHY Options |
| -------- | ----------------- |
| 💪 운동 | 건강 유지, 체력 향상, 다이어트, 스트레스 해소 |
| 📚 공부 | 성적 향상, 자기계발, 시험 준비, 커리어 성장 |
| 💼 취업 | 경제적 독립, 커리어 성장, 자아실현, 안정적 생활 |
| 💰 재테크 | 경제적 독립, 노후 준비, 목표 자금 마련, 재정 안정 |
| 💕 관계 | 소속감, 정서적 안정, 사회적 성장, 외로움 해소 |
| 🎨 취미 | 스트레스 해소, 자기표현, 성취감, 새로운 경험 |
| 🧘 습관 | 자기관리, 건강 유지, 생산성 향상, 마음의 안정 |
| 📖 독서 | 지식 확장, 사고력 향상, 영감, 휴식 |
| ✨ 기타 | (No fixed options, manual input only) |

> WHY duplication across categories is intentional — it serves as a natural starting point for Cross-Goal connections. When the same WHY is selected across multiple categories, it automatically links to the same WHY node.

## 6.4 Rule-Based Phase/Action Templates

> 3 Phase templates per Category + WHAT combination (each Phase contains 3 Actions + 1 coaching message).
> Detailed templates → [PHASE-TEMPLATES.md](../data/PHASE-TEMPLATES.md)

**Status Mapping Rules:**

| Status | Starting Phase | Phases Generated |
| ------ | -------------- | ---------------- |
| `just_starting` (이제 시작할래요) | Phase 1 | All 3 phases (1, 2, 3) generated |
| `some_experience` (조금 해봤어요) | Phase 2 | Only phases 2 and 3 generated |
| `actively_doing` (열심히 하는 중) | Phase 3 | Only phase 3 generated |

- Fixed WHAT options: Dedicated templates per category (32 total)
- Manual input WHAT / ✨ 기타: Generic template (시작하기 → 실천하기 → 발전하기)
