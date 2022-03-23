---
tags: cs161p projects wordle-solver
---

# Building a Wordle Solver, Part 4: Output and Enhancements
## Putting it all together

With my inputs and processing out of the way, now I need to build a way to gather the inputs from the user, and then use that input to display solutions. 

The main control flow of the program will go in a `main` function. I'm going to display a brief intro message, then get right to collecting the various letters. 

```python
def main():
    """Implements a wordle solver"""
    print("This program will help you solve Wordle games. You need some clues to get started.")
    grey_letters = get_grey_letters()
    yellow_letters = get_yellow_letters()
    green_letters = get_green_letters()
```

This is a great place to get started. Now, the program will prompt the user for all clues on the board. 

But what next? It's easy. With my variables `grey_letters`, `yellow_letters` and `green_letters`, I can use the `get_potential_words` function I built in my last post. The function will return a list of potential words that conform to the grey, yellow and green letter clues. 

So we just need to call `get_potential_words` with the relevant parameters and assign the returned list to a variable. Then, we can iterate over that list to display all potential words to the user.

```python
def main():
    """Implements a wordle solver"""
    print("This program will help you solve Wordle games. You need some clues to get started.")
    grey_letters = get_grey_letters()
    yellow_letters = get_yellow_letters()
    green_letters = get_green_letters()
    potential_words = get_potential_words(grey_letters, yellow_letters, green_letters)

    print("Your potential words are:")
    for word in potential_words:
        print(f"* {word}")
```

Wow, that was easy (after spending time building all the functions, of course...). Now I just need to add a statement that will call the main() function. 

```python
if __name__ == "__main__":
	main()
```

This little snippet will call `main()` when the interpreter is running this file. This `if` statement is handy when programs are larger and involve multiple files. You can include things like test cases that run only when a particular file is being run directly by the interpreter versus being imported by another Python script. 

With this, my program is complete. When the user runs the script, they can input their clues and see potential solutions that match those clues. It will help anyone improve their Wordle score. But there are a few quick things I'd like to do to make this program easier to understand and use. 

## Enhancements

### Splitting it up

The sovler has 7 functions, *not* including the `main` function! This can be a little hard to read and follow. 

To help with this, I'm going to split my functions into three separate files: one called `inputs.py` for input-related functions, another called `solver.py` for solving-related functions, and finally, my `main` function in `main.py`.

Thankfully, Python makes this easy. With my code re-arranged, I just need to add two lines to my `main.py`, above the `main` function definition. 

```python
import inputs
import solver
```

Then, I need to rename the functions so Python knows where they are. I do this by adding the name of the file, followed by a period, to the function name. My new `main.py` looks like this:

```python
import inputs
import solver


def main():
    """Implements a wordle solver"""
    print("This program will help you solve Wordle games. You need some clues to get started.")
    grey_letters = inputs.get_grey_letters()
    yellow_letters = inputs.get_yellow_letters()
    green_letters = inputs.get_green_letters()

    potential_words = solver.get_potential_words(grey_letters, yellow_letters, green_letters)

    print("Your potential words are:")
    for word in potential_words:
        print(f"* {word}")
```

Now, it's not so daunting to read through the Wordle solver code!

### Run again?

Another enhancement I would like is the option to run the solver again without having to re-launch the program. 

Right now, the program terminates after printing out a list of potential words. I would like it to ask the user if they want to run again, and only terminate when the user inputs no. 

To do this, I create a new function in my `inputs.py` file.

```python
def get_yes_no():
    """Gets input. Returns true on yes, false on no. Validates input."""
    valid_input = False
    while not valid_input:
        answer = input("Please input yes/no: ")
        if answer == "yes":
            valid_input = True
            choice = True
        elif answer == "no":
            valid_input = True
            choice = False
        else:
            print("Invalid input.")
    return choice
```

This function uses a while loop to make sure the user's input is valid. Once validated by the if statement, it then ends the loop and returns True or False depending on what the user chooses. 

We can use a similar while loop in the `main` function to allow the user to repeat the program indefinitely. 

```python
def main():
    """Implements a wordle solver"""
    print("This program will help you solve Wordle games. You need some clues to get started.")
    repeat = True
    while repeat:
	    grey_letters = inputs.get_grey_letters()
	    yellow_letters = inputs.get_yellow_letters()
	    green_letters = inputs.get_green_letters()

	    potential_words = solver.get_potential_words(grey_letters, yellow_letters, green_letters)

	    print("Your potential words are:")
	    for word in potential_words:
	        print(f"* {word}")

	    print("")
        print("Run again?")
        repeat = inputs.get_yes_no()
```

Thus, the program will repeat until the user inputs "no" when prompted "Run again?". 

### Input validation for green letter positions

One final and easy validation I want to add is validating positions for green letters. Here's the `get_green_letters` function:

```python
def get_green_letters():
    """Prompts user for green letters and returns a list of green letters and their positions in the word"""
    green_letter_positions = [None, None, None, None, None]
    green_letters = input("What green letters do you have? ").lower()
    for letter in green_letters:
        position = int(input(f"What position is {letter.upper()} in? "))
        green_letter_positions[position-1] = letter
    return green_letter_positions
```

The user needs to input a value between 1 and 5. But what happens if they input a value like -1, or 7? Well, since it's a number value, the Python interpreter will still accept it and use it to assign the letter somewhere on the list. Even worse, if the user inputs anything that's not an integer, the program will throw an error and terminate. 

I want the program to make sure that the user is inputting a valid number between 1 and 5, inclusive, and make sure the program doesn't crash if there's a mistake or misunderstanding. Thankfully, this is easy to add. 

We can use a while loop similar to in `get_yes_no` to repeat a block of code until the user enters a valid number. 

```python
def get_green_letters():
    """Prompts user for green letters and returns a list of green letters and their positions in the word"""
    green_letter_positions = [None, None, None, None, None]
    green_letters = input("What green letters do you have? ").lower()
    for letter in green_letters:
    	valid_input = False
    	while not valid_input:
        	position = int(input(f"What position is {letter.upper()} in? "))
        	green_letter_positions[position-1] = letter
        	valid_input = True
    return green_letter_positions
```

Then, we can add a try-except block along with a simple if statement to validate the input and catch any invalid inputs. 

```python
def get_green_letters():
    """Prompts user for green letters and returns a list of green letters and their positions in the word"""
    green_letter_positions = [None, None, None, None, None]
    green_letters = input("What green letters do you have? ").lower()
    for letter in green_letters:
        valid_input = False
        while not valid_input:
            try:
                position = int(input(f"What position is {letter.upper()} in? "))
                if position < 1 or position > 5:
                    raise ValueError
                green_letter_positions[position-1] = letter
                valid_input = True
            except ValueError:
                print("Please input a number between 1 and 5 (inclusive).")
    return green_letter_positions
```

That was simple! Now, if a user enters a value other than a number, or a number less than 1 or greater than 5, they will recieve a message telling them what to do to fix it, instead of the program crashing or some other unexpected behavior. 

## Recap

Wow, that was quite a final step! I put the program together to give the user a cohesive experience from start to finish. Then, I added some enhancements to help make it friendlier to both users and other programmers reading through the code. With this completed, I think I will call this program the MVWS: the Minimum Viable Wordle Solver!

## What next?

The MVWS will certainly help you improve your Wordle score and maybe even beat your friends. But, it still requires a lot of manual work and some common-sense to use it together with Wordle. 

With that in mind, there are a lot of enhancements I would love to add to this program in the future: 
* **Yellow letter positions.** Wordle will color a letter yellow when it is in the word, but in the wrong place. It would be very helpful to eliminate possible words based on having a yellow letter in a certain position. 
* **Double letters.** It's possible to have any given letter be a yellow or green letter AND a grey letter be the same time. For example, guessing DRESS against STAND would have one yellow S and one grey S. How can we use this information to narrow down the word list even further? 
* **Best next guess.** Using the list of possible words, what should the user guess next to eliminate as many possible words? 
* **Best first guess.** Once we have a way to determine the best next guess, I can work backwards to suggest the best first guess. 

But for now, this program will be an invaluable tool as I play Wordle. Thank you for following along as I put it together! 