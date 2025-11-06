# Performance Evaluation of Whisper for Automatic Processing of Polish L2 Interlanguage

This repository contains the implementation and data processing scripts developed for the master's thesis *Performance Evaluation of Tools for Automatic Processing of Polish L2 Interlanguage*.
The project investigates how current Automatic Speech Recognition tools handle non-native Polish speech (interlanguage). It focuses on evaluating OpenAI Whisper and spaCy for their ability to faithfully transcribe and analyze learner productions.

### Project Overview

The project explores the challenges of processing interlanguage speech—utterances produced by learners of Polish as a second language (L2).
Using a corpus of learner recordings from the VILLA Project (adult participants completing a “Route Direction” task), the study examines how well automated tools capture both phonetic and morphological errors typical of early stages of L2 acquisition.

The analysis combines:

- Automatic speech recognition (Whisper)
- Morphosyntactic tagging (spaCy)
- Quantitative evaluation
- Linguistic interpretation of error patterns (pronunciation and declension)

## Corpus Preparation

**Source:** The learner data comes from the VILLA project, in which adults from five L1 backgrounds (French, Italian, English, German, Dutch) learned Polish for 14 hours and performed a Route Direction oral task.

**Data:** Each participant’s recording was manually transcribed in ELAN using two tiers:

- manual transcription representing exactly what the learner said (including errors and disfluencies);
- correct Polish version, representing the intended meaning in standard Polish.

**Native benchmark:** A smaller dataset from four native Polish speakers performing the same task served as a control corpus.

**File types:**

- Audio recordings (`.wav`),
- Manual transcriptions (`.eaf`),
- Whisper-generated transcriptions (`.txt`).

### Preprocessing and Data Alignment

**Manual cleaning:** Teacher speech and non-Polish segments were manually removed from all files.

**Automatic steps:**

- Conversion to lowercase and removal of punctuation.
- Integration of manual, corrected, and automatic transcriptions into a unified `JSON` dataset containing:
  - learner ID,
  - country of origin,
  - automatic transcription,
  - aligned manual and corrected Polish segments.
- Fuzzy string matching for word-level comparison.
- Lemmatization and morphological tagging using spaCy.
- Polish lexicon integration: to detect non-existent Polish words, the corpus was compared against an external Polish lexicon, compiled from [A repository of Polish NLP resources](https://github.com/sdadas/polish-nlp-resources).

## Qualitative Linguistic Analysis

Two main error domains were studied:

**Declension deviations:**

- Frequent misuse of grammatical cases — especially overuse of the nominative in contexts requiring genitive or instrumental.
- Errors often followed predictable confusion between Genitive, Instrumental, Locative and Accusative forms.

**Pronunciation deviations:**

- Frequent reduction of consonant clusters, vowel denasalization, and devoicing.
- Tendencies among L1 groups.

An interactive Python-based visualization tool allows inspection of recurrent sound-level errors.

### How to run the platform

In the terminal:

```bash
python3 -m http.server 8000
```

In the browser:

```bash
http://localhost:8000
```

**Important:**
In the same folder as the file plateforme_erreurs_prononciation.html, you must have the following files (located in the data/processed/csv directory):
- `erreurs_prononciation_groupées_par_mots_API.csv`
- `erreurs_prononciation_groupées_par_mots.csv`


## Morphosyntactic Analysis

**Tool:** spaCy

- Comparison of grammatical case between erroneous (manual) and correct forms.
- Evaluation of spaCy’s case identification accuracy on:
  - Interlanguage forms,
  - Correct Polish forms,
  - Native Polish dataset (baseline).

| Dataset                         | Accuracy |
| ------------------------------- | -------- |
| Interlanguage (erroneous forms) | 60%      |
| Correct Polish forms            | 75%      |
| Native benchmark                | 67%      |

**Conclusions:**

- spaCy struggles to assign correct grammatical Polish cases to distorted or incomplete forms,
- Polish inflectional complexity itself poses difficulties for automated case detection.

## Automatic Speech Recognition Evaluation

**Tool:** [Whisper Small Model](https://github.com/openai/whisper) developed by OpenAI.

**Evaluation metrics:** Word Error Rate (WER) and Character Error Rate (CER)

Both metrics were computed for:

- Global corpus (all recordings),
- Native Polish subset,
- Interlanguage subset (learners’ speech).


| Corpus                       | WER       | CER       |
| ---------------------------- | --------- | --------- |
| Native Polish                | 13.7%     | 6.4%      |
| Interlanguage (learners)     | 75.4%     | 46.4%     |


- Whisper performed well on native data but struggled severely with learner speech.
- Many non-standard pronunciations and morphophonemic distortions led to word substitutions, deletions, or hallucinations.

### Qualitative classification

Each automatically transcribed word was compared with its manual and corrected versions and assigned to one of the following categories:

1. For prononciation-related interlanguage:


| Category                                              | Number of words | Percentage of corpus |
|--------------------------------------------------------|----------------:|---------------------:|
| Identically transcribed words                          | 72              | 10.88%               |
| Overcorrected words (correct in context)               | 202             | 30.51%               |
| Overcorrected words (existing in Polish but incorrect in context) | 176 | 26.59% |
| Words invented by Whisper                              | 108             | 16.31%               |
| Untranscribed words                                    | 104             | 15.71%               |
| **Total**                                              | **662**         | **100%**             |

2. For declension-related interlanguage:


| Category                              | Number of examples | Percentage of corpus |
|---------------------------------------|-------------------:|---------------------:|
| Faithful reproduction (reproduction of the error) | 221 | 42.10% |
| Overcorrection                        | 114               | 21.71%               |
| Not transcribed                       | 43                | 8.19%                |
| Unknown                               | 39                | 7.43%                |
| Invented form                         | 108               | 20.57%               |
| **Total**                             | **525**           | **100%**             |

**Conclusions:**

Whisper tends to “normalize” learner speech — it corrects non-native pronunciations into fluent native-like forms, which undermines the goal of faithfully capturing the learner’s interlanguage. However, declension-related errors were often transcribed correctly, showing partial morphological robustness.

## Future Directions

### Fine-tuning Whisper for Polish interlanguage:
Develop a version of Whisper trained on a dedicated corpus of learner speech, enabling the model to transcribe interlanguage forms rather than correcting them to standard Polish.

- Requires a manually annotated dataset created by native speakers with linguistic expertise.
- Explore whether separate models are needed for specific L1 groups (pronunciation errors may vary across L1s, but case-related errors appear universal, suggesting one general model could suffice)
- Learner errors evolve with proficiency, so future corpora should include diverse speech types across different contexts.
- Enhance systems like spaCy for more accurate case detection in non-standard learner language.