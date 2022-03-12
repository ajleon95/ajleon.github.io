---
tags: cs161p projects wordle-solver 
---

# Building a Wordle Solver, Part 3: Processing
## Processing inputs

Now that I have a [suite of inputs]({% post_url 2022-02-15-solving-wordle-inputs %}) that I can use in my program, I can move onto the processing step. 

### Figuring out where to go

Let's refer back to the pseudocode I wrote [in the planning phase]({% post_url 2022-02-14-solving-wordle-getting-started %}).

```pseudocode
for word in word list:
  if word contains any grey letters: 
    eliminate word
  if word doesn't contain any yellow letters:
    eliminate word
  if word doesn't have green letters in correct position:
    eliminate word
  add word to potential word list
print potential word list
```

I have the first line, `for word in word list`. This is already in my function `get_potential_words()`. But now I need a way to elimiate words that meet one of the three criteria: 

1. if word contains any grey letters
2. if word doesn't contain any yellow letters
3. if word doesn't have green letters in correct position

So I have a way to get the clues. I will also need a way to *test* potential words against the hints. That sonds like a job for 3 new functions. 

### Processing grey letters

Now, a potential solution can't contain any grey letters. So I need a new function to help me do this comparison. 

```python
def has_any_letter(word, letters):
	"""Returns True if word has any letter in letters"""
```

This should be pretty simple. All there is to do is iterate over each letter in `letters`, and see if it's in `word`. Once the function finds one matching letter, it can `return True`, which will end the loop. Otherwise, if the loop ends, it should `return False`. 

```python
def has_any_letter(word, letters):
	"""Returns True if word has any letter in letters"""
    for letter in letters:
        if letter in word:
            return True
    return False
```

Now I can write some code like this to test that a potential solution conforms to the grey letter clues.

```python
if has_any_letter(word, grey_letters) is False:
	#  Include word 
```

But let's get the other comparisons ready to go before moving onto that step. 

### Processing yellow letters

Unlike grey letters, a potential solution must have all yellow letters. So unlike with inputs, I can't just tweak the above function. I'll need a new approach. 

```python
def has_all_letters(word, letters):
	"""Returns True if word has all letters in letters"""
```

I decided on an accumulator that will count the number of letters in `word` that appear in `letters`. Then, it can test if `word` has all `letters` if the number of matching letters equals the length of `letters`. 

```python
def has_all_letters(word, letters):
    """Returns True if word has all letters in letters"""
    count = 0
    for letter in letters:
        if letter in word:
            count += 1
    return count == len(letters)
```

A little more complicated, but still quite straightforward. Let's deal with the last piece of the puzzle, which is the green letters. 

### Processing green letters

Green letters are the most complicated. Not only does the word need to have all green letters, it must have them in the correct position. 

In the last post, I showed how I structured the input for green letters. It's a list of letters, with known letters corresponding to an index in a 5-item list. For example

```python
green_letters = [None, "E", None, None, "D"]
```

means we know there is an E in the second place of the word, and a "D" in the fifth place.

So let's build a function that consumes a list like that, and compares it to a word. 

```python
def has_positioned_letters(word, letters):
	"""Returns true if word has letters in positions given by list letters"""
```

I start by iterating over each item in letters. Instead of iterating over the list itself, I'm going to iterate over numbers corresponding to an index.

```python
def has_positioned_letters(word, letters):
	"""Returns true if word has letters in positions given by list letters"""
	for index in range(len(letters)):

```

Next, I need to compare each letter in `letters` against the corresponding letter in `word`. 

Since I'm iterating using the index, I can use the index on `word` as well. This will help me check if the first letter in `letters` is also the first letter in `word`, and same for the second letter, and so on. 

I want to make sure all letters in `letters` correspond to the letters in `word`. So, I'll `return False` if any of the letters don't match, ending the loop. If the loop completes, that means all letters were a match, and I can `return True`.

```python
def has_positioned_letters(word, letters):
	"""Returns true if word has letters in positions given by list letters"""
	for index in range(len(letters)):
		if letters[index] != word[index]:
			return False
	return True
```

Finally, I need to make sure the function only compares known letters. If we don't know a letter in a position, we shouldn't compare the same letter in `word`. Let's add in a check to make sure the letter in `letters` is not `None`:

 ```python
def has_positioned_letters(word, letters):
	"""Returns true if word has letters in positions given by list letters"""
	for index in range(len(letters)):
		if letters[index] is not None and letters[index] != word[index]:
			return False
	return True
```

Now I should be ready to add this logic into our function to get potential words.


### Modifying `get_potential_words`

The `get_potential_words()` function will be a great place to do the comparisons I need across every word in the Wordle dictionary. Instead of printing every word, I want it to print only the words that conform to the clues the user has inputted. 

Let's revisit `get_potential_words()`. 

```python
def get_potential_words():
	"""Searches wordle word list and returns possible words that match the grey, yellow and green letters inputted"""
    wordle_dictionary = open("wordle.txt", "r")
    for word in wordle_dictionary:
        word = word.replace("\n", "")  # remove newline
        print(word)
```

Right now, it prints potential words. And it prints every potential word. 

The function needs to return only those words that meet these criteria: 

1. word contains no grey letters
2. word contains all yellow letters
3. word contains green letters in correct position 

Thankfully, I made functions corresponding to each of these! 

I first created a list I can use to add potential words. The function then returns this list. 

```python
def get_potential_words():
	"""Searches wordle word list and returns possible words that match the grey, yellow and green letters inputted"""
	potential_words = []
    wordle_dictionary = open("wordle.txt", "r")
    for word in wordle_dictionary:
        word = word.replace("\n", "")  # remove newline
        potential_words.append(word)
    return potential_words
``` 

It's time to add checks against `word` using the functions I created. A simple if statement can help us out. `get_potential_words()` will also need to accept the various clues we've gathered. 

```python
def get_potential_words(grey_letters, yellow_letters, green_letters):
	"""Searches wordle word list and returns possible words that match the grey, yellow and green letters inputted"""
	potential_words = []
    wordle_dictionary = open("wordle.txt", "r")
    for word in wordle_dictionary:
        word = word.replace("\n", "")  # remove newline
        if has_any_letter(word, grey_letters) is False and \
        		has_all_letters(word, yellow_letters) and \
                has_positioned_letters(word, green_letters):
        	potential_words.append(word)
    return potential_words
``` 

Now we're getting somewhere! Instead of printing all letters, now `get_potential_words()` will return a list of potential words that conform to the clues we've got from the game. 

The last step will be to tie it all together to give the user an experience that will allow them input their clues and view the list of potential words. That is, the output! 
