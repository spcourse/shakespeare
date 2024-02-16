# Data structures

The goal of this part is to get more familiar with the Python datastructures `set` and `dictionary`.

Create a file called `datastructures.py` and implement the functions below.

### 1. Lemmas

Create a function named `count_lemmas(text, lemmas)`. The function should count the occurrences of each `lemma` (base form of a word) in the given `text` and return the result as a dictionary.

> A lemma is a base form of a word that serves as a canonical representation of a set of words that have similar meanings. For example, the lemma of "run," "running," and "ran" is "run." In the given code, the lemmas parameter is a dictionary that maps the variations of a word to its lemma.

The text parameter is a string containing the text to be analyzed. The function should split (use `.split()`) the text into words; remove any punctuation marks (use `.strip()`); and convert to lowercase (`.lower()`). If the word exists in the `lemmas` dictionary, count the lemma, not the word itself.

You can use the function `tokenize(text)` below to convert the input text into a list of words:

    def tokenize(text):
        return [word.strip(" ,\n.);(!?)'").lower() for word in text.split(' ')]

Example usage:

    text = "He is, was and will be a runner. And runners ran, run and will always run. This is their nature."

    lemmas = {
        "runners": "runner",
        "ran": "run",
        "was": "be",
        "is": "be"
    }

    print(count_lemmas(text, lemmas))

Expected output:

    {'he': 1, 'be': 4, 'and': 3, 'will': 2, 'a': 1, 'runner': 2, 'run': 3, 'always': 1, 'this': 1, 'their': 1, 'nature': 1}


### 2. Grammatical categories

Create a function called `count_category(lemma_counts, category)`. This function counts the occurances of lemmas of a specific grammatical category (for example verbs or nouns). The input is `lemma_counts`, a dictionary of lemma counts like you created in the last assignment, and `category` a set of words representing a grammatical category.

(If you didn't finish the previous assignment, no worries, the correct `lemma_counts` dictionary is provided below.)

Example usage:

    lemma_counts = {'he': 1, 'be': 4, 'and': 3, 'will': 2, 'a': 1, 'runner': 2, 'run': 3, 'always': 1, 'this': 1, 'their': 1, 'nature': 1}
    nouns = {'runner', 'nature', 'building'}
    verbs = {'walk', 'run', 'be'}
    determiners = {'the', 'a', 'this', 'their'}

    print(count_category(lemma_counts, nouns))
    print(count_category(lemma_counts, verbs))
    print(count_category(lemma_counts, determiners))

Expected output:

    3
    7
    3


### 3. Library

We have loaded information about book genres into a dictionary called `library`. The dictionary has the titles of the books as keys and the genres as values (see usage example below). We would like to group the titles by genre. Write a function called `group_titles_by_genre(library)`, that takes the dictionary and outputs a new dictionary where each key is a genre and each value is a list of all titles that belong to the given genre.

Example usage:

    library = {"Life of Pi": "Adventure",
               "One World The Water Dancer": "Fantasy",
               "The Three Musketeers": "Adventure",
               "To Kill a Mockingbird": "Classics",
               "Circe": "Fantasy",
               "The Call of the Wild": "Adventure",
               "Little Women": "Classics"}

    grouped = group_titles_by_genre(library)
    print(grouped)

Expected output:

    {'Adventure': ['Life of Pi', 'The Three Musketeers', 'The Call of the Wild'], 'Fantasy': ['One World The Water Dancer', 'Circe'], 'Classics': ['To Kill a Mockingbird', 'Little Women']}


### 4. Unify

The first function is `unify(dict1, dict2)`. It combines two dictionaries. It should return a dictionary that contain all items (key-value pairs) from both dictionaries. If a key occurs in both input dictionaries, unify the corresponding lists. The value lists of the output should remain sorted and not contain any duplicates.

Example usage:

    dict1 = {"a": [1, 2, 3], "c": [4, 5, 6], "d": [6]}
    dict2 = {"a": [1, 3, 4], "b": [9], "c": [2, 4]}
    print(unify(dict1, dict2))

Expected output:

    {'d': [6], 'a': [1, 2, 3, 4], 'c': [2, 4, 5, 6], 'b': [9]}

### 5. Melt

Implement the function `melt(dict)`. It should return a list of tuples by unpacking the lists of values. There should be a tuple for each for each combination of key and value as they appear in the value lists of the input dictionary.

Example usage:

    dict1 = {"a": [1, 2, 3], "c": [4, 5, 6], "d": [6]}
    print(melt(dict1))

Expected output:

    [('a', 1), ('a', 2), ('a', 3), ('c', 4), ('c', 5), ('c', 6), ('d', 6)]

### 6. N-intersection

With sets it's relatively easy to compute the intersection between two sets (i.e., create a set with the items that are in both input sets). But it can be useful to compute the intersection between not just two, but an arbitrary number of input sets. Write a function called `n_intersection(sets)`, that takes as input a list of sets (`sets`) and returns the intersection of all of those sets.

Have a look a this example:

    ex1 = n_intersection([{5, 9, 6}, {9, 2, 6}, {6, 5, 9}])
    ex2 = n_intersection([{"kerfuffle", "hullaballoo", "ragamuffin", "flummox"},
                          {"kerfuffle", "ragamuffin", "gobbledygook", "flummox"},
                          {"hullaballoo", "ragamuffin", "gobbledygook", "flummox"},
                          {"hullaballoo", "ragamuffin", "ragamuffin", "gobbledygook", "flummox"}])
    ex3 = n_intersection([])

    print(ex1)
    print(ex2)
    print(ex3)

This should give the following output:

    {9, 6}
    {'ragamuffin', 'flummox'}
    set()

> Tip: `set()`, just means an empty set. This cannot be written as `{}` because that's already used in python for an empty dictionary.

### 7. Sentiment

Write a Python function called `sentiment_of_text(text, sentiment_of_word)` that takes a text string and a dictionary of word sentiments as input and returns the overall sentiment score of the text. The sentiment score is calculated as the sum of sentiment scores for individual words in the text using the provided `sentiment_of_word` dictionary. You may assume that words that cannot be found in the dictionary have a sentiment score of 0. You can use the `tokenize()` function of the first assignment.

This is the `sentiment_of_word` dictionary:

    sentiment_of_word = {
        "abysmal": -5, "agonizing": -4, "dreadful": -5, "grim": -3, "horrible": -5,
        "miserable": -4, "terrible": -5, "upset": -3, "unpleasant": -2, "woeful": -4,
        "horrific": -5, "disastrous": -5, "appalling": -5, "repulsive": -4, "noxious": -3,
        "atrocious": -5, "vile": -4, "wretched": -3, "deplorable": -5, "abominable": -5,
        "amazing": 5, "fantastic": 4, "joyful": 4, "excellent": 5, "delightful": 4,
        "wonderful": 5, "terrific": 4, "great": 3, "awesome": 5, "superb": 4,
        "fabulous": 4, "incredible": 5, "marvelous": 4, "phenomenal": 5, "outstanding": 4,
        "brilliant": 5, "spectacular": 4, "splendid": 3, "glorious": 4, "fantabulous": 5
    }

As you can see, negative words have negative scores and positive words have positive scores.


Example usage:

    text1 = "Wow, what an amazing day it has been! The weather is fantastic!"
    text2 = "Today has been abysmal. The weather is dreadful, and I feel miserably upset."

    print(sentiment_of_text(text1, sentiment_of_word))
    print(sentiment_of_text(text2, sentiment_of_word))

Expected output:

    9
    -13


## Test

You can verify your results with:

checkpy datastructures
