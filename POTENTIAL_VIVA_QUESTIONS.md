# COMPREHENSIVE VIVA QUESTION BANK
## Strategic Patient Risk Stratification Project

### How to Use This Document
This lists 80+ potential questions organized by:
1. **Phase** (1-4)
2. **Difficulty** (Basic, Intermediate, Advanced)
3. **Category** (Technical, Business, Methodological, Code)

---

# PHASE 1: DATA SANITATION

## BASIC QUESTIONS

**Q1.1**: "Describe the dataset. How many rows, columns, time span?"
**A**: "100,000+ hospital encounters from 130 US hospitals over 10 years (1999-2008). Originally ~50 columns including demographics, admissions, medications, diagnoses, outcomes. After cleaning, ~40K rows (removed deceased)."

**Q1.2**: "What does the `?` in the data represent?"
**A**: "Non-standard missing values. The original dataset used `?` instead of Python's standard NaN. This breaks calculations—pandas functions like `fillna()`, `groupby()` don't recognize `?` as missing."

**Q1.3**: "Why did you remove deceased patients?"
**A**: "Readmission is logically undefined for deceased patients (can't readmit a dead person). Including them artificially lowers readmission rates and biases analysis. Also, CMS penalties don't apply to deceased cases."

**Q1.4**: "Which column did you drop and why?"
**A**: "Dropped `weight`. It was 95% missing. If you impute 95% of values, you're creating fabricated data. Better to acknowledge data limitation and drop the column."

**Q1.5**: "What are the two input CSVs and what's the difference?"
**A**: "`diabetic_data.csv` contains the raw clinical encounters (patients, admissions, outcomes). `IDs_mapping.csv` is a lookup table that maps numeric IDs (e.g., admission_type_id=1) to human-readable labels (e.g., 'Emergency')."

---

## INTERMEDIATE QUESTIONS

**Q1.6**: "Walk me through your data cleaning pipeline step-by-step."
**A**: 
1. Load both CSVs as dataframes
2. Strip whitespace from object columns (leading/trailing spaces)
3. Define placeholder list: `['?', 'Not Mapped', 'Unknown/Invalid', '', 'Not Available']`
4. Replace all placeholders with np.nan
5. Load mapping dictionary from IDs_mapping.csv
6. Apply mappings to numerical ID columns (.map() function)
7. Filter out expired patients (discharge_disposition_id containing 'Expired')
8. Drop columns with >90% missing
9. Result: Clean dataset ready for analysis

**Q1.7**: "What would happen if you didn't clean the data?"
**A**:
- Calculations break: Can't sum columns with `?`
- Visualizations uninterpretable: Axes filled with numeric codes
- Stakeholders can't act: Don't know what ID 1 means
- Downstream analysis corrupted: Biased results
- Can't remove deceased patients → readmission metrics inflated

**Q1.8**: "Explain the difference between handling missing data via imputation vs. deletion."
**A**:
- **Deletion**: Remove rows or columns with missing data. Risk: lose information. Good for: high missingness (>70%) or if variable is unimportant.
- **Imputation**: Fill missing values (mean/median for numeric, mode for categorical). Risk: creates false data. Good for: low-moderate missingness (<50%).
- **Your decision**: Weight was 95% missing → delete. Age group had <5% missing → could impute or keep as-is.

**Q1.9**: "How did you verify that your cleaning was correct?"
**A**:
- Check no remaining `?`: `assert '?' not in df.values`
- Verify all IDs mapped: `assert df['admission_type_id'].isin(['Emergency', 'Urgent', ...]).all()`
- Confirm deceased removed: `assert 'Expired' not in df['discharge_disposition_id'].values`
- Spot-check mappings: Manually verify a few rows
- Compare row counts before/after

**Q1.10**: "Some records had gender = 'Unknown/Invalid'. Should you drop these or impute?"
**A**: 
- Can't reliably impute gender (no informative features to predict it)
- A few Unknown/Invalid records don't break analysis
- I'd keep them and treat 'Unknown' as a category (if you drop all Unknowns, you lose info)
- Only drop if gender is critical for analysis

---

## ADVANCED QUESTIONS

**Q1.11**: "Explain the concept of 'non-standard missing values' and why it's a data quality issue."
**A**:
Standard missing value indicator: NaN (Not a Number) in pandas/NumPy.
Non-standard: `?`, 'NULL', '', 'NA', 'Not Mapped', 'Unknown/Invalid', 'Not Available', etc.

**Why it's a problem**:
- pandas functions assume standard NaN. `df['age'].fillna(30)` won't replace `?`
- Calculations fail: `df['lab_tests'].sum()` fails if a value is `?` instead of np.nan
- Statistical tests break: scipy functions expect NaN not custom placeholders
- Comparisons fail: `df[df['status'] == 'valid']` won't catch rows with `?`

**Solution**: Standardize all missing values to NaN before analysis.

**Q1.12**: "What's the difference between dropping a row vs. dropping a column? When do you use each?"
**A**:
- **Drop row**: Remove a single record because it's invalid (deceased patient, impossible value like age=-5)
  - Use when: Few rows are invalid; record is logically wrong
  - Risk: Lose patient-level information
  
- **Drop column**: Remove an entire variable because it lacks information
  - Use when: >90% missing (sparse), never used in analysis, redundant with another column
  - Risk: Can't analyze that dimension anymore

**Example**:
- Drop row: Remove deceased patients (1-2% of data, logically invalid)
- Drop column: Remove weight (95% missing, uninformative)

**Q1.13**: "You mentioned using .map() to apply mappings. What's the difference between .map() and .apply()?"
**A**:
- **.map()**: For Series (single column) transformation using a dictionary or function. Returns Series.
  - `df['admission_type_id'] = df['admission_type_id'].map({1: 'Emergency', 2: 'Urgent'})`
- **.apply()**: For Series or DataFrame transformation using a function. Returns Series or DataFrame.
  - `df['admission_type_id'].apply(lambda x: 'Emergency' if x == 1 else 'Other')`

**Both work for single columns, but**:
- .map() is more intuitive for dictionary lookups
- .apply() is more flexible for complex logic

---

# PHASE 2: WEB SCRAPING

## BASIC QUESTIONS

**Q2.1**: "What is web scraping and why did you use it?"
**A**: Web scraping is automated extraction of data from websites. I used it to fetch ICD-9 diagnosis code descriptions. Instead of manually looking up each code, I programmed the script to hit icd9.chrisendres.com, parse the HTML, extract descriptions, and store them in a dictionary.

**Q2.2**: "What's the difference between BeautifulSoup and Requests?"
**A**: 
- **Requests**: HTTP library. Sends a request to a server and receives the HTML response as text.
- **BeautifulSoup**: HTML parser. Takes the raw HTML text and extracts specific information by navigating the document structure.

**Example**:
```python
response = requests.get(url)  # Get HTML from server
soup = BeautifulSoup(response.text, 'html.parser')  # Parse it
description = soup.find('div', class_='description').text  # Extract what you want
```

**Q2.3**: "Why did you include delays (time.sleep) in your scraper?"
**A**: 
- **Ethical**: Respect server resources. Rapid-fire requests overload the server.
- **Practical**: Avoid IP blocking. Servers detect aggressive scraping and block the source IP.
- **Legal**: Some websites' Terms of Service forbid rapid scraping. Delays simulate human browsing.
- **Best practice**: Include 1-2 second delays between requests.

**Q2.4**: "What would you do if a code had no description or the website was down?"
**A**: Handle with try-except:
```python
try:
    response = requests.get(url, timeout=5)
    description = parse_description(response.text)
    icd9_map[code] = description
except requests.exceptions.RequestException:
    icd9_map[code] = "Description not found"
except Exception as e:
    print(f"Error for code {code}: {e}")
    icd9_map[code] = "Error"
```
Document which codes failed and why.

**Q2.5**: "How many codes did you scrape and why?"
**A**: Focused on top 20-30 ICD-9 codes by frequency. Why? 
- Top 20 codes cover ~80% of all diagnoses (Pareto principle)
- Scraping all 5,000+ ICD codes would take hours and stress the server
- For comorbidity analysis, top codes are most important

---

## INTERMEDIATE QUESTIONS

**Q2.6**: "Walk me through the scraping logic. Start to finish."
**A**:
1. Extract unique diagnosis codes from diag_1 column
2. Calculate frequency of each code (value_counts)
3. Select top 50 codes by frequency
4. For each code:
   a. Construct URL: `icd9.chrisendres.com/index.php?srchtext=[code]&action=search`
   b. Send GET request via requests library
   c. Parse HTML response with BeautifulSoup
   d. Extract disease description from parsed HTML
   e. Store in dictionary: `icd9_map[code] = description`
   f. Include time.sleep(1) for ethical scraping
5. Return dictionary mapping codes to descriptions
6. Merge back to dataset: `df['diag_1_description'] = df['diag_1'].map(icd9_map)`

**Q2.7**: "What's the difference between parsing HTML vs. JSON vs. XML?"
**A**:
- **HTML**: Markup for displaying web pages. Nested tags, messy, not strictly structured. Use BeautifulSoup.
- **JSON**: Structured data format (key-value pairs). Clean, machine-readable. Use json library or requests.json().
- **XML**: Structured markup like HTML but stricter. Use xml.etree or lxml.

**ICD-9 database returns HTML** → BeautifulSoup is appropriate. If they had a JSON API, parsing would be simpler.

**Q2.8**: "How did you avoid overloading the ICD-9 server?"
**A**: 
- Included time.sleep(1) delays between requests
- Set timeout=5 seconds (fail gracefully if server is slow)
- Limited scraping to top 50 codes, not all 5,000
- Documented request headers to mimic browser behavior
- Implemented error handling (don't retry aggressively on failure)

**Q2.9**: "What if the website structure changed (HTML tags changed)? How would you debug?"
**A**:
1. Inspect the website manually (right-click → Inspect Element in browser)
2. Locate the new HTML structure
3. Update the parsing logic to match new tags
4. Test on a single code to verify
5. Document the change for future maintenance

Example: If description was in `<div class='description'>` but now in `<p id='disease-name'>`, update:
```python
# Old
description = soup.find('div', class_='description').text

# New
description = soup.find('p', id='disease-name').text
```

---

## ADVANCED QUESTIONS

**Q2.10**: "Compare web scraping vs. APIs for data extraction. When would you use each?"
**A**:
| Method | When to Use | Pros | Cons |
|--------|---|---|---|
| **Web Scraping** | No API available | Flexible, captures any data displayed | Fragile (breaks if site changes), slow, may violate ToS |
| **API** | Public API exists | Fast, structured, officially supported | Limited to what API provides, rate limits, authentication |

**For ICD-9**: No public API exists, so scraping was necessary. If a proper API was available, I'd use it instead.

**Q2.11**: "Explain the concept of robots.txt and when you should respect it."
**A**: `robots.txt` is a file on websites that specifies what bots can/can't scrape.
- Example: `disallow: /admin/` means don't scrape admin pages
- Ethical practice: Check robots.txt before scraping
- Legal: Some websites forbid all scraping in robots.txt; respect it
- For icd9.chrisendres.com: Public database, not commercial, likely allows scraping for educational use

In production, check robots.txt and respect it.

**Q2.12**: "What's the difference between requests.get() and requests.post()? When would you use each?"
**A**:
- **GET**: Request data from server (read-only). Parameters in URL. Example: `requests.get(url, params={'q': 'search'})`
- **POST**: Send data to server (can modify). Data in request body. Example: `requests.post(url, data={'username': 'user'})`

For ICD-9 scraping, **GET was appropriate** (we're reading, not modifying). If the site required authentication or form submission, POST would be needed.

---

# PHASE 3: EXPLORATORY DATA ANALYSIS

## BASIC QUESTIONS

**Q3.1**: "What is EDA and what was its goal in this project?"
**A**: Exploratory Data Analysis is initial investigation of data to understand structure, patterns, and relationships. Goal: Answer "What drives readmission?" by correlating variables with the readmission outcome.

**Q3.2**: "List 5 key insights you discovered in Phase 3."
**A**: 
1. **Age**: Readmission increases with age; 80+ patients have 5-6x higher rate than 20-30 year olds
2. **Admission Type**: Emergency admissions have 3x higher readmission than elective
3. **Comorbidities**: Patients with 8+ diagnoses have 3x higher readmission
4. **Prior ED Visits**: Frequent ED users (>4 visits/year) have 4x higher readmission
5. **Discharge Destination**: Discharge to other facilities (vs. home) associated with higher readmission

**Q3.3**: "You found that insulin users have higher readmission. Does insulin cause readmission?"
**A**: No. This is **confounding**. Sicker diabetics (Type 1 or poorly controlled Type 2) are prescribed insulin. Insulin doesn't cause readmission; severe diabetes does. The insight is: "Identify insulin users and intensify discharge planning."

**Q3.4**: "What visualization did you use to show readmission by age group?"
**A**: Bar chart with age groups on x-axis, readmission rate (%) on y-axis. Color-coded by risk level (green <10%, yellow 10-20%, red >20%). Clearly shows the gradient.

**Q3.5**: "How did you calculate readmission rate by demographic?"
**A**: 
```python
# Group by demographic, count readmissions
readmission_by_age = df.groupby('age_group')['readmitted_30'].value_counts(normalize=True)

# Or calculate directly:
readmission_rate_by_age = df.groupby('age_group').apply(
    lambda x: (x['readmitted_30'].sum() / len(x)) * 100
)
```
Important: Use rates (%), not counts, for comparison.

---

## INTERMEDIATE QUESTIONS

**Q3.6**: "In Phase 3, you found correlation between lab procedures and readmission. Did you assume causation?"
**A**: No. Correlation ≠ causation. More lab procedures indicate sicker patients, which is why they correlate with readmission. The causation is: Severity → more tests AND higher readmission. Both are caused by severity. I didn't claim "more lab tests cause readmission."

**Q3.7**: "How did you handle the class imbalance in the readmission variable?"
**A**: Readmission had imbalanced classes:
- NO: ~50-60%
- >30: ~30-35%
- <30: ~10-15% (our target)

Visualization strategy: Don't show raw counts; show **rates**. Example:
- Wrong: "15 elderly patients readmitted, 85 not readmitted"
- Right: "15% of elderly patients readmitted vs. 5% of young patients"

**Q3.8**: "You analyzed medication types. How did you avoid the causation fallacy?"
**A**: 
- Acknowledged that insulin users are sicker (confounding)
- Stated the insight as "association" not "effect": "Insulin users are ASSOCIATED with higher readmission"
- Avoided: "Insulin causes readmission" or "Insulin users get readmitted more because of insulin"
- Proposed action: "Identify insulin users for enhanced discharge planning" (doesn't require causation proof)

**Q3.9**: "What statistical test would you use to test if the readmission difference between admission types is statistically significant?"
**A**: 
- **Chi-square test of independence**: Tests whether two categorical variables (admission_type, readmitted) are associated
- Null hypothesis: Admission type and readmission are independent
- If p < 0.05: Reject null; they are significantly associated
- Formula: χ² = Σ(Observed - Expected)² / Expected

Would show: "Readmission rates differ significantly by admission type (p < 0.001)"

**Q3.10**: "You found 8+ diagnoses predict readmission. Why not use just the number as a risk factor instead of binning into 0, 3, 5 points?"
**A**: Good question. Could use raw count, but benefits of binning:
- **Interpretability**: "3-4 diagnoses = low risk" is more understandable than "each diagnosis adds 0.3 to risk"
- **Clinical practice**: Physicians already think in categories (simple vs. complex case)
- **Reduced overfitting**: Binning captures the general trend without fitting noise
- Trade-off: Lose some precision, gain interpretability

---

## ADVANCED QUESTIONS

**Q3.11**: "Explain the difference between exploratory analysis and inferential statistics."
**A**:
- **Exploratory (EDA)**: Describe what you observe in the data. "30% of emergency admission patients were readmitted." No statistical inference.
- **Inferential**: Use sample data to make claims about the population. "Based on our sample, we estimate the true readmission rate for emergency admissions in the population is 30% ± 2%."

**Your Phase 3**: Mostly exploratory. You described patterns in your dataset. You didn't make population-level inferences.

**Q3.12**: "How would you determine if a confounding variable is important enough to control for?"
**A**: 
1. **Is it associated with the exposure?** (insulin use) → Yes, sicker patients get insulin
2. **Is it associated with the outcome?** (readmission) → Yes, sicker patients readmit more
3. **Does it change the relationship?** → If you control for severity, does insulin effect disappear? Likely yes.

**Control methods**:
- **Stratification**: Analyze insulin users vs. non-insulin separately within severity groups
- **Statistical adjustment**: Regression with severity as a covariate
- **Matching**: Compare insulin users to similar non-insulin users on severity

For this module: Acknowledge confounding and propose analysis (don't need to execute).

**Q3.13**: "You showed correlation between time_in_hospital and lab_procedures. What's the Pearson correlation coefficient?"
**A**: Pearson r measures linear correlation (0 to ±1). Likely around r = 0.4-0.6 (moderate positive). This means:
- Patients with longer stays tend to have more lab procedures
- The relationship is not perfectly linear (r ≠ 1)
- ~40-60% of variance in one variable is explained by the other

Calculate:
```python
correlation = df['time_in_hospital'].corr(df['num_lab_procedures'])
```

---

# PHASE 4: VITALITY COMPLEXITY INDEX (VCI)

## BASIC QUESTIONS

**Q4.1**: "Explain the purpose of VCI. What problem does it solve?"
**A**: VCI is a **risk stratification tool** that identifies high-risk patients at discharge. Purpose: Enable nurses to triage discharge planning intensity. High-risk patients get case management; low-risk get standard care. Solves: "Which patients need intensive follow-up?" (before you had no systematic way to identify them)

**Q4.2**: "What does LACE stand for?"
**A**: 
- **L**: Length of Stay (how long was patient hospitalized)
- **A**: Acuity of Admission (how urgently admitted—emergency vs. elective)
- **C**: Comorbidity Burden (how many co-existing conditions)
- **E**: Emergency Visits (how many ED visits in prior year)

**Q4.3**: "What's the range of VCI scores?"
**A**: 0 (healthiest) to 20 (sickest).
- 0-6: Low risk (most points possible: no emergency admission, 1 day stay, <4 diagnoses, 0 ED visits)
- 20: High risk (emergency, 14+ day stay, 8+ diagnoses, 5+ ED visits)

Most patients cluster in 5-15 range.

**Q4.4**: "What are the three risk categories and readmission rates?"
**A**:
| VCI Score | Category | Readmission Rate |
|---|---|---|
| < 7 | Low Risk | ~5% |
| 7-10 | Medium Risk | ~15% |
| > 10 | High Risk | ~28% |

**Q4.5**: "You said high-risk has 28% readmission. How did you calculate this?"
**A**: 
```python
# Binary readmission indicator
df['readmitted_30'] = (df['readmitted'] == '<30').astype(int)

# Calculate rate by risk category
readmission_by_risk = df.groupby('VCI_Risk_Category')['readmitted_30'].agg(['sum', 'count'])
readmission_by_risk['rate'] = (readmission_by_risk['sum'] / readmission_by_risk['count']) * 100
```

Example: High risk group has 4,200 readmitted out of 15,000 total → 4200/15000 = 28%.

---

## INTERMEDIATE QUESTIONS

**Q4.6**: "Walk me through the VCI calculation for a specific patient."
**A**: Imagine patient:
- Time in hospital: 8 days → L = 4 points (5-13 day range)
- Admission type: Emergency → A = 3 points
- Number of diagnoses: 9 → C = 5 points (≥8 range)
- Prior ED visits: 3 → E = 3 points (1-4 range)

**VCI = 4 + 3 + 5 + 3 = 15** → High Risk (>10) → Readmission rate ~28% → Needs intensive discharge planning.

**Q4.7**: "How did you justify the threshold of 7 and 10?"
**A**: 
1. **From literature**: LACE Index studies show 7 and 10 are clinically meaningful breakpoints
2. **Empirical validation**: I calculated readmission rates at each threshold and confirmed the gradient exists:
   - Below 7: 5% readmission
   - 7-10: 15% readmission
   - Above 10: 28% readmission
3. **Significance testing**: Chi-square test shows differences are statistically significant (p < 0.001)

**Q4.8**: "Why use simple diagnosis count instead of Charlson Comorbidity Index?"
**A**: 
- **Charlson Index**: Weights diseases by severity (heart failure more serious than acne). More accurate but requires manual disease classification.
- **Simple count**: Count all diagnoses equally. Less accurate but:
  - Simpler to calculate (no manual weighting)
  - Data available in the dataset
  - Still correlates with readmission
  - Practical for this use case

**Trade-off**: Sacrificed some accuracy for simplicity and practicality.

**Q4.9**: "You made L_Score, A_Score, C_Score, E_Score as separate columns. Why not calculate VCI directly?"
**A**: 
- **Modularity**: If validation shows E_Score isn't predictive, I can remove it without recalculating others
- **Interpretability**: Stakeholders can see component scores: "This patient's acuity is high but comorbidity is low"
- **Debugging**: If total VCI is wrong, I can check each component independently
- **Future work**: If I switch to weighted VCI (C_Score weighted more than E_Score), I already have components

Good software engineering practice.

**Q4.10**: "If your high-risk group didn't have higher readmission rates, what would you conclude?"
**A**: The VCI doesn't work as designed. Options:
1. **Check for calculation errors**: Recalculate to verify
2. **Investigate data quality**: Are readmission labels correct?
3. **Revise the algorithm**: Use Phase 3 insights (what actually correlates?) to redesign VCI
4. **Document and publish**: "Our VCI didn't predict readmission in this cohort. Possible reasons: [list]"
5. **Don't deploy**: Don't implement a tool that doesn't work

This wouldn't be "failure"—null findings are valid research.

---

## ADVANCED QUESTIONS

**Q4.11**: "Why choose a rule-based algorithm instead of machine learning (logistic regression, random forest)?"
**A**: 
**Rule-based VCI advantages**:
- ✓ Interpretable: Each point has clinical meaning
- ✓ Transparent: No black-box; can explain to clinicians why patient scored high
- ✓ Simple: Calculate in 2 minutes; doesn't need computer
- ✓ Deployable: Can implement in EHR immediately
- ✓ Stable: Doesn't degrade over time (no retraining needed)

**ML advantages**:
- ✓ More accurate (might predict 5-10% better)
- ✗ Less interpretable (clinicians ask: "Why this score?")
- ✗ Requires maintenance (retrain regularly; performance decays)
- ✗ Harder to deploy (needs IT infrastructure)

**For this use case**: Simple > complex. Doctors need to trust the tool.

**Q4.12**: "How would you validate VCI on new data (patients not in the development dataset)?"
**A**: 
1. **Split data**: 80% train (build VCI), 20% test (validate)
2. **Build on train set**: Calculate VCI cutoffs using training data
3. **Apply to test set**: Score all test patients with the VCI
4. **Compare readmission rates**: Do test patients show the same gradient?
5. **Statistical test**: Chi-square on test data to confirm significance
6. **Conclusion**: If test data shows similar readmission pattern, VCI generalizes

This is **external validation**—stronger evidence than validating on the same data you built it on.

**Q4.13**: "If you had a much larger dataset with different hospitals, how would you adapt VCI?"
**A**: 
- **Recalibrate cutoffs**: Different hospitals have different readmission rates. What's "high-risk" in rural hospital might be different from urban academic medical center.
- **Hospital-specific analysis**: Run Phase 3 EDA separately for each hospital type.
- **Population-adjusted thresholds**: Baseline readmission rates might differ; adjust thresholds accordingly.
- **Validate separately**: Ensure VCI works in each hospital's population.

Core algorithm (L, A, C, E) stays the same; thresholds (7, 10) might change.

**Q4.14**: "What's the 'predictive value' of a high VCI score?"
**A**: 
**Positive Predictive Value (PPV)**: Of patients flagged as high-risk, what fraction actually readmit?
- High risk: 28% readmission rate
- PPV = 28% (i.e., 28% of flagged patients will readmit; 72% won't)

**Negative Predictive Value (NPV)**: Of patients flagged as low-risk, what fraction don't readmit?
- Low risk: 5% readmission rate
- NPV = 95% (i.e., 95% of non-flagged patients won't readmit)

These are important for clinical decision-making. "If my patient scores high, what's the chance they'll readmit?" Answer: 28%.

---

# BUSINESS & STRATEGIC QUESTIONS

## BASIC BUSINESS QUESTIONS

**Q5.1**: "What's the HRRP (Hospital Readmissions Reduction Program)?"
**A**: Federal program by Centers for Medicare & Medicaid Services (CMS) that penalizes hospitals for excessive 30-day readmission rates for specific conditions (heart failure, pneumonia, diabetes, COPD). Hospitals exceeding the benchmark readmission rate face financial penalties (up to 3% of Medicare revenue). Drives the business case for readmission reduction projects.

**Q5.2**: "What's VHN's current readmission rate and how does it compare to benchmark?"
**A**: VHN: 18% readmission rate. Benchmark: ~13-15% (varies by condition). VHN exceeds benchmark by 3-5 percentage points. This excess triggers CMS penalties.

**Q5.3**: "What's the financial impact of a single prevented readmission?"
**A**: Typical hospital readmission costs $3,000-5,000 per case (3-5 day stay, procedures, medications). If VCI-based intervention prevents 100 readmissions/year, that's $300K-500K in direct cost avoidance, plus penalties avoided.

**Q5.4**: "How would VHN operationalize VCI in practice?"
**A**: 
1. At discharge, calculate patient's VCI score (4 components, <2 min)
2. If VCI > 10 (high-risk): Assign case manager, arrange home health, schedule 48-hour follow-up call
3. If VCI 7-10 (medium): Enhanced discharge teaching, 7-day follow-up call
4. If VCI < 7 (low): Standard discharge instructions, routine follow-up
5. Track and monitor readmission outcomes for each group

**Q5.5**: "What resistance might VHN face when implementing VCI?"
**A**: 
- "It looks too simple" → Educate: LACE is evidence-based; simplicity is a strength
- "Nurses don't have time" → VCI takes 2 minutes; prevents expensive readmissions
- "We're doing fine" → Show data: 18% rate exceeds benchmark; penalties are real
- "We already have a risk system" → VCI is evidence-based and tailored to your data
- "How do we know it works?" → Propose pilot: randomize high-risk patients to intervention vs. control; measure outcomes

---

## INTERMEDIATE BUSINESS QUESTIONS

**Q5.6**: "What would you measure to determine if VCI actually reduced readmissions?"
**A**: 
**Study design**:
1. Baseline period: Measure readmission rates before VCI implementation
2. Implementation period: Deploy VCI, implement discharge protocols
3. Post period: Measure readmission rates after implementation
4. Compare: Did readmission rate drop? By how much?

**Metrics**:
- 30-day readmission rate (primary outcome)
- Readmission rate by risk category (is high-risk group benefiting from intervention?)
- Cost per prevented readmission
- ROI: (Cost savings from prevented readmissions) / (Cost to implement VCI)

**Statistical test**: Chi-square test comparing pre vs. post readmission rates.

**Q5.7**: "If VCI identifies 40% of patients as high-risk, what's the problem?"
**A**: 
- **Staffing capacity**: Can't intensify discharge planning for 40% of patients. Discharge planners don't have capacity.
- **Cost**: Case management, home health cost money. Budget might not support 40%.
- **Solution options**:
  1. Adjust VCI thresholds to identify highest-risk 20% only
  2. Use predictive model to narrow further (Phase 5: ML)
  3. Implement phased rollout (high-risk first, expand later)
  4. Hire more staff (if business case justifies it)

**Q5.8**: "What's the difference between your VCI and competing risk tools?"
**A**: 
- **LACE Index**: Published, evidence-based, but uses complex Charlson comorbidity weighting
- **Your VCI**: Adapted LACE for your data, simpler diagnosis count, validated on your population
- **ML models**: More accurate but less interpretable, require maintenance

**Your advantage**: Evidence-based (LACE), simple (can teach in 5 min), validated (shows readmission gradient).

**Q5.9**: "How would you present this to the hospital board?"
**A**: 
**1-minute pitch**: "VHN's 18% readmission rate costs millions in penalties and harms patients. We built a risk score (VCI) that identifies high-risk patients at discharge. High-risk patients have 28% readmission vs. 5% for low-risk. By providing intensive discharge planning to high-risk group, we project reducing overall readmission by 100+ cases/year, saving $300K-500K and improving patient outcomes."

**Evidence**: Show one visualization (readmission by VCI risk category).

**Q5.10**: "What's the scalability of this solution?"
**A**: 
**Highly scalable**:
- VCI uses only standard fields (time, admission type, diagnoses, ED visits) available in most EHRs
- Calculation is simple (addition; no complex computation)
- Can be integrated into automated workflows
- Once built, no ongoing maintenance needed (unlike ML models)

**Limitations**:
- Requires accurate data entry (garbage in, garbage out)
- Thresholds might need recalibration for different populations
- Doesn't replace clinical judgment

---

## ADVANCED BUSINESS QUESTIONS

**Q5.11**: "How would you handle the transition from manual VCI calculation to automated?"
**A**:
1. **Pilot phase**: Manual calculation by discharge planners for 2-4 weeks. Test workflow, gather feedback.
2. **IT integration**: Work with EHR team to build automated VCI calculation at discharge
3. **Parallel run**: Run automated system alongside manual calculation for 2-4 weeks to verify accuracy
4. **Cutover**: Switch to automated; retire manual process
5. **Monitoring**: Track flag rates and readmission outcomes to ensure automation is working

**Q5.12**: "What would you do if VCI performance degrades over time (high-risk patients no longer have higher readmission)?"
**A**: 
**Possible causes**:
1. Population changed (patient mix different)
2. Care processes improved (discharge planning now better for all)
3. Medication changes (new therapies reduce readmission overall)
4. Seasonal variation (winter has different readmission patterns)

**Solution**:
1. **Diagnose**: Compare current data to baseline. What's different?
2. **Recalibrate**: Run Phase 3 EDA on recent data. Do the same factors predict readmission? If not, which do?
3. **Adjust**: Rebuild VCI with updated insights if needed
4. **Revalidate**: Confirm new version predicts readmission

**Monitoring cadence**: Annual recalibration recommended.

---

# CODE IMPLEMENTATION QUESTIONS

## BASIC CODE QUESTIONS

**Q6.1**: "Write the function to calculate L (Length of Stay) Score."
**A**:
```python
def calculate_L_score(time_in_hospital):
    """Calculate Length of Stay score (0-7 points)"""
    if time_in_hospital < 1:
        return 0
    elif 1 <= time_in_hospital <= 4:
        return 1
    elif 5 <= time_in_hospital <= 13:
        return 4
    else:  # >= 14
        return 7
```

**Q6.2**: "Write the function to calculate A (Acuity) Score."
**A**:
```python
def calculate_A_score(admission_type):
    """Calculate Acuity score (0 or 3 points)"""
    if admission_type in ['Emergency', 'Trauma Center']:
        return 3
    else:
        return 0
```

**Q6.3**: "Write the function to calculate C (Comorbidity) Score."
**A**:
```python
def calculate_C_score(number_diagnoses):
    """Calculate Comorbidity score (0, 3, or 5 points)"""
    if number_diagnoses < 4:
        return 0
    elif 4 <= number_diagnoses <= 7:
        return 3
    else:  # >= 8
        return 5
```

**Q6.4**: "Write the function to calculate E (Emergency Visits) Score."
**A**:
```python
def calculate_E_score(number_emergency):
    """Calculate Emergency visit score (0, 3, or 5 points)"""
    if number_emergency == 0:
        return 0
    elif 1 <= number_emergency <= 4:
        return 3
    else:  # > 4
        return 5
```

**Q6.5**: "How would you apply these functions to a DataFrame?"
**A**:
```python
df['L_Score'] = df['time_in_hospital'].apply(calculate_L_score)
df['A_Score'] = df['admission_type_id'].apply(calculate_A_score)
df['C_Score'] = df['number_diagnoses'].apply(calculate_C_score)
df['E_Score'] = df['number_emergency'].apply(calculate_E_score)

# Composite
df['VCI_Score'] = df['L_Score'] + df['A_Score'] + df['C_Score'] + df['E_Score']
```

---

## INTERMEDIATE CODE QUESTIONS

**Q6.6**: "Write a function to categorize VCI scores into risk categories."
**A**:
```python
def categorize_VCI_risk(vci_score):
    """Categorize VCI into risk levels"""
    if vci_score < 7:
        return 'Low Risk'
    elif 7 <= vci_score <= 10:
        return 'Medium Risk'
    else:  # > 10
        return 'High Risk'

df['VCI_Risk_Category'] = df['VCI_Score'].apply(categorize_VCI_risk)
```

**Q6.7**: "Write code to calculate readmission rate by risk category."
**A**:
```python
# Create binary readmission indicator
df['readmitted_30'] = (df['readmitted'] == '<30').astype(int)

# Calculate rate by risk category
readmission_by_risk = df.groupby('VCI_Risk_Category').agg({
    'readmitted_30': ['sum', 'count']
})

readmission_by_risk.columns = ['Readmitted_Count', 'Total_Count']
readmission_by_risk['Readmission_Rate_%'] = (
    readmission_by_risk['Readmitted_Count'] / readmission_by_risk['Total_Count'] * 100
).round(1)

print(readmission_by_risk)
```

**Q6.8**: "Write code to visualize readmission rate by risk category."
**A**:
```python
import matplotlib.pyplot as plt
import seaborn as sns

fig, ax = plt.subplots(figsize=(10, 6))

# Prepare data
rates = df.groupby('VCI_Risk_Category')['readmitted_30'].mean() * 100
risk_order = ['Low Risk', 'Medium Risk', 'High Risk']
rates = rates.reindex(risk_order)

# Create bar chart
colors = ['green', 'yellow', 'red']
bars = ax.bar(risk_order, rates, color=colors, edgecolor='black', linewidth=1.5)

# Customize
ax.set_xlabel('VCI Risk Category', fontsize=12, fontweight='bold')
ax.set_ylabel('30-Day Readmission Rate (%)', fontsize=12, fontweight='bold')
ax.set_title('Readmission Rates by VCI Risk Stratification', fontsize=14, fontweight='bold')
ax.set_ylim(0, 35)

# Add value labels on bars
for bar, rate in zip(bars, rates):
    height = bar.get_height()
    ax.text(bar.get_x() + bar.get_width()/2., height,
            f'{rate:.1f}%', ha='center', va='bottom', fontweight='bold')

plt.tight_layout()
plt.show()
```

**Q6.9**: "How would you check for errors in VCI calculation?"
**A**:
```python
# Check for NaN values in components
print("NaN counts:")
print(f"L_Score NaN: {df['L_Score'].isna().sum()}")
print(f"A_Score NaN: {df['A_Score'].isna().sum()}")
print(f"C_Score NaN: {df['C_Score'].isna().sum()}")
print(f"E_Score NaN: {df['E_Score'].isna().sum()}")

# Check score ranges
print("\nScore ranges:")
print(f"L_Score: {df['L_Score'].min()} to {df['L_Score'].max()}")
print(f"A_Score: {df['A_Score'].min()} to {df['A_Score'].max()}")
print(f"C_Score: {df['C_Score'].min()} to {df['C_Score'].max()}")
print(f"E_Score: {df['E_Score'].min()} to {df['E_Score'].max()}")

# Check total VCI range (should be 0-20)
print(f"\nVCI_Score: {df['VCI_Score'].min()} to {df['VCI_Score'].max()}")

# Spot-check a few rows
print("\nSample rows:")
print(df[['time_in_hospital', 'L_Score', 'admission_type_id', 'A_Score', 'VCI_Score']].head(10))
```

---

## ADVANCED CODE QUESTIONS

**Q6.10**: "How would you handle missing values in the VCI components?"
**A**: 
**Issue**: If `time_in_hospital` is NaN, you can't calculate L_Score.

**Solution**:
```python
# Option 1: Impute missing values before scoring
df['time_in_hospital'] = df['time_in_hospital'].fillna(df['time_in_hospital'].median())

# Option 2: Handle NaN in function
def calculate_L_score_safe(time_in_hospital):
    if pd.isna(time_in_hospital):
        return np.nan  # Return NaN; handle later
    if time_in_hospital < 1:
        return 0
    # ... rest of logic

# Option 3: Drop rows with missing VCI components
df_complete = df.dropna(subset=['time_in_hospital', 'admission_type_id', 'number_diagnoses', 'number_emergency'])

# Document: "X patients couldn't be scored due to missing data"
```

**Q6.11**: "How would you use vectorized operations instead of .apply() for better performance?"
**A**: 
**.apply()** is slow for large datasets. Use numpy conditions:

```python
# Slow (uses .apply())
df['L_Score'] = df['time_in_hospital'].apply(calculate_L_score)

# Fast (vectorized)
df['L_Score'] = np.select(
    [
        df['time_in_hospital'] < 1,
        (df['time_in_hospital'] >= 1) & (df['time_in_hospital'] <= 4),
        (df['time_in_hospital'] >= 5) & (df['time_in_hospital'] <= 13),
        df['time_in_hospital'] >= 14
    ],
    [0, 1, 4, 7],
    default=np.nan
)
```

For 100K rows, vectorized is 10-100x faster.

**Q6.12**: "How would you create a pipeline that runs all 4 phases in sequence?"
**A**:
```python
def run_project_pipeline():
    """Run all 4 phases in sequence"""
    
    # Phase 1: Data Sanitation
    print("Phase 1: Cleaning data...")
    df = load_and_clean_data()
    
    # Phase 2: Web Scraping
    print("Phase 2: Scraping ICD-9 descriptions...")
    icd9_map = scrape_icd9_descriptions()
    df = enrich_diagnoses(df, icd9_map)
    
    # Phase 3: EDA
    print("Phase 3: Exploratory analysis...")
    eda_insights = run_eda(df)
    
    # Phase 4: VCI
    print("Phase 4: Building VCI...")
    df = calculate_vci_scores(df)
    df = categorize_vci_risk(df)
    validation_results = validate_vci(df)
    
    print("Pipeline complete!")
    return df, validation_results

# Run it
final_df, results = run_project_pipeline()
```

---

# EDGE CASE & TROUBLESHOOTING QUESTIONS

**Q7.1**: "What if a patient has missing admission_type_id? Can they be scored?"
**A**: 
- Missing admission type → Can't calculate A_Score → VCI is incomplete
- Options:
  1. Impute: Use mode (most common admission type)
  2. Exclude: Don't score this patient; flag for manual review
  3. Assign default: Assume "Other" (not Emergency) → A_Score = 0

Decision: Document your choice and impact.

**Q7.2**: "Some patients have time_in_hospital = 0 (admitted and discharged same day). How do you handle?"
**A**: 
- time_in_hospital < 1 → L_Score = 0 (per the algorithm)
- This is correct: same-day discharge indicates low-risk, straightforward case
- No special handling needed

**Q7.3**: "What if number_diagnoses = 0? Can a patient have no diagnoses?"
**A**: 
- Unlikely in real data (they were admitted for something)
- If it happens: C_Score = 0 (< 4 diagnoses)
- Might indicate data quality issue worth investigating

**Q7.4**: "Readmission categories are '<30', '>30', 'NO'. How do you handle these?"
**A**: 
Create binary indicator for 30-day readmission:
```python
df['readmitted_30'] = (df['readmitted'] == '<30').astype(int)
```
Now: 1 = readmitted <30 days, 0 = not readmitted within 30 days

**Q7.5**: "If web scraping fails for 10% of codes, how do you proceed?"
**A**: 
- Document which codes failed: "Codes 414, 428, 500 had no descriptions due to website errors"
- Strategy options:
  1. Retry failed codes with exponential backoff
  2. Use general category: Code 414 → "Ischemic Heart Disease (specific code not found)"
  3. Exclude them: Don't use codes without descriptions
- Trade-off: Some description loss, but maintain project progress

---

# PRESENTATION & COMMUNICATION QUESTIONS

**Q8.1**: "If you had only 5 minutes to present this to the hospital board, what would you say?"
**A**: (1-minute version, repeated 5 times)
"Vitality Health Network's 18% readmission rate costs millions in penalties. We analyzed 100,000 patient records and built a simple risk score (VCI) that identifies high-risk patients. High-risk patients have 28% readmission; low-risk have 5%. By providing intensive discharge planning to the highest-risk 20% of patients, we can prevent hundreds of readmissions and reduce penalties. The score takes 2 minutes to calculate and can be implemented immediately in your discharge workflow."

**Q8.2**: "How would you explain confounding to a clinician who believes 'insulin causes high readmission'?"
**A**: "You're right that insulin users have higher readmission—I see the same pattern. But insulin doesn't cause readmission. Think of it this way: Patients with Type 1 diabetes or poorly controlled Type 2 need insulin. These patients are sicker and have higher baseline readmission. Insulin is a marker of severity, not a cause. The insight for your discharge team: When you see insulin on the medication list, that's a red flag—give these patients extra attention."

**Q8.3**: "How would you respond if someone says, 'Your analysis uses historical data from 1999-2008. That's 20 years old. How is it relevant?'"
**A**: "Great point. The data is historical, so if care has improved dramatically since then, readmission rates might be lower now. That said: (1) The *factors* that predict readmission (age, comorbidities, ED visits) are likely stable. (2) VCI uses these stable factors. (3) We should validate VCI on modern data and recalibrate thresholds if needed. But the framework is sound and gives us a place to start."

---

# FINAL CATCH-ALL QUESTIONS

**Q9.1**: "What's the biggest limitation of your analysis?"
**A**: Historical data (1999-2008). Modern patient populations, medications, and discharge practices differ. To deploy VCI in real VHN: (1) Validate on recent data, (2) Recalibrate thresholds if needed, (3) Pilot with a subset of patients, (4) Monitor performance over time.

**Q9.2**: "If you had 6 more months to work on this project, what would you do?"
**A**: 
1. Collect modern data; validate VCI on recent cohorts
2. Integrate temporal features (seasonality, trends over time)
3. Develop ML model for comparison (show accuracy trade-offs)
4. Conduct pilot at VHN; measure real-world impact
5. Create automated EHR integration for VCI calculation
6. Develop mobile app for nurses to calculate VCI at bedside
7. Extend to other conditions (heart failure, pneumonia, not just diabetes)

**Q9.3**: "What would you say your project's main contribution is?"
**A**: "We transformed raw, incomprehensible clinical data into a clinically actionable risk tool. The contribution isn't just the tool itself, but demonstrating the full pipeline: clean data → enrich with context → discover drivers → build decision support. We showed that evidence-based practices (LACE index) + simple algorithms can be more practical than complex models for healthcare decision-making."

**Q9.4**: "Are there ethical concerns with your readmission prediction model?"
**A**: 
- **Potential bias**: If historical readmissions reflect biased discharge practices (minorities get less follow-up), model perpetuates bias
- **Fairness**: Ensure VCI doesn't discriminate by race/gender. I should analyze readmission rates by demographic; if disparities exist, that's a clinical equity issue
- **Transparency**: VCI should be interpretable so clinicians understand why patients are flagged
- **Accountability**: If VCI causes harm (high-risk patient not flagged), who's responsible?

These are important to acknowledge.

**Q9.5**: "If hospitals just ignore VCI and discharge all patients the same way, does VCI fail?"
**A**: No, but VCI's value isn't realized. VCI is a tool; tools only help if used. The real success is implementation:
1. Hospital adopts VCI protocols
2. High-risk patients get intensive follow-up
3. Readmission rates decline
4. Hospital avoids penalties, saves costs

If they don't implement, it's not a technical failure; it's a change management challenge.

---

**You have this. Go crush the viva!** 🚀
