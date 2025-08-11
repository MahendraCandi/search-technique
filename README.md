# Search Technique
Learning some technique to queries data.

# Challenges
#### 1. Exact match requirement
The search will only return results that perfectly match the user’s query, 
character for character (sometimes including letter case, spacing, punctuation).

#### 2.Flexibility
The search can tolerate variations, typos, and different formats 
so the user can still find what they’re looking for.

#### 3. Scalability
The search system can handle growth in data size, query volume, 
and complexity without slowing down or failing.

#### 4. Contextual understanding
The search engine understands the intent behind the query, not just the literal words.

# Fuzzy Search
A search technique that **finds matches even when the query isn’t exactly 
the same as the stored data**, often by allowing for typos, misspellings, 
and small differences in wording.

> It’s like the search engine saying: “I think you meant this…” instead of “No results found.”

## How it works

### 1. Levenshtein Distance

Check this article for good explanations: 
[Understanding the Levenshtein Distance Equation for Beginners](https://medium.com/@ethannam/understanding-the-levenshtein-distance-equation-for-beginners-c4285a5604f0).
The summary is, to calculating the number distance between two string by comparing index position and the character self.

The return is a "distance number" means, the total number a string could be transformed to another string by doing 
insertion, delete, or replacement. 

### 2. Soundex

Algorithm that change word into a code by using it sound. 
Soundex is originally only use for English language.

#### A. Letter-to-Digit Mapping

| Letters                | Code                  |
|------------------------|-----------------------|
| B, F, P, V             | 1                     |
| C, G, J, K, Q, S, X, Z | 2                     |
| D, T                   | 3                     |
| L                      | 4                     |
| M, N                   | 5                     |
| R                      | 6                     |
| A, E, I, O, U, Y, H, W | (ignored / not coded) |

#### B. Soundex: Classic Russell vs NARA

| Name         | Steps (Key Difference Points)                                                                              | Classic Russell Output | NARA Output                     |
|--------------|------------------------------------------------------------------------------------------------------------|------------------------|---------------------------------|
| **Tymczak**  | C(2) and Z(2) collapse, K(2) blocked by collapse rule in Classic; vowel in NARA allows K(2) to count again | **T520**               | **T522**                        |
| **Ashcraft** | S(2) and C(2) collapse across H in Classic; in NARA, H is treated as a separator so C’s code is kept       | **A261**               | **A262**                        |
| **Jackson**  | C(2) and K(2) collapse in Classic; vowel O allows K(2) again in NARA                                       | **J250**               | **J252**                        |
| **Pfister**  | F(1) and S(2) unaffected; vowel in NARA can allow repeated codes in other cases                            | **P236**               | **P236** *(no difference here)* |
| **Robert**   | No collapse differences; both rules same                                                                   | **R163**               | **R163**                        |
| **Euler**    | Vowel handling doesn’t cause code duplication; both same                                                   | **E460**               | **E460**                        |

#### C. Main Differences Between the Two Variants

| Rule Aspect                   | Classic Russell                                                            | NARA (U.S. Census)                                                                      |
|-------------------------------|----------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| **Vowels (A, E, I, O, U, Y)** | Ignored completely; *do not* break runs of same code digits                | Ignored but **break runs** — same digit can appear again after a vowel                  |
| **H and W**                   | Ignored completely; *do not* break runs                                    | Treated as **separators** — same digit can appear again after them                      |
| **Duplicate collapsing**      | Collapses codes for adjacent letters if same digit, even across vowels/H/W | Collapses only if truly adjacent in the consonant sequence (not separated by vowel/H/W) |


#### D. Step-by-Step Example: "Tymczak"

**Original name**: `Tymczak`  
**First letter kept**: `T`

##### - Classic Russell Soundex
1. Remove first letter: remaining = `y m c z a k`
2. Map to digits:
    - `y` → ignored
    - `m` → 5
    - `c` → 2
    - `z` → 2
    - `a` → ignored
    - `k` → 2  
      Sequence = `5 2 2 2`
3. Collapse adjacent duplicates regardless of vowels/H/W:
    - `5 2 2 2` → `5 2`
4. Pad to three digits: `5 2 0`
5. Final code: **T520**

##### - NARA Soundex
1. Remove first letter: remaining = `y m c z a k`
2. Map to digits:
    - `y` → ignored (breaks runs)
    - `m` → 5
    - `c` → 2
    - `z` → 2
    - `a` → ignored (breaks runs)
    - `k` → 2  
      Sequence = `5 2 2 2`
3. Collapse only truly adjacent duplicates (separated by vowels/H/W allowed to repeat):
    - First `c(2)` and `z(2)` are collapsed → `5 2`
    - After vowel `a`, `k(2)` is allowed again → `5 2 2`
4. No padding needed
5. Final code: **T522**

TODO NEXT...

### Metaphone