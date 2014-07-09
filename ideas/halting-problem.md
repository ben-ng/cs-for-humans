# The Halting Problem

Ever wonder why when a script on a web page doesn't do anything for more than 20 or so seconds, your browser asks *you* if you want to kill the script?

It's because it's *impossible* for the browser to know the difference between a slow running script, and one that is endlessly looping.

The task of writing a program that can determine if another program eventually finishes running is called the *Halting Problem*.

## Proving The Halting Problem Is Undecidable

There are many ways to prove this, but here's a simple one:

Suppose for the sake of contradiction that we *could* solve the halting problem, and it was given to us as the function `halts()`.

Now create this cute little program:

```javascript

function opposite(x) {
  if(halts(x)) {
    // go into an infinite loop!
    while(true) {}
  }
  else {
    return
  }
}

```

Now consider the amusing consequences of running `opposite` on `opposite`'s code:

```javascript
opposite(opposite)
```

Two possibilities result:

 * If the `opposite` exits, that means that `opposite` halts. But wait, that would have resulted in the infinite loop, which clearly doesn't halt. Contradiction!
 * If the `opposite` loops forever, that means that `halts` declared that `opposite` halts. Another contradiction!

Since the `opposite` program was constructed with a simple if/else statement, which we know always halts, only one possibility remains: the `halts` function can not possibly exist.

## Why Do We Care?

It turns out that many unsolved problems *reduce* to the halting problem. This means that if you were able to solve the halting problem, you would be able to use that solution to solve *all* these other problems.

I'll give you two practical problems, and one mathematical problem.

### Virus Scanning

Why do virus scanners keep having to update their databases?

Viruses *replicate*. They're unique in that they can write their own code, which enables them to spread to other systems without your intervention.

You would think that this would be an easy way to nip malware in the bud. A virus scanner could read an executable to see if it can write out its own code, and delete it without ever running the code.

Let's define our problem:

**Given the code for a program, does it ever copy itself?**

To solve this problem, observe that it is trivial to figure out all the places where the code could *possibly* copy itself, since there are a limited number of keywords in a language that let you do that:

```
function find_writes (code) {
  return code.match(/(writeFile|readFile|fs|copy)/)
}
```

With that in mind, we can *reduce* the virus scanning problem to the following:

**Given the code for a program, does it ever execute these specific lines of code?**

Turns out, this is just the halting problem in disguise! If we could tell if a program executed any line of code, we could certainly tell if it executed its *last* line of code.

Since we know that the halting problem is undecidable, this means that the problem of detecting a new virus is also undecidable.

### Search Engines

Put yourself in Google's shoes for a moment. There is a tremendous amount of information on the web, and your job is to separate the bad sites from the good sites.

To do this, you've spent years building an index of the entire Internet. Unfortunately, web pages are dynamic; it's trivial to change the content on a page after it has loaded.

How do you know when a page is done loading, and it is safe to start indexing? How do you know that a website carrying scholarly articles isn't going to replace its content with ads for special pills in 30 seconds?

*Can you ever know for sure?*

You guessed it: the answer is no. While you might be able to tell when a script has stopped running (in the case of JavaScript, when there are no more callbacks/events bound), you can't tell if a script *will* stop running. Since modern websites have complex scripts that are alive all the time, this problem is essentially undecidable.

### The Goldbach Conjecture

From the world of mathematics, we have the still unproven **Goldbach conjecture**: "Every even integer greater than 2 can be expressed as the sum of two primes".

This is a pretty bold statement to make about a series of infinite numbers! While we have ways of proving claims about infinite series of things, they have so far failed to prove this conjecture.

Observe that it's easy to write a program to check, number by number, if this conjecture holds. I've sketched out a test program below:

```javascript
// A simple testing script

function conjecture_holds (x) {
  // 1. find all prime numbers less than x
  // 2. find a pair of primes that add up to x
  // 3. return false if no pair was found, true otherwise
}

var i = 0

for (;;i++) {
  if(!conjecture_holds(i))
    throw new Error('The conjecture is disproven!')
}
```

Over the years, computer programs have proven that the conjecture is true for numbers as large as 4 * 10^18 (*That's 4 billion billion*!). It's pretty frustrating that even with so much evidence that the claim is true, we can't seem to put together a formal proof for it.

As it turns out, proving Goldbach's conjecture *reduces* to the halting problem. If we were able to tell if our testing program eventually exits, we would have a yes/no answer to whether the conjecture were true or false.
