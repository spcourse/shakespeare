# Simple Shakespeare classifier
The aim is to program a classifier capable of deciding if text fragments are written by Shakespeare. This task involves a set of words characteristic of Shakespearean writing to categorize the fragments.

This classifier has a straightforward design, based on the frequency of word overlaps between the text fragments and the pre-generated Shakespearean word list.

For instance, let's consider the following sequence of words as representative of Shakespearean language:

_wassail, halcyon, cannoneer, darnel, beggarly, dispraise_

With this list, we can evaluate the following text and assign it a score based on the relative occurrence of Shakespearean words. Here is the example text:

"In the **halcyon** days of yore, we reveled amidst fields of golden **darnel**, a **beggarly** intrusion among nature's grandeur. The mirthful melodies of the **wassail** danced upon the breeze, while **beggarly** worries scattered like autumn leaves."

This text would yield a _Shakespeare score_ of approximately $$0.139$$, as it contains 4 distinct words from the Shakespearean word list (note that *beggarly* appears twice, but multiple occurrences are not counted at this stage). The total count of unique words in the text is $$29$$, yielding a score of $$4/29 \approx 0.139$$.

Assuming that higher scores indicate a higher likelihood of Shakespearean authorship, while lower scores indicate the opposite, we can formulate a simple classification mechanism. This involves setting a threshold (e.g., $$0.02$$). For all texts with a higher score than this threshold, we predict them as having been written by Shakespeare. So the provided text would be positively classified (as Shakespearean) because it has score higher score than $$0.02$$ (i.e., $$0.139$$).

This algorithm will be implemented through several phases. Initially (phase 1), a precomputed compilation of Shakespearean words will be provided. The task involves using this compilation to calculate the _Shakespeare score_ for a given text and then categorizing it based on a designated threshold. After this (phase 2), you will write an algorithm to generate the list of Shakespearean words from a set of provided text fragments.

## Specification Phase 1

The process for classifying text fragments involves the following sequence of steps:

0. Download the data for this assignment: [shakespeare data](shakespeare-data.zip)
1. Read a file containing text fragments and transform it into a set of distinct words.
2. Compute the _Shakespeare score_ for the text.
3. Identify the optimal threshold:
   - Obtain a _test set_ by gathering collections of text fragments with known Shakespearean authorship status, forming a _test set_.
   - Collect author information for all text fragments to establish expected results.
   - Predict whether each text fragment was written by Shakespeare, using the specified threshold.
   - Validate predictions by assessing the correctness of authorship identification.
   - Iterate through these steps using various threshold values. Visualize the outcomes and determine the threshold that produces the most accurate classification.

### Step 1

- **Create** a file named `unique-word-classifier.py` and ensure it resides in the same directory as the `test-set` and `training-set` folders.
- Inside this file, **implement** the function `to_set(text)`. This function tokenizes the text and converts it into a set:
    - Split the text on spaces.
    - Remove leading and trailing punctuation from each word. Punctuation characters include: ' ' (space), ',', ';', '.', ':', "'", '"', '[', ']', '(', ')', '-', '_', '?', and '!'.
    - Convert all words to lowercase.
    - Exclude words that still contain non-alphabetic characters after cleanup.
    - Return a `set` containing only unique words.

Copy the following code into your Python file. It includes test cases for your function:

```python
def main():
    test1 = "Hello world! This is a test."
    print(to_set(test1))
    # Expected output (order might differ):
    # {'world', 'test', 'is', 'this', 'a', 'hello'}

    test2 = "Programming is FUN! Let's code."
    print(to_set(test2))
    # Expected output (order might differ):
    # {'programming', 'code', 'is', 'fun'}

    test3 = "-human: 'Hello, world!' -human: 'How's life?' -computer: 'I'm [computer: red] doing _well_ (I think); thank you.'"
    print(to_set(test3))
    # Expected output (order might differ):
    # {'red', 'computer', 'think', 'world', 'human', 'thank', 'you', 'life', 'doing', 'hello', 'well', 'i'}

    with open('test-set/shakespeare.0493.txt') as file:
        print(to_set(file.read()))
    # Expected output (order might differ):
    # {'his', 'fathers', 'nothing', 'traitor', 'daughters', 'begot', 'mercy', 'a', 'have', 'it', 'their',
    #  'on', 'such', 'flesh', 'to', 'should', 'that', 'lowness', 'is', 'judicious', 'punishment', 'unkind',
    #  'could', 'death', 'fashion', 'little', 'but', 'the', 'discarded', 'thus', 'pelican', 'this', 'lear',
    #  'those', 'nature'}

if __name__ == '__main__':
    main()
```

### Step 2

- **Implement** a function `load_shakespeare_words()` that reads the file `shakespeare-words.txt`, extracts words considered typical for Shakespearean writing, and returns them as a `set`.
- **Implement** a function `calculate_shakespeare_score(text, shakespeare_words)` that calculates the _Shakespeare score_ for a given text using the `to_set()` function you previously wrote.

Test your functions using the following code:

```python
test_text = """In the halcyon days of yore, we reveled amidst fields of golden darnel,
a beggarly intrusion among nature's grandeur. The mirthful melodies of the wassail danced upon the breeze,
 while beggarly worries scattered like autumn leaves"""

test_words = set(['wassail', 'halcyon', 'cannoneer', 'darnel', 'beggarly', 'dispraise'])
print('Score:', calculate_shakespeare_score(test_text, test_words))
# Expected output:
# Score: 0.13793103448275862

shakespeare_words = load_shakespeare_words()
print('Amount of Shakespeare words:', len(shakespeare_words))
# Expected output:
# Amount of Shakespeare words: 3040

with open('test-set/shakespeare.0493.txt') as file:
    print('Score of shakespeare.0493.txt', calculate_shakespeare_score(file.read(), shakespeare_words))
# Expected output:
# Score of shakespeare.0493.txt 0.02857142857142857
```

### Step 3

#### Retrieve Test Files

The following function retrieves the names of all `.txt` files in a specified folder:

```python
def get_text_file_names(path, extension='txt'):
    """This function searches for all file names in [path] that have [exetension]"""
    return list(Path(path).glob(f'*.{extension}'))
```

Ensure you import `Path` from `pathlib` at the top of your file:

```python
from pathlib import Path
```

You can test this function using the following code:

```python
test_files_dir = Path('test-set')
filenames = get_text_file_names(test_files_dir)
print('Number of .txt files:', len(filenames))
# Expected output:
# Number of .txt files: 485
```

#### Determine Expected Results

The following function uses filenames to determine the expected classification results:

```python
def is_written_by_shakespeare(text_file_names):
    """Checks for each file name in the list of text_file_names if it the corresponding
    file contains text that is written by Shakespeare. It is assumed that files are
    formatted as [writer].[fragment number].txt"""
    results = []
    for file_name in text_file_names:
        results.append(file_name.name.split('.')[0] == "shakespeare")
    return results
```

Test this function with:

```python
print(is_written_by_shakespeare(
    [Path('shakespeare.0100.txt'), Path('marlow.0100.txt'), Path('shakespeare.foo.bar')])
)
# Expected output:
# [True, False, True]

is_shakespeare = is_written_by_shakespeare(filenames)
print(is_shakespeare[225:235])
# Expected output:
# [False, False, False, False, False, False, True, True, True, True]
```

#### Predict

- **Implement** the function `calculate_scores(text_file_names, shakespeare_words)` to calculate _Shakespeare scores_ for each file in `text_file_names`. Utilize the `calculate_shakespeare_score()` function you previously wrote. The output should be a list containing scores for each file.

- **Implement** the function `predict_shakespeare(shakespeare_scores, threshold)` to determine whether each score, based on the `threshold`, indicates Shakespearean authorship. The output should be a list of Booleans.

Test your code with:

```python
scores = calculate_scores(filenames, shakespeare_words)
print(scores[225:235])
# Expected output:
# [0.0, 0.013245033112582781, 0.0, 0.013333333333333334, 0.010101010101010102,
# 0.012345679012345678, 0.06321839080459771, 0.06666666666666667, 0.03333333333333333,
# 0.09230769230769231]

threshold = 0.02
predicted_shakespeare = predict_shakespeare(scores, threshold)
print(predicted_shakespeare[225:235])
# Expected output:
# [False, False, False, False, False, False, True, True, True, True]
```

#### Evaluate

- **Implement** the function `calculate_accuracy(actual, predicted)` to compute the accuracy of predictions compared to actual values. Both input lists contain Boolean values, where `True` signifies predicted or actual Shakespearean authorship.

Test your code with:

```python
actual = [True, False, True, False]
predicted = [True, True, True, False]
accuracy = calculate_accuracy(actual, predicted)
print('Accuracy:', accuracy)
# Expected output: Accuracy: 0.75

accuracy = calculate_accuracy(is_shakespeare, predicted_shakespeare)
print('Accuracy:', accuracy)
# Expected output: Accuracy: 0.7835051546391752
```

#### Repeat

- **Extend** the `main()` function to iterate through multiple threshold values, running the prediction and evaluation steps. Experiment with values between 0 and 0.1 in 40 steps. Visualize the results. The visualization should resemble the figure below:

![embed](unique-word-classifier)
