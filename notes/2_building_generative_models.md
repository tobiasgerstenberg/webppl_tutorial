# Building generative models

- Test the different code snippets by navigating here: [webppl.org](http://webppl.org)
- Check out this chapter of the probmods book for more information: [02-generative-models](http://probmods.org/chapters/02-generative-models.html)

## Forward sampling and random primitives

- `flip()` is a basic random primitive (akin to a coin-flip).
- Run `flip()` a few times.
- Tip: An evaluation on the last line is displayed by default (so you don't really need `display()` to show it.)

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
	return flip(0.7)
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
	var d = a + b + c
	return d
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
viz(repeat(1000,whatIsThisEven))
```

## Memoization

- Sometimes we want to assume a probability distribution over a property or object, but we want to assure that we get the same answer whenever that property or object is queried.
- For example, we might not know the color of Kevin's eyes. We can model this knowledge/uncertainty as a random distribution over different eye colors.
- Run the following code and notice what's odd:

```javascript
var eyeColor = function (person) {
	return uniformDraw(['blue', 'green', 'brown'])
}
display([eyeColor('Kevin'), eyeColor('Kevin'), eyeColor('Kevin')])
```

- Each time we call the `eyeColor()` function on `'Kevin'` it draws again from a uniform distribution.
- However, we want to ensure that whenever we ask for Kevin's eye color within our model, it will return the same eye color.
- To do this we use the built-in procedure `mem`. A memoized procedure will sample its random values the first time it is run, but will re-use those same values on subsequent runs.
- A memoized procedure will sample new values if it is passed new arguments.
- Try playing with the following eye-color example:

```javascript
var eyeColor = mem(function (person) {
	return uniformDraw(['blue', 'green', 'brown'])
})
display([eyeColor('Kevin'), eyeColor('Kevin'),
	eyeColor('Josh'), eyeColor('Josh')])
```

- Note that memoization requires **all** function arguments to be the same to recall its previous draw.
- For instance, the eyeColor function below allows for different "Kevins" to have different eye colors:

```javascript
var eyeColor = mem(function (firstName, lastName) {
	return uniformDraw(['blue', 'green', 'brown'])
})
display([eyeColor('Kevin', 'Smith'), eyeColor('Kevin', 'Smith'),
 	eyeColor('Kevin', 'McCallister'), eyeColor('Kevin', 'McCallister')])
```

## Recursion

- Recursion is a very powerful technique.
- For example, recursive functions are important for capturing theory-of-mind inferences such as: "Lisa thinks that Tom thinks that she thinks that he is interested in her."
- A recursive function calls itself within the function call. It needs a stopping condition (which can be probabilistic).
- Here is an example for how to implement a `while` loop using a recursive function. This function implements a "counting down to zero" procedure.

```javascript
var countingDown = function(number){
	display(number)
	return (number == 0) ? display('done with counting') : countingDown(number-1)
}
countingDown(4)
```

## Practice

1. Create a function for returning the strength of a person. What distribution makes sense to you? Should you use memoization?

<!--
- SOLUTION:

A Gaussian seems like a reasonable distribution -- this assumes that there is some average strength in the population and deviations follow a bell curve.

You SHOULD use memoization here -- A person's strength might change if she starts / stops working out, but it won't change in the time between two function calls

 ```javascript
var strength = mem(function (person) {return gaussian(50, 10)})

display("Josh's strength: " + strength('josh'))
display("Josh's strength: " + strength('josh'))
display("Kevin's strength: " + strength('kevin'))
```-->

2. Suppose someone in a tug of war sometimes decides to be lazy (one third of the time) and not pull that hard. Create a function for returning the laziness of a person. Should this be memoized?

<!--
- SOLUTION:

You should NOT use memoization here -- A person might work hard at one point but be lazy later. Memoization would force him to always be lazy or working hard

 ```javascript
var lazy = function(person) {return flip(1/3) }

display("Is Kevin lazy? " + lazy('kevin'))
display("Is Kevin lazy again? " + lazy('kevin'))
display("Is Kevin lazy still? " + lazy('kevin'))
display("Is Kevin lazy now? " + lazy('kevin'))
```-->

3. Create a function tugWinner. The function takes in two people, and checks their strength and laziness (using the previous functions). If one is lazy and the other is not, the non-lazy one should be returned. If both or neither are lazy, return the one with the greater strength. Note: The symbol for a logical AND is `&`, and the symbol for a logical NOT is `!`.

<!--
- SOLUTION:

 ```javascript
var strength = mem(function (person) {return gaussian(50, 10)})
var lazy = function(person) {return flip(1/3) }

var tugWinner = function(person1, person2) {
	var str1 = strength(person1)
	var isLazy1 = lazy(person1)
	var str2 = strength(person2)
	var isLazy2 = lazy(person2)

	if (isLazy1 & !isLazy2) { // person1 is lazy, person2 is not
		return person2
	} else if (!isLazy1 & isLazy2) { // person2 is lazy, person1 is not
		return person1
	} else { // Both or neither are lazy
		return str1 > str2 ? person1 : person2
	}
}
display("The winner between Kevin and Andrei is: " + tugWinner('kevin', 'andrei'))
display("The winner between Kevin and Josh is: " + tugWinner('kevin', 'josh'))
display("The winner between Andrei and Josh is: " + tugWinner('andrei', 'josh'))

display ("Kevin's strength is: " + strength('kevin'))
display ("Andrei's strength is: " + strength('andrei'))
display ("Josh's strength is: " + strength('josh'))
```

Note that we don't have to worry about equal strengths here -- because these numbers are pulled from a continuous distribution, they will never be exactly equal
-->

4. Recursion (BONUS). Try to build the following procedure: You take a coin, and flip it. If the coin returns `true` ("heads"), stop. If the coin returns `false` ("tails"), then flip the coin again. Keep going in this manner, counting up the total number of tails you see before you hit tails.

- Below is the start of some code to construct this procedure, fill in the rest:

```javascript
var flipTails = function(){
	if (...){
		return ...
	} else {
		return ...
	}
}
//viz(repeat(1000,flipTails)) //uncomment this bit to visualize your results once you've updated the function
```

<!--
- SOLUTION:

 ```javascript
var flipTails = function(){
	if (flip()){
		return 0
	}
	else
	{
		return 1+flipTails()
	}
}
viz(repeat(1000,flipTails))
```-->
