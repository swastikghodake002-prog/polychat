# PolyChat Product Specification

## 1) Feature List
### Core Learning
- 100% MSBTE syllabus-mapped content by Branch → Semester → Subject → Unit → Topic.
- Unit-wise study notes with marks distribution, learning outcomes, and must-study topics.
- Question bank with 2, 4, 8, 12, 16 marks questions.
- MCQ, theory, and numerical question types.
- Difficulty tagging (Easy/Medium/Hard) and frequency tagging (Low/Medium/High).

### Exam Preparation
- Exam Mode (strict): timer, no hints before submit, random questions by unit weightage.
- Auto evaluation: MCQ auto-check, theory keyword + structure scoring, numericals step-based.
- Mock Test Mode: hints, step-by-step solutions, “Why my answer is wrong,” and “Ideal MSBTE answer.”
- Progress tracking: unit-wise accuracy, streaks, weak-unit detection, revision plan.

### AI Tutor
- Chatbot answers only from MSBTE syllabus data.
- Answer modes: Simple, Exam (point-wise headings), Deep (detailed).
- Always mention subject, unit, and marks.

### Utility
- Offline mode: download notes and question banks.
- Admin panel: upload/edit content, CSV/JSON import, enable/disable questions.
- Multilingual: English, Hindi, Marathi.
- Light/Dark theme.

## 2) User Flows
### Onboarding Flow
1. Select Branch → Semester → Scheme/Pattern → Language.
2. Save preferences → land on Dashboard.

### Study Flow
1. Dashboard → Syllabus Hub → select Subject.
2. View unit breakup, outcomes, marks distribution.
3. Select Unit → Topic → Notes + Questions.
4. Save bookmarks and add to revision list.

### Exam Mode Flow
1. Dashboard → Exam Mode → choose Subject/All Units.
2. Timer starts → answer questions (no hints).
3. Submit → auto evaluation → result analysis → revision plan.

### Mock Test Flow
1. Dashboard → Mock Test → choose Subject/Units.
2. Timed/Untimed → hints allowed.
3. Submit → step-by-step solutions + ideal MSBTE answers.

### AI Tutor Flow
1. Open PolyChat Tutor → select Subject and Unit.
2. Ask question → choose answer mode.
3. Get syllabus-only response with marks/format.

## 3) Database Schema (Relational Example)
### Core Tables
- branches(id, code, name)
- semesters(id, number)
- schemes(id, code, name)
- languages(id, code, name)
- subjects(id, branch_id, semester_id, scheme_id, code, name)
- units(id, subject_id, unit_number, title, marks_weightage)
- topics(id, unit_id, title, is_must_study)
- learning_outcomes(id, unit_id, outcome_text)

### Question Bank
- questions(id, topic_id, marks, type, difficulty, frequency, question_text, is_active)
- answers(id, question_id, definition, explanation_points, diagram_steps, advantages, disadvantages, applications, conclusion)
- mcq_options(id, question_id, option_label, option_text, is_correct)
- numerical_steps(id, question_id, step_no, step_text)
- keywords(id, question_id, keyword_text, weight)

### Users & Progress
- users(id, name, email, branch_id, semester_id, scheme_id, language_id)
- user_preferences(id, user_id, theme, offline_enabled)
- user_progress(id, user_id, subject_id, unit_id, accuracy, attempts, last_attempted_at)
- user_streaks(id, user_id, streak_count, last_active_at)
- revision_items(id, user_id, topic_id, priority, next_review_at)

### Tests
- tests(id, user_id, type, subject_id, total_marks, duration_minutes, created_at)
- test_questions(id, test_id, question_id, marks)
- test_answers(id, test_id, question_id, user_answer, score, feedback)

## 4) API Endpoints (REST)
### Onboarding
- POST /api/v1/users
- PUT /api/v1/users/{id}/preferences
- GET /api/v1/branches
- GET /api/v1/branches/{id}/semesters
- GET /api/v1/schemes
- GET /api/v1/languages

### Syllabus Hub
- GET /api/v1/subjects?branch={id}&semester={id}&scheme={id}
- GET /api/v1/subjects/{id}
- GET /api/v1/subjects/{id}/units
- GET /api/v1/units/{id}/topics
- GET /api/v1/units/{id}/learning-outcomes

### Question Bank
- GET /api/v1/topics/{id}/questions?marks=&type=&difficulty=
- GET /api/v1/questions/{id}
- POST /api/v1/questions/search

### AI Tutor
- POST /api/v1/tutor/answer
  - Request: { subject_id, unit_id, question_text, mode }
  - Response: { subject, unit, marks, answer_format, answer_content }

### Tests
- POST /api/v1/tests (create exam/mock)
- GET /api/v1/tests/{id}
- POST /api/v1/tests/{id}/submit
- GET /api/v1/tests/{id}/analysis

### Admin
- POST /api/v1/admin/import/csv
- POST /api/v1/admin/import/json
- PUT /api/v1/admin/questions/{id}
- PUT /api/v1/admin/questions/{id}/status

## 5) UI Screens
- Onboarding (Branch/Semester/Scheme/Language)
- Dashboard (quick actions: Syllabus, Exam, Mock, Tutor)
- Syllabus Hub (subject list + unit cards)
- Unit Details (marks distribution, outcomes, must-study)
- Topic View (notes + questions)
- Question View (answer formats + model answers)
- Exam Mode (timer + no hints UI)
- Mock Test Mode (hints + solutions)
- Results & Analysis (weak units + revision plan)
- Progress & Streaks
- Offline Downloads
- Admin Panel (content upload + tagging)
- Settings (theme, language, preferences)

## 6) Exam Evaluation Logic (Strict)
### MCQ
- Auto-check by exact match to correct option.
- Score per question = marks if correct else 0.

### Theory
- Structure score (30%): headings + point-wise format.
- Keyword score (50%): matched syllabus keywords with weights.
- Completeness score (20%): includes definition, explanation, applications.
- Total score scaled to question marks.

### Numerical
- Step scoring: each step mapped to expected formula/result.
- Partial credit if method correct but arithmetic error.
- Final answer must match tolerance range (if numeric).

## 7) Mock Test Logic
- Question selection respects unit weightage but allows hints.
- Hints: show keywords or first step after request.
- Solutions: step-by-step with final ideal MSBTE answer.
- Feedback: “Why wrong” lists missing keywords and structure gaps.

## 8) Sample MSBTE Model Answer (Format Only)
**Subject:** [Subject Name]  
**Unit:** [Unit Number]  
**Marks:** [8 Marks]

**Q.** Define [Topic] and explain its applications.

**Answer (MSBTE Format):**
- **Definition:**
  - [Point-wise definition in simple language.]
- **Explanation:**
  1. [Point 1]
  2. [Point 2]
  3. [Point 3]
- **Diagram (Steps in text):**
  1. [Step 1]
  2. [Step 2]
- **Applications:**
  - [Application 1]
  - [Application 2]
- **Conclusion:**
  - [One-line conclusion.]

**Note:** If information is missing, respond: “Not available in syllabus database.”

## 9) Admin CSV Format
### Subjects CSV
```
branch_code,semester,scheme_code,subject_code,subject_name
CO,3,2019,22321,Data Structures
```

### Units CSV
```
subject_code,unit_number,unit_title,marks_weightage
22321,1,Introduction to Data Structures,16
```

### Topics CSV
```
subject_code,unit_number,topic_title,is_must_study
22321,1,Arrays,TRUE
```

### Questions CSV
```
subject_code,unit_number,topic_title,marks,type,difficulty,frequency,question_text
22321,1,Arrays,8,Theory,Medium,High,Define array and list its applications.
```

### Answers CSV
```
subject_code,unit_number,topic_title,question_text,definition,explanation_points,diagram_steps,advantages,disadvantages,applications,conclusion
22321,1,Arrays,Define array and list its applications.,"Array is...","1) ...; 2) ...","1) ...; 2) ...","1) ...","1) ...","1) ...","..."
```
