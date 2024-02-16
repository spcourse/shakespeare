# Unique words part 1: optimize the classifier

The aim is to program a classifier capable of deciding if text fragments are written by Shakespeare. This task involves a set of words characteristic of Shakespearean writing to categorize the fragments.

This classifier has a straightforward design, based on the frequency of word overlaps between the text fragments and the pre-generated Shakespearean word list.

For instance, let's consider the following sequence of words as representative of Shakespearean language:

_wassail, halcyon, cannoneer, darnel, beggarly, dispraise_

With this list, we can evaluate the following text and assign it a score based on the relative occurrence of Shakespearean words. Here is the example text:

"In the **halcyon** days of yore, we reveled amidst fields of golden **darnel**, a **beggarly** intrusion among nature's grandeur. The mirthful melodies of the **wassail** danced upon the breeze, while **beggarly** worries scattered like autumn leaves."

This text would yield a _Shakespeare score_ of approximately $$0.139$$: It contains 4 distinct words from the Shakespearean word list (note that *beggarly* appears twice, but multiple occurrences are not counted at this stage). The total count of unique words in the text is $$29$$, yielding a score of $$4/29 \approx 0.139$$. In general, the Shakespeare score is computed as follows:

$$
\textrm{Shakespeare score} = \frac{\textrm{unique Shakespeare words in text}}{\textrm{unique words in text}}
$$

Assuming that higher scores indicate a higher likelihood of Shakespearean authorship, while lower scores indicate the opposite, we can formulate a simple classification mechanism. This involves setting a threshold (e.g., $$0.02$$). For all texts with a higher score than this threshold, we predict them as having been written by Shakespeare. So the provided text would be positively classified (as Shakespearean) because it has score higher score than $$0.02$$ (i.e., $$0.139$$).

In this assignment you will be provided with a precomputed compilation of Shakespearean words and some code that can apply the algorithm as described above to a single document. However, the provided algorithm is quite slow. Your task for this assignment will be to optimize the algorithm.

## Provided example.

Download the data for this assignment: [shakespeare](shakespeare.zip). To kick you off, this `.zip` contains some example code. The file `unique-word-classifier.py` contains a program that will predict if a file contains text that was written by Shakespeare. Run the file:

    $ python unique-word-classifier.py

This gives:

    The text test-set\shakespeare.0350.txt is written by Shakespeare
    With a threshold of 0.0 we predict that Shakespeare is the author.
    With a threshold of 0.05 we predict that Shakespeare is the author.
    With a threshold of 0.1 we predict that Shakespeare is not the author.
    With a threshold of 0.15 we predict that Shakespeare is not the author.

The program takes an arbitrary file from the folder `test-set` and predicts whether it is written by Shakespeare. For all files in this folder, we actually know if it was written by Shakespeare, so we an actually see if our prediction is correct. This way we can use the test-set to check how well our algorithm works.

As you can tell from the output we test the algorithm on file `shakespeare.0350.txt`. This file contains a text fragment that was actually written by Shakespeare.

You see that the code tries out different threshold for the prediction. With thresholds of $$0.05$$ or lower for the _Shakespeare score_ we predict correctly that Shakespeare is the author of this text fragment. If we would take a higher threshold ($$0.1$$ or above) the algorithm would not give the correct prediction. So based on only this text fragment we could conclude that the threshold should be around $$0.05$$, but that is based on only one text fragment. For other fragments this score might differ. One of the challenges of the upcoming few assignments is to find the optimal threshold that works for all text fragments.

### Explanation of code.

The provided code contains the following functions:

1. **`main()`**:
    - The main entry point of the program. It:
        - loads a collection Shakespearean words from a provided file (`shakespeare-words.txt`)
        - selects a file containing a text fragment from the folder `test-set`
        - determines if the fragment was actually written by Shakespeare
        - calculates a score
        - predicts authorship based on varying thresholds

2. **`load_shakespeare_words(filename)`**:
    - Loads words typically used by Shakespeare from a file.

3. **`get_text_file_names(path, extension='txt')`**:
    - Finds all files within a specified directory having a particular extension (default is '.txt').

4. **`calculate_shakespeare_score(text, shakespeare_words)`**:
    - Computes a score representing the likeness of a given text to Shakespeare's style. It:
        - tokenizes the text into words
        - calculates the ratio of unique Shakespearean words to unique words in the text.

5. **`is_written_by_shakespeare(file_name)`**:
    - Determines if a file was authored by Shakespeare based on its name. It does this by checking if the filename starts with "shakespeare".

6. **`tokenize_text(text)`**:
    - Tokenizes a string. It:
        - Splits a string of text into a list of words.
        - Cleans each word by removing punctuation and non-alphabetic characters.

## Specification

In the end we would like to have an algorithm that can classify all text fragments from `test-set` and assess how well it performs for **all** fragments. One obstacle is that the code we have provided is hopelessly inefficient. Especially the function `calculate_shakespeare_score` is very inefficient. Currently it has a running time complexity of $$O(N^2)$$. This could be reduced to $$O(N)$$

Your task is to optimize this function by eliminating the `for`-loops and use `sets` instead.

Tips:

* At the moment `calculate_shakespeare_score` contains two `for`-loops. And both `for`-loops use `in` to search an element in a list. This makes that both loops have a complexity of $$O(N)^2$$.
* Think about what these two `for`-loops do and how this can be done by using `sets` instead.
* This assignment should require very little programming. It's really about understanding the problem and the working of `sets`.
* If done correctly, the modifications should make the code much shorter and clearer, not more complicated.
* You only need to modify `calculate_shakespeare_score`. Nothing else.
* To classify a text fragment, your code could run about a 100 times faster after optimizing it. You can test the running time of the function `calculate_shakespeare_score` using the `timeit` library. Add the following code at the end of your file:

        import timeit
        time = timeit.timeit("calculate_shakespeare_score(text, shakespeare_words)",
        setup =
        """from __main__ import load_shakespeare_words, calculate_shakespeare_score
        shakespeare_words = load_shakespeare_words("shakespeare-words.txt")
        with open('./test-set/shakespeare.0350.txt', 'r') as file:
            text = file.read()
        """, number=1000)
        print(time)

    This will run the function a 1000 times and print the total running time. The precise running time will depend on the machine you are running your code on. But on my machine, the running time before optimization is around 2 seconds and after optimization it is around 0.03 seconds. **So it runs two orders of magnitudes faster, just by using sets.**
