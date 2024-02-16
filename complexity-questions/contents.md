# Complexity questions

Create a file called `questions.txt` and write down the answers to the questions below.

All the code fragments below have a variable `n`. Determine the big O complexity in terms of `n` for these fragments. If for example the big O complexity of an algorithm is quadratic, your answer should be "O(n^2)".

Hint: If you are not sure, you can run the code yourself and see what happens if you change the variable `n`.

### Question 1

    n = 6

    for i in range(n):
        print(i)

### Question 2

    n = 2

    for i in range(10):
        print(n + i)

### Question 3

    n = 5

    for i in range(n):
        for j in range(n):
            print(str(i*j) + "\t", end="")
        print()

### Question 4

    n = 4

    for i in range(n*3):
        print(i)
