# 🎁 Children's Gift Recommendation System

A **rule-based expert system** that recommends gifts for children based on age, interests, budget, gender, and gift type. Built with **CLIPS** for the inference engine and **Python/Tkinter** for the graphical interface.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [System Architecture](#system-architecture)
- [Knowledge Base](#knowledge-base)
- [Scoring Algorithm](#scoring-algorithm)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Future Improvements](#future-improvements)
- [License](#license)

---

## Overview

Selecting the right gift for a child involves balancing multiple factors: age appropriateness, personal interests, budget constraints, and gift type preferences. This expert system captures that decision-making process into a **knowledge base** of 75 gifts across 10 categories, connected through 50+ interest-to-category mappings, and uses **forward-chaining inference** to generate ranked, scored recommendations.

### Why Rule-Based Instead of Machine Learning?

| Rule-Based (This Project) | Machine Learning |
|---------------------------|------------------|
| Transparent reasoning — every recommendation is explainable | Black-box decisions |
| No training data required | Needs large datasets |
| Easy to modify — add rules or facts directly | Requires retraining |
| Deterministic — same input always gives same output | Can be non-deterministic |
| Perfect for structured, well-understood domains | Better for pattern recognition from data |

---

## Features

- 🧠 **Forward-chaining inference engine** using CLIPS with the Rete algorithm
- 🎯 **Multi-factor scoring system** (0-100 points) ranking gifts by:
  - Interest match strength
  - Age appropriateness
  - Budget efficiency
  - Gift type alignment
- 📊 **75 gifts** across 10 categories with balanced physical/experience/activity types
- 🔗 **50+ interest-to-category mappings** for flexible user input
- 🖥️ **Clean Tkinter GUI** with scrollable inputs and color-coded results
- 🛡️ **Defensive design** — proper working memory cleanup, input validation, error handling

---

## System Architecture
┌─────────────────────────────────────────────────────────┐

│ User Interface (Python/Tkinter) │

│ gui.py │

├─────────────────────────────────────────────────────────┤

│ Inference Engine (CLIPS) │

│ ┌──────────────┐ ┌────────────┐ ┌─────────────────┐ │

│ │ Rule Base │ │ Working │ │ Pattern │ │

│ │ (5 Rules) │ │ Memory │ │ Matcher │ │

│ │ │ │ (Facts) │ │ (Rete Net) │ │

│ └──────────────┘ └────────────┘ └─────────────────┘ │

├─────────────────────────────────────────────────────────┤

│ Knowledge Base (CLIPS Facts) │

│ ┌──────────────┐ ┌────────────┐ ┌─────────────────┐ │

│ │ Gift │ │ Interest │ │ Templates │ │

│ │ Database │ │ Mappings │ │ & Schemas │ │

│ │ (75 items) │ │ (50+ map) │ │ (4 templates) │ │

│ └──────────────┘ └────────────┘ └─────────────────┘ │

└─────────────────────────────────────────────────────────┘

### KBS Components

| Component | Location | Purpose |
|-----------|----------|---------|
| **Knowledge Base** | `knowledge_base/*.clp` | Gift facts, interest mappings, rule definitions |
| **Inference Engine** | CLIPS (via `engine.py` line 85: `env.run()`) | Forward-chaining pattern matching |
| **Working Memory** | Managed by `engine.py` | Transient facts: user queries, intermediate results, final recommendations |
| **User Interface** | `gui.py` | Input collection and result presentation |

---

## Knowledge Base

### Gift Categories

| Category | Physical | Experience | Activity | Total |
|----------|:-------:|:----------:|:--------:|:-----:|
| Arts & Crafts | 7 | 2 | 0 | 9 |
| Athletic | 5 | 1 | 6 | 12 |
| Board Games | 5 | 1 | 0 | 6 |
| Books | 5 | 1 | 0 | 6 |
| Construction | 6 | 2 | 0 | 8 |
| Educational | 3 | 2 | 0 | 5 |
| Musical | 4 | 2 | 0 | 6 |
| Outdoor | 3 | 2 | 4 | 9 |
| Science Kits | 5 | 2 | 0 | 7 |
| Technology | 5 | 2 | 0 | 7 |
| **TOTAL** | **48** | **17** | **10** | **75** |

### Interest Mapping Design

Interests act as a **semantic bridge** between user language and system categories:

Multiple interests can map to the same category, and a single interest could map to multiple categories in future expansions.

---

## Scoring Algorithm

Each matching gift receives a score from **0 to 100** based on four weighted factors:

| Factor | Weight | Formula |
|--------|:------:|---------|
| **Interest Match** | 50 pts | `10 × number_of_matching_interests` |
| **Age Appropriateness** | 20 pts | `max(0, 20 - 2 × distance_from_range_middle)` |
| **Budget Efficiency** | 25 pts | `max(0, 25 - 25 × price/budget)` |
| **Type Match** | 5 pts | `5` for exact match, `3` for "any" type |

### Example Scoring Breakdown

**Query:** Age 8, Budget $50, Interests: ["science", "experiments"]

| Gift | Interest | Age | Budget | Type | Total |
|------|:---:|:---:|:---:|:---:|:---:|
| 🥇 Dinosaur Fossil Dig Kit | 20 | 19 | 16 | 3 | **58** |
| 🥈 Volcano Making Kit | 20 | 18 | 16 | 3 | **57** |
| 🥉 Science Museum Visit | 20 | 11 | 17 | 3 | **51** |

---

## Project Structure

gift_recommender/

│

├── knowledge_base/           # CLIPS knowledge base files

│   ├── templates.clp         # Deftemplate definitions (schemas)

│   ├── mappings.clp          # Interest-to-category ontology (50+ mappings)

│   ├── gifts.clp             # Gift database (75 items)

│   └── rules.clp             # Production rules (5 rules with scoring)

│

├── src/                      # Python source code

│   ├── __init__.py

│   ├── engine.py             # CLIPS wrapper class (GiftRecommender)

│   └── gui.py                # Tkinter graphical user interface

│

├── main.py                   # Application entry point

└── README.md                 # This file

## How it Works

Inference Cycle

When a user clicks "Find Perfect Gift!", this is what happens:

1. User query is asserted into working memory
   └─ (user-query (age 8) (budget 50.00) (interests "science" "experiments"))

2. Rule 1 fires: match-interests-to-categories
   └─ "science" → "science_kit", "experiments" → "science_kit"
   └─ Asserts: (matching-category "science_kit")

3. Rule 2 fires: count-category-matches
   └─ Counts 2 interests mapping to "science_kit"
   └─ Asserts: (category-match-count (category "science_kit") (count 2))

4. Rule 3 fires: find-and-score-matching-gifts
   └─ Matches all science_kit gifts within age/budget/type constraints
   └─ Calculates score for each
   └─ Asserts: (matching-gift (name "Volcano Kit") (score 57)) ...

5. Rules 4-5 fire: cleanup (salience -10)
   └─ Retracts intermediate facts, keeping only results

6. Python collects matching-gift facts and returns sorted recommendations

Conflict Resolution Strategy

The system uses CLIPS' default depth strategy with salience-based priority overrides:

Rules 1-3 use default salience (0) — natural pipeline order via depth

Rules 4-5 use salience -10 — fire only after all scoring is complete

Within same salience: textual order in rules.clp breaks ties

This creates a deterministic pipeline: match → count → score → clean

## Future Improvements

User profiles — save preferences and recommendation history

Machine learning hybrid — collaborative filtering for cold-start mitigation

Multi-child optimization — recommend shared gifts for siblings

Occasion awareness — factor in birthday vs. holiday vs. graduation

## Acknowledgments

CLIPS — NASA's expert system tool

clipspy — Python bindings for CLIPS

Built as a Knowledge-Based Systems course project

## License
This project is licensed under the MIT License — see the LICENSE file for details

