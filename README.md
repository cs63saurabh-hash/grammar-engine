# Pāṇini Grammar Parsing Engine

**Roll No:** 2407042 · **IKS Domain:** Pāṇini's Aṣṭādhyāyī · **CS Concept:** Rule-Based Grammar Engine

> IKS Concept as CS Concept: **Pāṇini's Aṣṭādhyāyī as a Rule-Based Grammar Engine**

A single self-contained `index.html` — no build step, no server, no
dependencies — that models Pāṇini's Sanskrit grammar (the
*Aṣṭādhyāyī*) as an ordered, prioritized production-rule system, with
a live interactive parser and a built-in test suite that runs in the
browser.

**Live demo:** open `index.html` directly, or deploy it for free on
GitHub Pages (instructions below).

---

## 1. Problem Statement

Modern rule-based parsing engines (tokenizers, semantic role
labellers, expert systems) must resolve **conflicts between multiple
matching rules** in a predictable, deterministic way. Pāṇini solved
exactly this problem ~2,500 years ago in the *Aṣṭādhyāyī*, via the
**utsarga–apavāda** principle: a general rule (*utsarga*) always
yields to a more specific exception rule (*apavāda*) when both are
applicable. This project implements a working parsing engine that
applies this precedence principle to assign **karaka (semantic
roles)** — subject, object, instrument, recipient, source, possessor,
location — to Sanskrit words based on their case-ending (**vibhakti**).

## 2. Objectives

- Represent Pāṇini's grammar rules as data (not hard-coded if/else).
- Implement the utsarga–apavāda (general-vs-exception) rule
  precedence as an explicit, testable sort order.
- Parse single words and full sentences into their karaka roles.
- Demonstrate that Sanskrit meaning is carried by case-endings, not
  word order.
- Validate correctness with a minimum of 10 automated test cases,
  visible and running live on the page.

## 3. Introduction to the IKS Concept

Pāṇini's *Aṣṭādhyāyī* is a ~4,000-sutra grammar of Sanskrit, organized
as an ordered rule system. Two ideas from it are used here:

- **Vibhakti (case endings)** — each of the 7 nominal cases has a
  characteristic suffix (e.g. `-aḥ` nominative, `-am` accusative,
  `-ena` instrumental).
- **Kāraka (semantic roles)** — each vibhakti signals *why* a word is
  in the sentence: who did the action (karta), what it was done to
  (karma), with what (karana), for whom (sampradana), from where
  (apadana), whose it is (sambandha), or where (adhikarana).
- **Utsarga–apavāda** — Pāṇini's own meta-rule for resolving conflicts
  between a general and a more specific rule: the specific one wins.

## 4. Introduction to the Computer Science Concept

A **rule-based grammar/parsing engine** assigns structure or meaning
to tokens by matching them against an ordered list of production
rules — the same mechanism behind lexer rule-priority resolution in
compilers, semantic role labelling in NLP pipelines, and rule-based
expert systems.

## 5. Conceptual Mapping Table

| Pāṇini / IKS concept                         | Computer Science equivalent                              |
|-----------------------------------------------|------------------------------------------------------------|
| Sūtra (grammar rule)                          | Rule object (suffix + result)                              |
| Vibhakti (case ending)                        | Token suffix / lexeme pattern                              |
| Kāraka (semantic role)                        | Semantic tag / parse label                                  |
| Utsarga (general rule)                        | Default / low-priority rule                                 |
| Apavāda (exception rule)                      | High-priority / more-specific rule                          |
| Utsarga–apavāda precedence                    | Rule-priority sort/resolution                                |
| Prātipadika (nominal stem)                    | Recovered stem after suffix stripping                       |
| Aṣṭādhyāyī (the rule set as a whole)          | The engine's rule base (`RULES` array)                       |

## 6. System Requirements

- Any modern web browser. That's it — the entire engine is vanilla
  JavaScript embedded in `index.html`, with fonts loaded from Google
  Fonts over CDN.

## 7. Proposed Methodology / System Design

```
sentence (string)
      │
      ▼
 split into words
      │
      ▼
for each word:
    try each rule, most specific first (utsarga-apavada order)
      │
      ▼
 first matching suffix -> (stem, vibhakti, karaka)
      │
      ▼
 collect all word analyses -> group by karaka -> sentence summary
```

## 8. Algorithm Specification (Pseudocode)

```
SORTED_RULES = sort(RULES, by: priority desc, then suffix length desc)

function analyzeWord(word):
    w = lowercase(trim(word))
    for rule in SORTED_RULES:
        if w ends with rule.suffix and length(w) > length(rule.suffix):
            stem = w minus rule.suffix
            return {word, stem, rule.suffix, rule.vibhakti, rule.karaka}
    return {word, unrecognized: true}

function parseSentence(sentence):
    return [analyzeWord(w) for w in split(sentence)]

function summarizeSentence(sentence):
    analyses = parseSentence(sentence)
    return group analyses by karaka
```

## 9. Working Code / Source Code

```
panini-grammar-engine/
├── index.html      # everything: engine, live parser UI, test suite
├── README.md
└── LICENSE
```

Open `index.html` in any browser — no install, no build.

## 10. Input and Output (sample)

Type `ramah phalam khadati` into the live parser on the page:

```
ramah   -> stem: Rama (proper noun)  suffix: -ah   karaka: karta (subject)
phalam  -> stem: fruit               suffix: -am   karaka: karma (object)
khadati -> unrecognized (no matching rule)
```

(`khadati` is a verb, not a case-inflected noun, so it is correctly
left unrecognized by this nominal-case engine — see Limitations.)

## 11. Test Cases (15 total, ≥ 10 required)

The page itself runs 15 assertions on load and renders a live
pass/fail grid — scroll to the "Test cases" section. Coverage: all 7
kāraka roles, apavāda-before-utsarga rule precedence, case
insensitivity, empty-string and unrecognized-word edge cases, stem
lookup, and full-sentence parsing/summary.

## 12. Result Analysis

All 15 test cases pass in the browser. The engine correctly resolves
suffix conflicts using explicit priority + longest-match ordering,
matching the traditional utsarga–apavāda resolution used in the
Aṣṭādhyāyī (e.g. `-asya` is matched before the shorter, more general
`-a`-family endings would be).

## 13. Complexity Analysis

- **Time:** For a word of length *n* and *R* rules, `analyzeWord` is
  `O(R · n)` worst case. For a sentence of *W* words,
  `parseSentence` is `O(W · R · n)`. With a fixed, small rule set
  (`R` = 7), this is effectively linear, `O(W · n)`.
- **Space:** `O(R)` for the rule table plus `O(W)` for the returned
  analyses.

## 14. Correctness & Limitations

**Correctness:** rule precedence is deterministic and covered by the
live test suite; the same input always produces the same karaka
assignment.

**Limitations:**
- Only nominal case endings (vibhakti) are modeled — verbs are
  intentionally out of scope and reported as `unrecognized`.
- Sandhi (sound-change at word boundaries) is not applied.
- The suffix list and lexicon are illustrative, not exhaustive —
  extending the `RULES` / `KNOWN_STEMS` objects in `index.html`
  requires no other code changes.

## 15. Future Scope

- Add verb (tiṅanta) conjugation rules and a sandhi module.
- Support plural/dual number, not just singular.
- Persist/share parses via a URL query string.

## 16. Conclusion

This project shows that Pāṇini's ~2,500-year-old *utsarga–apavāda*
rule-precedence principle maps directly onto a core Computer Science
idea — deterministic conflict resolution in rule-based systems — and
that a compact, fully-tested rule engine, running entirely client-side
in a browser, is enough to recover grammatical meaning from Sanskrit
case-endings.

## 17. References

- Pāṇini, *Aṣṭādhyāyī* (traditional Sanskrit grammar treatise).
- Cardona, G., *Panini: A Survey of Research*, Mouton, 1976.
- Kak, S., *The Paninian Approach to Natural Language Processing*,
  International Journal of Approximate Reasoning, 1987.

---

## Deploying this to GitHub Pages

```bash
cd panini-grammar-engine
git init
git add .
git commit -m "Initial commit: Panini Grammar Parsing Engine (2407042)"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo-name>.git
git push -u origin main
```

Then on GitHub:
1. Go to your repo → **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a
   branch**.
3. Branch: `main`, folder: `/ (root)` → **Save**.
4. Wait ~1 minute, then your live site is at:
   `https://<your-username>.github.io/<your-repo-name>/`
