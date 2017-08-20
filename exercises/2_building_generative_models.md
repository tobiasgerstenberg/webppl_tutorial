# Building generative models

- Test the different code snippets by navigating here: [http://webppl.org](webppl.org)
- Check out this chapter of the probmods book for more information: [https://probmods.org/chapters/02-generative-models.html](02-generative-models)

## Forward sampling and random primitives

- `flip()` is a basic random primitive (akin to a coin-flip).
- Run `flip()` a few times.
- Pro tip: An evaluation on the last line is displayed by default (so you don't really need `print()` to show it.)

```javascript
flip()
```

- When you get bored of running `flip()`, you can make use of the higher order function `repeat()`.
- `repeat()` takes a number and a function as inputs, and runs the function that many times.

```javascript
repeat(1000,flip)
```

- WebPPL comes with some simple visualization tools that we fire up using `viz()`.
- Let's visualize flipping a biased coin with `p(true) = 0.7` 1000 times.

```javascript
var biased_flipping = function(){
	flip(0.7)
}
var repeated_flipping = repeat(1000,biased_flipping)
viz(repeated_flipping)
```

- Comprehension check: Why wouldn't `repeat(1000,flip(0.7))` work?

## Building simple models

- Here is a model where we flip 3 biased coins, sum up the results, and visualize them.
- Note that javascript turns `true` into `1`, and `false` into `0` for us, when we apply mathematical operations to logical variables.

```javascript
var flipping_away = function(){
	var a = flip(0.3)
	var b = flip(0.3)
	var c = flip(0.3)
	var result = a + b + c
	return result
}
viz(repeat(1000,flipping_away))
```
## Sample from probability distributions

- WebPPL has a number of probability distributions that we can simply sample from.

```javascript
var strength = gaussian(50,10) //Gaussian strength
var strength = exponential(1,2) //Exponential strength
```

## Memoization

- Sometimes we want to assume a probability distribution over a property or object, but we want to assure that we get the same answer whenever that property or object is queried. For example, we might not know the color of Tobi's eyes. We can model this knowledge/uncertainty as a random distribution over different eye colors. However, we want to ensure that whenever we query for Tobi's eye color within our model, we will return the same eye color.

- To do this we use the built-in procedure `mem`. A memoized procedure will sample its random values the first time it is run, but will re-use those same values on subsequent runs. A memoized procedure will sample new values if it is passed new arguments. Try playing with the following eye-color example:

```javascript
var eyeColor = mem(function (person) {
    return uniformDraw(['blue', 'green', 'brown']);
});
[eyeColor('tobi'), eyeColor('kevin'), eyeColor('tomer')];
```

## Practice

- Create a function for returning the strength of a person. What distribution makes sense to you? Should you use memoization?

- Suppose someone in a tug of war sometimes decides to be lazy and not pull that hard. Create a function for returning the laziness of a person. Should this be memoized?

- Create a function tugWinner. The function takes in two people, and checks their strength and laziness (using the previous functions). If one is lazy and the other is not, the non-lazy one should be returned. If both or neither are lazy, return the one with the greater strength.

- Recursion (BONUS). Try to build the following procedure: You take a coin, and flip it. If the coin returns true ("heads"), stop. If the coin returns false ("tails"), then flip the coin again. Keep going in this manner, counting up the total number of tails you see before you hit tails.

- Below is the start of some code to construct this procedure, fill in the rest:

```javascript
var countTails = function(){
  if ()
{}
  else
      {}
}
```

- Try repeating `countTails` many times using `repeat` and visualize the result using `viz`. What does this distribution look like?
