# Building generative models

- Test the different code snippets by navigating here: [webppl.org](http://webppl.org)
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
var biasedFlipping = function(){
		flip(0.7)
}
var repeatedFlipping = repeat(1000,biasedFlipping)
viz(repeatedFlipping)
```

- Comprehension check: Why wouldn't `repeat(1000,flip(0.7))` work?

## Building simple models

- Here is a model where we flip 3 biased coins, sum up the results, and visualize them.
- Note that javascript turns `true` into `1`, and `false` into `0` for us, when we apply mathematical operations to logical variables.

```javascript
var flippingAway = function(){
	var a = flip(0.3)
	var b = flip(0.3)
	var c = flip(0.3)
	var result = a + b + c
	return result
}
viz(repeat(1000,flippingAway))
```

## Sample from probability distributions

- WebPPL has a number of probability distributions that we can simply sample from. Below are some examples, the full list can be found here: [http://docs.webppl.org/en/master/distributions.html](http://docs.webppl.org/en/master/distributions.html)

```javascript
var strengthGaussian = gaussian(50,10) //Gaussian strength
var strengthExponential = exponential(1) //Exponential strength

// we can easily combine distributions to make complex new ones
var whatIsThisEven = function(){
	Math.pow(Math.abs(gaussian(0,1)), uniform(0,1)*exponential(1))
}
whatIsThisEven()
```

## Memoization

- Sometimes we want to assume a probability distribution over a property or object, but we want to assure that we get the same answer whenever that property or object is queried. For example, we might not know the color of Tobi's eyes. We can model this knowledge/uncertainty as a random distribution over different eye colors. However, we want to ensure that whenever we query for Tobi's eye color within our model, we will return the same eye color.

```javascript
var eyeColor = function (person) {
	return uniformDraw(['blue', 'green', 'brown']);
};
[eyeColor('tobi'), eyeColor('tobi'), eyeColor('tobi')];
```

- To do this we use the built-in procedure `mem`. A memoized procedure will sample its random values the first time it is run, but will re-use those same values on subsequent runs. A memoized procedure will sample new values if it is passed new arguments. Try playing with the following eye-color example:

```javascript
var eyeColor = mem(function (person) {
	return uniformDraw(['blue', 'green', 'brown']);
});
[eyeColor('tobi'), eyeColor('kevin'), eyeColor('tomer')];
```

## Recursion

- counting down example




## Practice

- Create a function for returning the strength of a person. What distribution makes sense to you? Should you use memoization?

<!--
- SOLUTION:

A Gaussian seems like a reasonable distribution -- this assumes that there is some average strength in the population and deviations follow a bell curve.

You SHOULD use memoization here -- Tobi's strength might change if he starts / stops working out, but it won't change in the time between two function calls

 ```javascript
var strength = mem(function (person) {return gaussian(50, 10)});

print("Tobi's strength: " + strength('tobi'));
print("Tobi's strength: " + strength('tobi'));
print("Tomer's strength: " + strength('tomer'));
```-->

- Suppose someone in a tug of war sometimes decides to be lazy (one third of the time) and not pull that hard. Create a function for returning the laziness of a person. Should this be memoized?

<!--
- SOLUTION:

You should NOT use memoization here -- Tobi might work hard at one point but be lazy later. Memoization would force him to always be lazy or working hard

 ```javascript
var lazy = function(person) {return flip(1/3) };

print("Is Tobi lazy? " + strength('tobi'));
print("Is Tobi lazy again? " + strength('tobi'));
print("Is Tobi lazy still? " + strength('tobi'));
print("Is Tobi lazy now? " + strength('tobi'));
```-->



- Create a function tugWinner. The function takes in two people, and checks their strength and laziness (using the previous functions). If one is lazy and the other is not, the non-lazy one should be returned. If both or neither are lazy, return the one with the greater strength.

<!--
- SOLUTION:

 ```javascript
var tugWinner = function(person1, person2) {
	var str1 = strength(person1);
	var isLazy1 = lazy(person1);
	var str2 = strength(person2);
	var isLazy2 = lazy(person2);

	if (isLazy1 & !isLazy2) { // person1 is lazy, person2 is not
		return person2;
	} else if (!isLazy1 & isLazy2) { // person2 is lazy, person1 is not
		return person1;
	} else { // Both or neither are lazy
		return str1 > str2 ? person1 : person2
	}
};
print("The winner between Tobi and Tomer is: " + tugWinner('tobi', 'tomer'));
print("The winner between Tobi and Kevin is: " + tugWinner('tobi', 'kevin'));
print("The winner between Kevin and Tomer is: " + tugWinner('kevin', 'tomer'));

print ("Tobi's strength is: " + strength('tobi'));
print ("Tomer's strength is: " + strength('tomer'));
print ("Kevin's strength is: " + strength('kevin'));
```

Note that we don't have to worry about equal strengths here -- because these numbers are pulled from a continuous distribution, they will never be exactly equal
-->


- Recursion (BONUS). Try to build the following procedure: You take a coin, and flip it. If the coin returns true ("heads"), stop. If the coin returns false ("tails"), then flip the coin again. Keep going in this manner, counting up the total number of tails you see before you hit tails.

- Below is the start of some code to construct this procedure, fill in the rest:

```javascript
var countTails = function(){
	var flipAndAdd = function(tailsSoFar) {
		if (...){
			...
		} else{
			...
		}
	}
	return flipAndAdd(0);
};
```

<!--
- SOLUTION:

 ```javascript
 var countTails = function(){
 	var flipAndAdd = function(tailsSoFar) {
 		if (flip()){ // Got heads
 			return tailsSoFar;
 		} else{
 			return flipAndAdd(tailsSoFar + 1);
 		}
 	}
 	return flipAndAdd(0);
 };
```-->


- Try repeating `countTails` many times using `repeat` and visualize the result using `viz`. What does this distribution look like?

<!--
- SOLUTION:

 ```javascript
viz(repeat(1000, countTails));
```
... hey... that looks like an exponential distribution...

-->
