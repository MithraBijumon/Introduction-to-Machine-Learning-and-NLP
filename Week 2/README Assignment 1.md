# A comparison of word scores between your manual TF-IDF, CountVectorizer, and TfidfVectorizer
```
corpus = [
    'the sun is a star',
    'the moon is a satellite',
    'the sun and moon are celestial bodies'
]
```

## CountVectorizer
|     | and | are | bodies | celestial | is | moon | satellite | star | sun | the |
|-----|-----|-----|--------|-----------|----|------|-----------|------|-----|-----|
|  0  |  0  |  0  |   0    |     0     | 1  |  0   |     0     |  1   |  1  |  1  |
|  1  |  0  |  0  |   0    |     0     | 1  |  1   |     1     |  0   |  0  |  1  |
|  2  |  1  |  1  |   1    |     1     | 0  |  1   |     0     |  0   |  1  |  1  |

## TfidfVectorizer
|     |   and   |   are   |  bodies  | celestial |    is   |   moon  | satellite |  star  |   sun   |   the   |
|-----|---------|---------|----------|-----------|---------|---------|-----------|--------|---------|---------|
|  0  | 0.000000| 0.000000| 0.000000 | 0.000000  | 0.480458| 0.000000| 0.000000  | 0.631745| 0.480458| 0.373119|
|  1  | 0.000000| 0.000000| 0.000000 | 0.000000  | 0.480458| 0.480458| 0.631745  | 0.000000| 0.000000| 0.373119|
|  2  | 0.426184| 0.426184| 0.426184 | 0.426184  | 0.000000| 0.324124| 0.000000  | 0.000000| 0.324124| 0.251711|

## Manual TF-IDF
|     |  bodies  |   sun   | satellite |  star   | the |  moon   |  are   |   a    | celestial |   is    |  and   |
|-----|----------|---------|-----------|---------|-----|---------|--------|--------|-----------|---------|--------|
|  0  | 0.000000 | 0.081093| 0.000000  | 0.219722| 0.0 | 0.000000| 0.000000| 0.081093| 0.000000  | 0.081093| 0.000000|
|  1  | 0.000000 | 0.000000| 0.219722  | 0.000000| 0.0 | 0.081093| 0.000000| 0.081093| 0.000000  | 0.081093| 0.000000|
|  2  | 0.156945 | 0.057924| 0.000000  | 0.000000| 0.0 | 0.057924| 0.156945| 0.000000| 0.156945  | 0.000000| 0.156945|

# An explanation for why the scores for common words (like 'the') differ significantly between the methods.
## CountVectorizer
- Converts the corpus into a document-term matrix using raw word counts.
- Every word is treated equally — even extremely frequent ones like "the" get the same importance.
## TfidfVectorizer (from Scikit-learn)
TF (Term Frequency): It uses L2 normalization of the TF-IDF vector (i.e., the whole row is normalized so the sum of squares = 1). Thus, even if the raw TF-IDF is same, the vector is rescaled in sklearn to prevent longer documents from dominating.
IDF (Inverse Document Frequency): log((1 + N) / (1 + df)) + 1 where: N is the number of documents, df is the number of documents containing the word. This is called "smoothing", and it helps prevent division by zero and gives every term a minimum IDF value > 0.
This makes frequent terms (like "the") less penalized in scikit-learn than in the raw formula. That's why scikit-learn gives "the" an IDF of 1.0 instead of ∞ or a huge number.
- Automatically computes TF-IDF values based on term frequencies and inverse document frequencies.
- Downweights common words like "the".
## Manual TF-IDF
TF (Term Frequency): Number of times a word appears in a document divided by the total number of words in that document.
IDF (Inverse Document Frequency): log(N / df) where: N is the number of documents, df is the number of documents containing the word.
For each term in the vocabulary, I computed: TF-IDF = TF * IDF
The vocabulary was manually built using set() on the entire corpus
## Why are common words like "the" weighted lower in TF-IDF?
- Common words appear in nearly every document.
- Their IDF becomes close to zero:
    idf("the") = log(3 / 3) = log(1) = 0
- So, TF-IDF score for "the" is near zero, because such words don’t help differentiate between documents.
- In contrast, CountVectorizer gives equal importance to all words, regardless of their usefulness.
