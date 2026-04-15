# Natural Language Processing - Lab 3: Comprehensive Notes

## 📚 Lab Outline & Core Concepts
- **Feature Extraction**: Transforming raw text into numerical representations for ML algorithms.
- **Lexical Feature Extraction Techniques**:
    - One Hot Encoding
    - Bag of Words (BOW)
    - N-grams
    - TF-IDF
- **Text Similarity**: Lexical vs. Semantic comparison.
- **Word Embeddings**: Word2Vec (CBOW & Skip-gram).


---

## 1. 🧬 Feature Extraction Fundamentals

### Definition: What is a Feature?
A **feature** is selected or treated data prepared as input for Machine Learning algorithms.
- *Examples:* Price of a house, RGB value of a pixel.
- *In NLP:* The **numerical representation of a word**.

### Definition: What is Feature Extraction?
The process of transforming **raw text** into a **numerical representation** (vectors/matrices) that computers can process mathematically.

---

## 2. 📊 Lexical Feature Extraction Techniques

### 2.1 One Hot Encoding
**Mechanism:** Generates a vector of boolean values.
- Map each word to a unique ID.
- Vector dimension = Size of vocabulary (`V`).
- Vector is all `0`s except for a `1` at the word's index.

**Example:**
- **Vocab:** `['dog', 'bites', 'man']`
- **Sent 1:** `'dog bites man'` → `[[1,0,0], [0,1,0], [0,0,1]]`
- **Sent 2:** `'man bites dog'` → `[[0,0,1], [0,1,0], [1,0,0]]`

**❌ Drawbacks:**
- **Scalability:** Vector size grows with vocabulary (high memory usage).
- **No Semantics:** No relationship between words (e.g., "dog" and "puppy" are as different as "dog" and "car").
- **Sparsity:** Most computations are wasted on zeros.

---

### 2.2 Bag of Words (BOW)
**Mechanism:** Counts the frequency of each word in the document. Order is ignored (hence "bag").

**Example:**
- **Vocab:** `['the', 'cat', 'sat', 'on', 'hat', 'dog', 'ate', 'and']`
- **Sent 1:** `'the cat sat on the hat'` → `{2, 1, 1, 1, 1, 0, 0, 0}`
- **Sent 2:** `'the dog ate the cat and the hat'` → `{3, 1, 0, 0, 1, 1, 1, 1}`

**❌ Drawbacks:**
- **No Semantic Relationship:** Still blind to meaning.
- **Frequency Bias:** Common words (like "the") get high weight but are not useful for differentiating documents.
- **Curse of Dimensionality:** Sparse vectors with high dimensions make distance metrics less meaningful.

---

### 2.3 Bag of N-grams
**Mechanism:** Same as BOW, but the "tokens" are contiguous sequences of `N` words (phrases).
- *Bigram:* 2 words (`the cat`)
- *Trigram:* 3 words (`cat sat on`)

**Example (Bigrams):**
- **Vocab:** `['the cat', 'cat sat', 'sat on', 'on the', 'the hat', 'the dog', 'dog ate', 'ate the', 'cat and', 'and the']`
- **Sent 1:** `'the cat sat on the hat'` → `{1, 1, 1, 1, 1, 0, 0, 0, 0, 0}`
- **Sent 2:** `'the dog ate the cat and the hat'` → `{1, 0, 0, 0, 0, 1, 1, 1, 1, 1}`

**❌ Drawbacks:**
- **Exploding Vocabulary:** The feature set becomes massive very quickly.
- **Syntactic Blindness:** No notion of grammar or semantic equivalence.

```Python
from sklearn.feature_extraction.text import CountVectorizer
import pandas as pd

corpus = ['the cat sat on the hat', 'the dog ate the cat']

# ngram_range=(1, 2) includes both single words and word pairs
cv = CountVectorizer(ngram_range=(1, 2))
x = cv.fit_transform(corpus)

# Convert sparse matrix to dense DataFrame for viewing
df_bow = pd.DataFrame(x.toarray(), columns=cv.get_feature_names_out())
```
---

### 2.4 TF-IDF (Term Frequency-Inverse Document Frequency)
**Mechanism:** Captures the *importance* of a word to a document *within a corpus*.
- **TF (Term Frequency):** How often a word appears in a document.
- **IDF (Inverse Document Frequency):** How rare/common the word is across all documents.
- **Goal:** Increases weight for words that are **frequent in this document** but **rare in the whole corpus**.

**Formula Concept:**
`Weight = TF * log(N / DF)`
- `N` = Total number of documents
- `DF` = Number of documents containing the word

**❌ Drawbacks:**
- Relies on BOW model (ignores word order).
- Lacks semantic understanding (cannot tell that "car" and "automobile" are related).
- Only useful as a **lexical level feature**.

``` python
from sklearn.feature_extraction.text import TfidfVectorizer

tfidf = TfidfVectorizer()
x_tfidf = tfidf.fit_transform(corpus)
df_tfidf = pd.DataFrame(x_tfidf.toarray(), columns=tfidf.get_feature_names_out())
```
---

## 3. 📏 Text Similarity

### Lexical vs. Semantic Similarity
| Type | Definition | Example |
| :--- | :--- | :--- |
| **Lexical** | Share common character sequences/words. | D1: "This is my girl"<br>D2: "This is my girl friend" |
| **Semantic** | Share the same meaning (different words). | D1: "The bottle is empty"<br>D2: "There is nothing in the bottle" |

### Cosine Similarity
Measures the angle between two vectors. Used to compare document vectors in BOW or TF-IDF space.
- **1.0** = Identical direction (very similar)
- **0.0** = Orthogonal (no similarity)

### 🔬 Comparison Experiment (CountVectorizer vs TF-IDF)
- **Corpus:**
    1. 'the weather is hot under the sun'
    2. 'I make my hot chocolate with milk'
    3. 'one hot encoding'
    4. 'I will have a chai latte with milk'
    5. 'there is a hot sale today'

- **CountVectorizer Result:** Matched Doc 1 & Doc 3 due to common word **"hot"** (high frequency, low value word).
- **TF-IDF Result:** Matched Doc 2 & Doc 4 due to the rare word **"milk"** (high importance).

``` python
from sklearn.metrics.pairwise import cosine_similarity
from numpy import dot
from numpy.linalg import norm

# Manual Calculation
# cosine = dot(v1, v2) / (norm(v1) * norm(v2))

# Scikit-learn (Automated for matrices)
similarity_matrix = cosine_similarity(x_tfidf)
```

---

## 4. 🧠 The Problem with Legacy Techniques & Introduction to Word2Vec

### The "Seattle Motel" Problem
- **Search Query:** "Seattle motel"
- **Desired Match:** Document containing "Seattle hotel"
- **Reality (One-Hot):**
    - `motel` = `[0 0 0 0 0 0 0 0 0 0 1 0 0 0 0]`
    - `hotel` = `[0 0 0 0 0 0 0 1 0 0 0 0 0 0 0]`
- **Result:** Vectors are orthogonal (Cosine Similarity = 0). **No similarity found.**

### The Solution: Distributed Representations
**Core Idea:** "A word is characterized by the company it keeps" (Firth, 1957).
- Learn to encode similarity **directly in the vector space**.
- Words appearing in similar contexts should have similar vectors.

---

## 5. 🚀 Word2Vec Architecture

Word2Vec is a **framework** (not a deep network itself) for learning word vectors. It turns text into numerical form usable by neural networks.

### 5.1 Continuous Bag of Words (CBOW)
- **Task:** Predict the **target word** given the **context words** (neighbors within a window).
- *Example (Window Size 2):* Context = `['the', 'cat', 'on', 'the']` → Predict `'sat'`
- **Drawback:** Struggles to capture good representations for **rare words** because it averages contexts.

### 5.2 Skip-gram
- **Task:** Predict the **context words** given a **target word**.
- *Example (Window Size 2):* Target = `'sat'` → Predict `'the', 'cat', 'on', 'the'`
- **✅ Advantages:**
    - **Rare Words:** Works much better for infrequent words.
    - **Semantic Capture:** Synonyms like "intelligent" and "smart" will have very similar context predictions, thus similar vectors.

``` python
from gensim.models import Word2Vec

# data: A list of lists where each inner list is a tokenized sentence
# sg=0 for CBOW, sg=1 for Skip-gram
model = Word2Vec(data, vector_size=100, window=5, min_count=1, sg=0)

# Extracting similarity
word_sim = model.wv.similarity('alice', 'wonderland')
most_similar = model.wv.most_similar('alice', topn=5)
```
---

## 6. 📊 Comparative Analysis: Technique Cheat Sheet

### 6.1 Feature Comparison Matrix

| Technique | Captures Word Order? | Captures Semantics? | Vector Sparsity | Vocabulary Size Impact | Best Use Case |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **One Hot Encoding** | ❌ No | ❌ No | **Very High** (Mostly 0s) | **High** (Dim = Vocab Size) | Baseline for single characters/words in small vocab (not practical for text). |
| **Bag of Words (BOW)** | ❌ No | ❌ No | **High** | **High** (Dim = Vocab Size) | Document classification where topic words are strong indicators (e.g., Spam vs. Ham). |
| **N-Grams** | ✅ **Yes** (Local) | ❌ No | **Very High** | **Extremely High** | Sentiment Analysis (captures phrases like "not good"). |
| **TF-IDF** | ❌ No | ❌ No | **High** | **High** (Dim = Vocab Size) | Information Retrieval / Search Engines (finding relevant documents by keywords). |
| **Word2Vec (CBOW)** | N/A | ✅ **Yes** (Contextual) | **Low** (Dense) | **Low** (Fixed Dim e.g., 100-300) | Fast training on large corpus; frequent words. |
| **Word2Vec (Skip-gram)** | N/A | ✅ **Yes** (Contextual) | **Low** (Dense) | **Low** (Fixed Dim e.g., 100-300) | **Small datasets** or capturing **rare words**. |

### 6.2 When to Use What? (Decision Flow)

```mermaid
graph TD
    A[Start: Need to vectorize text] --> B{Semantic Meaning Important?};
    B -- No (Lexical only) --> C{Specific Phrases important?};
    B -- Yes (Need word relationships) --> D[Use Word Embeddings];
    
    C -- Yes --> E[**N-Grams**];
    C -- No --> F{Differentiating rare keywords?};
    
    F -- Yes --> G[**TF-IDF**];
    F -- No (Just count frequencies) --> H[**Bag of Words**];
    
    D --> I{Large Data & Rare Words?};
    I -- Yes --> J[**Skip-gram**];
    I -- No (Small/Fast) --> K[**CBOW**];

