#  NLP Lab 2: Text Preprocessing & RegEx Notes

## 1. Environment Setup (Conda)
Before starting NLP tasks, it is best practice to create an isolated environment.
* **Create Environment**: `conda create -n nlp_env python=3.6`
* **Activate Environment**: `conda activate nlp_env`
* **Install NLTK**: `pip install nltk==3.5`

---

## 2. Natural Language Toolkit (NLTK)
* **Definition**: An open-source Python package for Natural Language Processing (NLP).
* **Core Capabilities**: Provides interfaces to over 50 corpora and lexical resources (like **WordNet**) and libraries for tokenization, stemming, tagging, and parsing.

---

## 3. Tokenization
The process of breaking down unstructured text into smaller, manageable pieces called **tokens**.

* **Sentence Tokenization**: Breaks text into individual sentences. This is useful for maintaining context and understanding the relationship between words.
* **Word Tokenization**: Breaks sentences into individual words/punctuation. These are the "atoms" of NLP, used to identify word frequency.
* **NLTK Logic**: 
    * Recognizes contractions (e.g., "It's" becomes `'It'` and `"'s"`).
    * Handles special names (e.g., "Muad'Dib") as single tokens.



---

## 4. Filtering Stop Words
Stop words are common words (like 'in', 'the', 'is') that are often filtered out to focus on the "content" of the text.

* **Casefolding**: Always use `.casefold()` on words before comparing them to the stop word list, as NLTK's list is lowercase.
* **Content vs. Context**:
    * **Content Words**: Provide information about the **topic** or sentiment.
    * **Context Words**: (e.g., "I", "our") Reveal writing style. Keeping these is essential for **Stylometry** (identifying an author's "fingerprint").
* **Critical Filter Note**: Filtering words like "not" can accidentally invert the sentiment of a sentence (e.g., "not happy" becomes "happy").

---

## 5. Stemming & Lemmatizing
Both techniques reduce words to their base forms, but they use different logic.

| Feature | Stemming | Lemmatizing |
| :--- | :--- | :--- |
| **Logic** | Chops off suffixes (Heuristic) | Dictionary Lookup (Morphological) |
| **Result** | Often a fragment (e.g., `discoveri`) | A real word (e.g., `discovery`) |
| **Algorithm** | **Porter Stemmer** (1979) | **WordNet Lemmatizer** |
| **Accuracy** | Prone to Over/Understemming | High accuracy with POS tags |



### Key Concepts:
* **Overstemming**: Unrelated words reduced to the same stem (False Positive).
* **Understemming**: Related words left as different stems (False Negative).
* **POS Tagging**: For Lemmatization to be accurate, you must provide the Part of Speech (e.g., `pos='a'` for adjectives to turn "worst" into "bad").

---

## 6. Regular Expressions (RegEx)
Used for pattern matching, data cleaning, and web scraping. In Python, use the `re` module.

### Common Python Methods:
* `re.match(pattern, string)`: Checks for a match only at the **beginning** of the string.
* `re.search(pattern, string)`: Searches the **entire** string for a match.
* `re.findall(pattern, string)`: Returns a **list** of all matches found.

### Essential Sequences & Metacharacters:
* **`r""`**: Raw string literal. Essential for RegEx so backslashes (`\`) aren't treated as escape characters.
* **`\d+`**: Matches one or more digits.
* **`\w`**: Matches any alphanumeric character (A-Z, 0-9) and underscore (`_`).
* **`.`**: Matches any character except a newline.
* **`^`**: Matches the start of a string.
* **`$`**: Matches the end of a string.
* **`*`**: Matches 0 or more occurrences.
* **`+`**: Matches 1 or more occurrences.
