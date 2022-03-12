---
tags: cs161p projects wordle-solver 
---

# Building a Wordle Solver, Part 2: Inputs
## Getting started: Creating inputs

With the [overarching planning]({% post_url 2022-02-14-solving-wordle-getting-started %}) ready to go, I realized that a bulk of this solver will be dedicated to collecting inputs and finding ways to represent the hints you get from the game. 

We recently spent a week covering inputs in my Computer Science I class, so I felt prepared to tackle the challenge. 

### Collecting grey letters

First up, I decided that collecting the grey letters would be pretty easy. Since a grey letter doesn't appear anywhere in the final word, it should be straightforward to ask for a string of letters. 

```python
def get_grey_letters():
    grey_letters = input("What grey letters do you have? ").lower()
    return grey_letters
```

So we have our first function! 

### Collecting yellow letters

Next up, the yellow letters would also be quite simple. For v1 of this solver, I decided to not worry about where the yellow letters are in the puzzle. While that can be a valuable clue, it is also just as valuable to be able to discard words that don't have a yellow letter. 

So, it'll be up to the user to make sure that a potential solution on the list matches the yellow letter hints from the game. With that limitation in mind, we can use the same code from above and tweak it just a little bit.

```python
def get_yellow_letters():
    yellow_letters = input("What yellow letters do you have? ").lower()
    return yellow_letters
```

### Collecting green letters

Finally come the green letters. These are some of the best hints in the game as you can confirm you a letter and its position in the word. I decided that I wanted to take advantage of the green letter location in narrowing down potential words. 

So I had to think of a way to represent the letter and its position on the board (first, second, third, etc.). 

At first I thought about using a string. But that seemed a little too complicated. How would I handle having only the third letter, or the first and fourth letter, or some other combination? 

So then I thought an empty list would be a great way to represent the 5 letters. We can make an empty list like this:

```python
def get_green_letters():
    """Prompts user for green letters and returns a list of green letters and their positions in the word"""
    green_letter_positions = ["", "", "", "", ""]
```

where each empty string is one of five letters. Then, we prompt the user for their green letters. 

```python
def get_green_letters():
    """Prompts user for green letters and returns a list of green letters and their positions in the word"""
    green_letter_positions = ["", "", "", "", ""]
	green_letters = input("What green letters do you have? ").lower()
```

Next, we need to find out where those letters are on the board. We can use a for loop to iterate over each letter from the above input, and ask each user for that letters's position in the word with a number.

```python
def get_green_letters():
    """Prompts user for green letters and returns a list of green letters and their positions in the word"""
    green_letter_positions = ["", "", "", "", ""]
	green_letters = input("What green letters do you have? ").lower()
    for letter in green_letters:
        position = int(input(f"What position is {letter.upper()} in? "))
        green_letter_positions[position-1] = letter
    return green_letter_positions
```

Now we have a complete suite of functions to gather most of the available hints from the game via the user! 

### Getting all potential words

The last item on the IPO model is "a list of possible solutions". That is, the list that inspired this solver in the first place! With the magic of the Internet I downloaded a list of [all possible Wordle solutions](https://gist.github.com/cfreshman/a03ef2cba789d8cf00c08f767e0fad7b) for my program. 

With my previous programming experience, I knew it would be possible to use this file in my program. But how? We haven't covered how to read files yet in my class. So I decided to take it back to the internet to see what I could find. 

I found a link to the online text we use in class, *How to Think Like a Computer Scientist*, which has a page about exactly what I need to do: [iterating over lines in a file](https://runestone.academy/ns/books/published/thinkcspy/Files/Iteratingoverlinesinafile.html). Maybe I should have just looked in my textbook to begin with! 

With the lesson from the text, I was able to create a function to get potential words:

```python
def get_potential_words():
    wordle_dictionary = open("wordle.txt", "r")
    for word in wordle_dictionary:
        word = word.replace("\n", "")  # remove newline
        print(word)
```

Well, right now, it *prints* potential words. But, I was successful in processing all the words into my program. 

### Recap

Now, I have all four of my inputs that I identified in my IPO model. Those are: 

#### Inputs:
1. Get all the green letters so far
2. Get all the yellow letters so far
3. Get all the grey letters so far
4. Get a list of possible solutions 

Now I'm ready for the next step: implementing the necessary processing for my solver! 