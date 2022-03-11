---
tags: cs161p projects wordle-solver 
---

# Building a Wordle Solver, Part 1
## Introduction
Like everyone else in 2022, I am obsessed with playing [Wordle](http://powerlanguage.co.uk/wordle). However, my aptitude for guessing 5 letter words is... well, it's not great. "HELLO"... then... uhh... Okay, no "E"... How about "HOTEL"? Does "SOCK" have 5 letters? 

After a while, I got smart about it. After a few clues, I would build out small spreadsheets with every possible letter in every possible position, and permute through them to see which combinations formed existing words. I also used apps like WolframAlpha to fill in the gaps and search the dictionary with wildcard letters. 

That became tedious. Then I tried using a Wordle solver, and there are many online that are easy to use. But it kind of took the fun out of it to have a computer guide me to the solution. 

Given the simplicty of the game, I thought, "What if I build a program myself to go through the entire dictionary with possible solutions?" At the very least, it could do the difficult permutations for me. And because I built it myself, using a solver wouldn't seem like cheating, at least to me. 

So that's how I set out to build my own Wordle Solver.

## Preliminary Research & Approach

To get started, I did some research on how the app works. Thankfully, the Wordle app is incredible simple. It's a JavaScript webbrowser game, and all of the words are pre-programmed for at least 2 years worth of gameplay. Thankfully, the *New York Times* has made minimal changes to the application since they bought it. 

My first step was to think about how I wanted to approach the problem. Yes, the game had solutions programmed out for years in advance, but simply getting the date and printing the corresponding solution is **too** trivial and not fun to build. 

I then thought to just use every word in the English language combined with whatever hints I've gotten from playing the Wordle game. This is what I had been doing manually with spreadsheets and permutations. 

I decided my app wouldn't concern itself with the "best" starting word, or any particular starting word, while playing. I would just play Wordle like normal, and then this Wordle Solver would help me when I get stuck by doing the tedious permutations and dictionary cross-refrencing I was already doing to play the game. Yes, that seems like the right approach. 

But what list of words should I use? I first thought to create a list of 5 letter words using a normal dictionary file. But with a little searching on Google, I found a list of [all possible Wordle solutions](https://gist.github.com/cfreshman/a03ef2cba789d8cf00c08f767e0fad7b) (which has since been [updated for the *New York Times* edition](https://gist.github.com/cfreshman/a7b776506c73284511034e63af1017ee)). I wouldn't even have to go through the trouble of making my own list. Awesome. 

### IPO model

I then moved onto building an IPO model to understand what inputs I need, what processing needed to happen, and what I was going to output. Outputs was easy. This program is a Wordle Solver, so the output must be potential solutions. 

#### Outputs:
1. Potential solutions

Next, I needed to think about the information needed to get to the solutions. Thankfully, I was already very familiar with the approach I used manually. For my approach, I used all the green, yellow and grey letters I've gotten from the app. And since this was going to be a step up from what I was doing manually, I also included the list of possible solutions (linked above) to input into my program. 

#### Inputs:
1. Get all the green letters so far
2. Get all the yellow letters so far
3. Get all the grey letters so far
4. Get a list of possible solutions 

Awesome. Now for the meat of the program, the processing. I just need a rough sketch to get started. 

#### Processing:
1. Get green, yellow and grey letters 
2. Load list of possible solutions
3. Iterate over possible solutions
4. Output possible solution if it matches the hints (green, yellow and grey letters) 

### Pseudocode

Okay, this all sounds great so far. But the processing step of iterating over possible solutions seemed daunting. To break it down further, I wrote some pseudocode to help me organize my thoughts. 

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

Very nice. I felt like I had a good foundation for starting. With the scope of my project defined and the pre-coding word ready to go, I felt ready to start working on my Wordle solver. 
