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

### Metaphone

From Wikipedia, Metaphone is algorithm improved from Soundex. Metaphone is used not limit to only human names (like Soundex),
but for English words. Double Metaphone is second version that spelling peculiarities of number of other language.

Same like Soundex, orignally, Metaphone has procedures to generate code from words.
These are procedure described from Wikipedia, but this is not complete implementations:

1. Drop duplicate adjacent letters, except for 'C'.
2. If the word begins with 'KN', 'GN', 'PN', 'AE', or 'WR', drop the first letter.
3. Drop 'B' if it comes after 'M' at the end of the word.
4. Transform 'C' to 'X' if followed by 'IA' or 'H' (unless part of '-SCH-', then transform to 'K'); to 'S' if followed by 'I', 'E', or 'Y'; otherwise, to 'K'.
5. Transform 'D' to 'J' if followed by 'GE', 'GY', or 'GI'; otherwise, to 'T'.
6. Drop 'G' if followed by 'H' and 'H' is not at the end or before a vowel.
7. Drop 'G' if followed by 'N' or 'NED' and is at the end.
8. Transform 'G' to 'J' if before 'I', 'E', or 'Y', and not part of 'GG'; otherwise, to 'K'.
9. Drop 'H' if after a vowel and not before a vowel.
10. Transform 'CK' to 'K'.
11. Transform 'PH' to 'F'.
12. Transform 'Q' to 'K'.
13. Transform 'S' to 'X' if followed by 'H', 'IO', or 'IA'.
14. For 'T': transform to 'X' if followed by 'IA' or 'IO'; transform 'TH' to '0'; drop 'T' if followed by 'CH'.
15. Transform 'V' to 'F'.
16. Transform 'WH' to 'W' if at the beginning; drop 'W' if not followed by a vowel.
17. Transform 'X' to 'S' if at the beginning; otherwise, to 'KS'.
18. Drop 'Y' if not followed by a vowel.
19. Transform 'Z' to 'S', and drop all vowels unless they are the first letter.  

This is the procedures generated by GPT:

1. **Normalize the input**
    - Convert all letters to uppercase.
    - Remove non-alphabetic characters.

2. **Remove silent letters**
    - Drop leading `KN`, `GN`, `PN`, `WR`, `PS`.
    - Drop other silent letters such as `B` after `M` at the end, silent `GH`, etc.

3. **Apply letter-to-sound rules**
    - Replace letter combinations based on common English pronunciation patterns.
    - Examples:
        - `PH` → `F`
        - `SH` → `X`
        - `TH` → `0` (zero-like symbol in some implementations)
        - `CIA` → `X`
        - `CH` → `X` or `K` depending on context.

4. **Handle vowels**
    - Keep the first letter if it’s a vowel.
    - Remove other vowels unless they affect pronunciation in certain contexts.

5. **Process consonants**
    - Collapse duplicate adjacent consonants (except `C` in some cases).
    - Apply special-case handling for letters like `C`, `G`, `S`, `T` depending on following vowels or consonants.

6. **Output the code**
    - The result is a phonetic code that represents how the word sounds rather than how it’s spelled.
    - The output length can be fixed (e.g., 4 characters) or variable depending on implementation.

### Example 1
- **Word**: `KNIFE`
- Step 2: Begins with "KN" → drop first letter → `NIFE`
- Step 19: Drop vowels except first → `NF`
- Final Metaphone code: `NF`

### Example 2
- **Word**: `TOM`
- Step 3: 'B' rule not applied → no change
- Step 19: Drop vowels except first → `TM`
- Final Metaphone code: `TM`

### Example 3
- **Word**: `CATHERINE`
- Step 4: 'C' before 'A' → transform to `KATHERINE`
- Step 9: 'H' after a vowel and not before a vowel → drop 'H' → `KATERINE`
- Step 19: Drop vowels except first → `KTRN`
- Final Metaphone code: `KTRN`


### N-Grams

A technique by dividing words per-N letter or sentence per-N words.
In search technique, commonly divide the words by N letter.\
Example, search text with word "Engineer"
* 1 Gram (Monogram) will divide the word into 1 letter. The result will be "e", "n", "g", "i", "n", "e", "e", "r"
* 2 Gram (Bigram) divide word into 2 letter. "en", "ng", "gi", "in", "ne", "ee", "er"
* 3 Gram (Trigram) **The most common use**, divide into 3 letter. "eng", "ngi", "gin", "ine", "nee", "eer".\
    In Postgresql trigram (pg_trigram) the result has additional blank space "  e", " en", "er ", "r  "
* 4 Gram divide into 4 letter, "engi", "ngin", "gine", "inee", etc...
* 5 Gram divide into 5 letter, "engin", "ngine", etc...


# Postgres Trigram

> official documentation https://www.postgresql.org/docs/current/pgtrgm.html

From the documentation, consider following comparison: 

    SELECT word_similarity('word', 'two words');

In the first string, the set of trigrams is {" w"," wo","wor","ord","rd "}.\
In the second string, the ordered set of trigrams is {" t"," tw","two","wo ",
" w"," wo","wor","ord","rds","ds "}.\
The most similar extent of an ordered set of trigrams in the second string is 
{" w"," wo","wor","ord"}, and the similarity is 0.8.

This is not about exact match like conventional DB, it is about relevant.

## Postgres pg_trgm in action

1. First download and install Postgres
2. Download and insert the dataset on https://github.com/neondatabase-labs/postgres-sample-dbs?tab=readme-ov-file#employees-database
3. Create postgres pg_trgm extension

```
create extension IF NOT EXISTS pg_trgm;
```

## Threshold point

The default threshold point that used by pg_trgm to find relevant value is 0.3.
Please check this https://www.postgresql.org/docs/current/pgtrgm.html#PGTRGM-GUC.

so if, the similarity point is greater than 0.3, then it would be return as results.

```
-- noinspection SqlType
select e.id, (e.first_name || ' ' || e.last_name || ' ' || t.title) as Field,
       show_trgm((e.first_name || ' ' || e.last_name || ' ' || t.title)) as record_trigram,
       show_trgm('Geor facel eng''') as search_trigram,
       similarity((e.first_name || ' ' || e.last_name || ' ' || t.title), 'Geor facel eng')
from employee e
left join title t on e.id = t.employee_id
where (e.first_name || ' ' || e.last_name || ' ' || t.title) % 'Geor facel eng'
limit 10;
```

The result is:

| id    | field                          | record\_trigram                                                                                                                                       | search\_trigram                                                                 | similarity |
|:------|:-------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------|:-----------|
| 10001 | Georgi Facello Senior Engineer | {"  e","  f","  g","  s"," en"," fa"," ge"," se",ace,cel,eer,ell,eng,eni,eor,"er ",fac,geo,"gi ",gin,ine,ior,llo,"lo ",nee,ngi,nio,"or ",org,rgi,sen} | {"  e","  f","  g"," en"," fa"," ge",ace,cel,"el ",eng,eor,fac,geo,"ng ","or "} | 0.3939394  |
| 55649 | Georgi Facello Senior Engineer | {"  e","  f","  g","  s"," en"," fa"," ge"," se",ace,cel,eer,ell,eng,eni,eor,"er ",fac,geo,"gi ",gin,ine,ior,llo,"lo ",nee,ngi,nio,"or ",org,rgi,sen} | {"  e","  f","  g"," en"," fa"," ge",ace,cel,"el ",eng,eor,fac,geo,"ng ","or "} | 0.3939394  |
| 60558 | Gor Georg Engineer             | {"  e","  g"," en"," ge"," go",eer,eng,eor,"er ",geo,gin,gor,ine,nee,ngi,"or ",org,"rg "}                                                             | {"  e","  f","  g"," en"," fa"," ge",ace,cel,"el ",eng,eor,fac,geo,"ng ","or "} | 0.32       |
| 89589 | Fuqing Facello Engineer        | {"  e","  f"," en"," fa"," fu",ace,cel,eer,ell,eng,"er ",fac,fuq,gin,ine,ing,llo,"lo ",nee,"ng ",ngi,qin,uqi}                                         | {"  e","  f","  g"," en"," fa"," ge",ace,cel,"el ",eng,eor,fac,geo,"ng ","or "} | 0.31034482 |

because `similarity` function will do: 
> Returns a number that indicates how similar the two arguments are. 
> The range of the result is zero (indicating that the two strings are completely dissimilar) 
> to one (indicating that the two strings are identical).

> #### This same function!
> * `similarity((e.first_name || ' ' || e.last_name || ' ' || t.title), 'Geor facel eng')`
> * `1.0 - ((e.first_name || ' ' || e.last_name || ' ' || t.title) <-> 'Geor facel eng') > 0.3`
> * `(e.first_name || ' ' || e.last_name || ' ' || t.title) % 'Geor facel eng'`