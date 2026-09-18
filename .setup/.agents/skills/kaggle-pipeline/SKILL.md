---
name: Kaggle Pipeline Executor
description: Guides the agent on how to autonomously execute Kaggle competitions iteratively using a single End-to-End notebook template and proper Local CV tracking.
---

# Kaggle Autonomous Execution Protocol

When executing a Kaggle competition, you MUST act autonomously using this iterative protocol. You will use a single Jupyter Notebook per experiment to keep everything cohesive.

## Phase 1: Context Gathering & Initialization
1. Search the competition using `kaggle_search` if the name is not fully known.
2. **MANDATORY**: Use `kaggle_competition_info` to understand the goal, rules, and most importantly, the **Evaluation Metric**. You must tailor your loss function and metrics to this.
3. Run `python scripts/init_competition.py <competition_name> .` to download the dataset and copy `Experiment_Template.ipynb` and `AI_LAB_BOOK.md`.

## Phase 2: Autonomous Experimentation Loop
You are strictly forbidden from modifying `Experiment_Template.ipynb`. Instead, you must iterate.

**For every new iteration/idea (Trial & Error):**
1. **Duplicate** `notebooks/Experiment_Template.ipynb` (or copy the notebook of your previous best experiment) and name it `notebooks/Exp<XX>_<ShortDescription>.ipynb` (e.g., `notebooks/Exp01_Baseline.ipynb`, `notebooks/Exp02_OptunaTuning.ipynb`).
2. **Strategy & Brainstorming**: Open the new notebook and write down your hypothesis and algorithm choices in the top Markdown cells.
3. **Execution**:
   - Write well-commented code in the cells. Every non-trivial block must be explained.
   - Run the EDA to understand the data.
   - Handle missing values and engineer features. Train and Test data MUST be concatenated before imputation/encoding to prevent column mismatch.
   - Run the **Cross Validation (Local CV)** block. 
4. **Scoring**: You must print out the final overall **Local CV Score**.

## Phase 3: Submission Throttling (Crucial)
Kaggle has strict daily submission limits (e.g., 5 per day). You MUST conserve submissions by relying on your Local CV score.

- **First Iteration (Exp01)**: You MUST submit this to establish a Public Leaderboard (LB) baseline.
- **Subsequent Iterations**: 
  - Compare the new Local CV score to the previous best Local CV score recorded in `AI_LAB_BOOK.md`.
  - If the Local CV score **did NOT improve significantly**, DO NOT SUBMIT. Simply move to the next iteration (Exp<XX+1>).
  - If the Local CV score **IMPROVED**, use `kaggle_submit` to get the LB score.

## Phase 4: Logging
After every experiment (whether submitted or not), you MUST open `AI_LAB_BOOK.md` and log:
- Notebook Name (`Exp02_...`)
- Local CV Score
- Public LB Score (or "-" if skipped)
- Notes (What worked? What caused overfitting? What to try next?)

Repeat Phase 2 to Phase 4 until the user stops you or you reach the goal!
