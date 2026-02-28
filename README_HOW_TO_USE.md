# VIVA & PRESENTATION PREPARATION - COMPLETE GUIDE
## How to Use These Documents

---

## 📚 THREE DOCUMENTS CREATED FOR YOU

### 1. **VIVA_PREPARATION_4PHASES.md** (55KB, 1200+ lines)
**Purpose**: Complete technical deep-dive on all 4 phases  
**Use When**: You want to understand the "WHY" and "HOW" of each phase at expert level

**Contents**:
- Executive problem statement
- **Phase 1**: Data sanitation—why clean, what problems, how solved, viva Q&A
- **Phase 2**: Web scraping—purpose, implementation details, ethical practices, edge cases
- **Phase 3**: EDA—7 different analyses (age, admission type, medication, comorbidity, etc.), interpretation
- **Phase 4**: VCI algorithm—LACE foundation, component scoring, validation, business utility
- How all 4 phases connect
- Presentation & viva mastery checklist
- Sample Q&A bank (80+ questions answered in detail)

**Study Time**: 2-3 hours for deep mastery

**Key Sections**:
- Spend time on Phase 3 (hardest to explain)—confounding, rate vs. count, interpretation
- Phase 4 VCI: Master the scoring logic cold (L, A, C, E breakpoints)
- Final checklist: Go through before your viva

---

### 2. **QUICK_REFERENCE_4PHASES.md** (22KB, 500 lines)
**Purpose**: Fast reference guide for day-of-viva  
**Use When**: Right before your viva; need quick mental refresher

**Contents**:
- Business context (1 page)
- Each phase summarized in one page:
  - What you're solving
  - Problems & solutions (table format)
  - Why it matters
  - Viva talking points (3-4 key sentences)
  - Code pattern (key code snippet)
- 4-phase pipeline diagram
- Common mistakes to avoid
- Presentation structure (for 15-minute presentation breakdown)
- References to cite

**Study Time**: 30 minutes to 1 hour

**Use Strategy**:
- Read through once for context
- The morning of viva: Review the "Viva Talking Points" section (5 min read)
- Keep this open during presentation for quick reference

---

### 3. **POTENTIAL_VIVA_QUESTIONS.md** (44KB, 970 lines)
**Purpose**: 80+ potential exam questions with full answers  
**Use When**: Practice answering questions; anticipate what examiners might ask

**Contents**:
- **Phase 1**: 10 questions (Basic, Intermediate, Advanced)
- **Phase 2**: 12 questions (Basic, Intermediate, Advanced)
- **Phase 3**: 13 questions (Basic, Intermediate, Advanced)
- **Phase 4**: 14 questions (Basic, Intermediate, Advanced)
- **Business Questions**: 12 questions (Basic, Intermediate, Advanced)
- **Code Questions**: 12 questions (Basic, Intermediate, Advanced)
- **Edge Cases**: 5 questions
- **Communication**: 3 questions
- **Catch-all**: 5 questions

**Study Time**: 3-4 hours to read all; 5-6 hours to fully internalize

**Use Strategy**:
- Pick 5-10 questions per phase; practice answering out loud
- Focus on **Advanced** questions (harder questions mean you're ready for anything)
- Read answers even for questions you think you can answer (answers provide better phrasing)

---

## 🎯 RECOMMENDED STUDY SCHEDULE

### **Week 1: Deep Understanding (2-3 hours/day)**
- **Day 1**: Read VIVA_PREPARATION_4PHASES sections on Phase 1 & 2 (1-2 hours)
- **Day 2**: Read VIVA_PREPARATION_4PHASES sections on Phase 3 (1-2 hours, dense section)
- **Day 3**: Read VIVA_PREPARATION_4PHASES sections on Phase 4 (1-2 hours)
- **Day 4**: Read synthesis section + presentation checklist (1 hour)

### **Week 2: Practice & Refinement (1-2 hours/day)**
- **Day 1**: Answer 5 Basic questions from POTENTIAL_VIVA_QUESTIONS (30 min)
- **Day 2**: Answer 5 Intermediate questions (45 min; harder)
- **Day 3**: Answer 5 Advanced questions (1 hour; hardest)
- **Day 4**: Answer Business & Code questions (1 hour)
- **Day 5**: Pick the 3 hardest questions; practice answering out loud (30 min)

### **Day Before Viva: Quick Review (30 minutes)**
- Skim QUICK_REFERENCE_4PHASES once more
- Review "Common Mistakes to Avoid"
- Read 3-5 Advanced questions you found hardest
- Sleep well!

### **Morning of Viva: Mental Prep (15 minutes)**
- Read the "Viva Talking Points" section of QUICK_REFERENCE_4PHASES
- Review the elevator pitch
- Take 3 deep breaths
- You've got this! 🚀

---

## 🔍 QUICK LOOKUP BY TOPIC

### **Need to understand Phase 1 quickly?**
1. QUICK_REFERENCE → "PHASE 1: DATA SANITATION" (5 min read)
2. VIVA_PREPARATION → "PHASE 1" section (20 min read)
3. POTENTIAL_VIVA_QUESTIONS → Questions Q1.1-Q1.13 (practice)

### **Need to explain VCI?**
1. QUICK_REFERENCE → "PHASE 4: VCI" (10 min read)
2. VIVA_PREPARATION → "PHASE 4" section (30 min read)
3. POTENTIAL_VIVA_QUESTIONS → Questions Q4.1-Q4.14 (practice)

### **Need to answer a specific type of question?**
- **"Explain Phase 1"** → VIVA_PREPARATION Q1.1-Q1.13
- **"Why did you scrape?"** → QUICK_REFERENCE "PHASE 2" + VIVA_PREPARATION Q2.1-Q2.12
- **"How does EDA connect to VCI?"** → VIVA_PREPARATION "SYNTHESIS" section
- **"What's the business case?"** → QUICK_REFERENCE "THE BUSINESS CONTEXT" + POTENTIAL_VIVA_QUESTIONS Q5.1-Q5.12
- **"Code this function"** → POTENTIAL_VIVA_QUESTIONS Q6.1-Q6.12

---

## 🎤 PRACTICE VIVA SIMULATION

### **With a Study Buddy (Recommended)**
1. Have your buddy ask you 3 random questions from POTENTIAL_VIVA_QUESTIONS
2. Answer without looking at notes (30 seconds thinking time allowed)
3. They give feedback on clarity, correctness, depth
4. Repeat 3-5 times with different questions
5. Progressively increase difficulty (Basic → Intermediate → Advanced)

### **Solo Practice**
1. Pick a question from POTENTIAL_VIVA_QUESTIONS
2. Set 2-minute timer
3. Answer out loud (as if in real viva)
4. Compare to provided answer
5. Note gaps in your explanation
6. Repeat 10-15 times

---

## ⚠️ COMMON PITFALLS TO AVOID

These are highlighted in QUICK_REFERENCE, but key ones:

### **Phase 1 Mistakes**
❌ "I dropped weight because it was missing."
✓ "Weight was 95% missing. Imputing fabricates data. Dropping was methodologically sound."

❌ "I removed dead patients because they had NaN."
✓ "Readmission is undefined for deceased. Removing ensures analysis is logically valid."

### **Phase 2 Mistakes**
❌ "I scraped as fast as possible to save time."
✓ "I included delays (time.sleep) between requests—ethical practice, avoids IP blocking."

❌ "If scraping failed, I just skipped the code."
✓ "I handled errors gracefully with try-except and documented which codes failed."

### **Phase 3 Mistakes**
❌ "Insulin users have higher readmission, so insulin causes readmission."
✓ "Insulin users readmit more because they're sicker (confounding). Insulin is a marker of severity."

❌ "2% of young patients readmitted; 32% of elderly readmitted."
✓ "Young patients: 2% rate. Elderly: 32% rate. This is the correct rate comparison."

### **Phase 4 Mistakes**
❌ "I chose the VCI cutoffs arbitrarily."
✓ "Cutoffs (7, 10) are based on LACE literature. Empirically validated: high-risk has 5.6x readmission."

❌ "I could use ML instead of VCI."
✓ "Could use ML (more accurate) but VCI is better here: interpretable, simple, deployable. Doctors trust it."

---

## 📊 WHAT EXAMINERS CARE ABOUT

### **They Will Test:**
1. **Deep understanding** (not just following steps)
   - "WHY did you drop this column, not impute?"
   - "What would happen if you didn't clean data?"
   - Test: VIVA_PREPARATION deep explanations

2. **Ability to code** (they might ask you to code a function)
   - "Write the L_Score function from scratch"
   - Test: POTENTIAL_VIVA_QUESTIONS Q6.1-Q6.12

3. **Business sense** (not just technical work)
   - "How would VHN operationalize VCI?"
   - "What's the ROI?"
   - Test: POTENTIAL_VIVA_QUESTIONS Q5.1-Q5.12

4. **Methodology** (statistical rigor)
   - "How did you avoid confounding?"
   - "How would you validate on new data?"
   - Test: VIVA_PREPARATION methodology sections + POTENTIAL_VIVA_QUESTIONS Advanced

5. **Communication** (can you explain clearly?)
   - Present findings simply to non-technical audience
   - Admit limitations honestly
   - Test: QUICK_REFERENCE presentation structure + practice out loud

### **They Won't Test:**
- Memorization of exact code
- Exact numbers from your dataset (they don't care if readmission was 11.2% or 11.3%)
- Trivia about Python libraries

---

## 🚀 YOUR VIVA OPENING (Practice This)

**Examiners ask**: "Tell us about your project."

**Your 2-minute response** (use QUICK_REFERENCE as template):

"Vitality Health Network faced an 18% readmission rate for diabetic patients, exceeding benchmarks and triggering millions in CMS penalties. I built a four-phase data science pipeline to address this.

**Phase 1: Data Sanitation** — The dataset was messy: non-standard missing values (`?`), unmapped numeric codes, and deceased patients. I cleaned it by standardizing missing values, mapping IDs to human-readable labels, and removing deceased patients (readmission undefined for them). Result: 40K analyzable patients.

**Phase 2: Web Scraping** — ICD-9 diagnosis codes (250, 428, 414) were meaningless to stakeholders. I used BeautifulSoup and Requests to scrape icd9.chrisendres.com, fetching disease descriptions for the top 50 codes. This enabled meaningful comorbidity analysis.

**Phase 3: Exploratory Analysis** — I discovered what drives readmission: age (elderly 5x higher risk), admission type (emergency 3x higher), comorbidities (8+ diagnoses 3x higher), and prior ED visits (frequent users 4x higher). These findings informed Phase 4.

**Phase 4: Risk Scoring** — I built the Vitality Complexity Index (VCI) using the LACE framework: Length of stay, Acuity, Comorbidity, Emergency visits. Patients score 0-20. I stratified into three risk categories: Low (VCI<7, 5% readmission), Medium (7-10, 15%), High (>10, 28%). High-risk patients are 5.6x more likely to readmit, validating the tool.

**Impact**: VCI is a simple, evidence-based risk stratification tool. Nurses can calculate it in 2 minutes and use it to triage discharge planning intensity. Implementing intensive follow-up for high-risk patients could prevent 100+ readmissions/year, saving $300-500K in costs.

Questions?"

**Time**: ~2 min. Clear structure (4 phases + impact). Shows technical depth + business sense.

---

## ✅ FINAL CHECKLIST BEFORE VIVA

- [ ] **Phase 1 Understanding**: Can explain why each data cleaning step was needed?
- [ ] **Phase 2 Scraping**: Can describe the scraping logic and error handling?
- [ ] **Phase 3 EDA**: Can explain 5+ insights and avoid confounding fallacy?
- [ ] **Phase 4 VCI**: Can code the four component functions from scratch?
- [ ] **Validation**: Can explain how you validated VCI works?
- [ ] **Business Case**: Can articulate ROI and operationalization?
- [ ] **Code Quality**: Can discuss code patterns, vectorization, best practices?
- [ ] **Limitations**: Can honestly discuss dataset age, simplifications made?
- [ ] **Communication**: Can present findings clearly to non-technical audience?
- [ ] **Practice**: Answered at least 20 questions out loud?

---

## 💡 FINAL TIPS

1. **Slow down when nervous** - Speak clearly, not fast. Better to pause than stumble.
2. **Show your working** - "Let me think through this... [explain reasoning]" is better than silence.
3. **Admit if you don't know** - "I didn't consider that angle; here's how I'd approach it..." beats making something up.
4. **Connect to business** - Every technical decision should link to "Why does VHN care?"
5. **Use the documents strategically**:
   - QUICK_REFERENCE = morning review + during viva (for memory jog)
   - VIVA_PREPARATION = deep study sessions
   - POTENTIAL_VIVA_QUESTIONS = practice + identify weak areas

6. **Your greatest strength**: You understand this project deeply. The examiners know you did the work. They're testing comprehension, not gotcha questions. You've prepared well.

---

## 🎓 YOU'VE GOT THIS!

You have:
✓ 1,200+ lines of detailed technical explanation  
✓ 500 lines of quick reference  
✓ 80+ practice questions with full answers  
✓ Business and code context  
✓ Common pitfalls documented  
✓ Presentation structure outlined  

**Time to shine.** Go into that viva with confidence. You understand this project better than 95% of students could. Examiners will ask hard questions—answer them thoughtfully, admit limitations, and show your working. You'll pass with flying colors.

All the best! 🚀

---

**Created**: February 28, 2026  
**For**: Strategic Patient Risk Stratification Project  
**Status**: Ready for Viva & Presentation

