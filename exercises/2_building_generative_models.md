# Building generative models

- Test the different code snippets by navigating here: [http://webppl.org](webppl.org)

## Forward sampling and random primitives 

- `flip()` is a basic random primitive (akin to a coin-flip).
- Run `flip()` a few times. 
- Pro tip: An evaluation on the last line is displayed by default (so you don't really need `print()` to show it.)

```javascript
flip()
```

- When you get bored of running `flip()`, you can make use of the higher order function `repeat()`.
- `repeat()` takes a number and a function as inputs, and runs the function that many times. 
- Note that we need to put `flip()` inside a function. 

```javascript
var flipping = function(){
	flip()
}
repeat(1000,flipping)
```

- Webppl comes with some simple visualization tools that we fire up using `viz()`. 
- Let's visualize flipping a biased coin with `p(true) = 0.7` 1000 times. 

```javascript
var biased_flipping = function(){
	flip(0.7)
}
var repeated_flipping = repeat(1000,biased_flipping)
viz(repeated_flipping)
```

## Building simple models 

- Here is a model where we flip 3 biased coins, sum up the results, and visualize them. 
- Note that javascript turns `true` into `1`, and `false` into `0` for us, when we apply mathematical operations to logical variables. 

```javascript
var flipping_away = function(){
	var a = flip(0.3)
	var b = flip(0.3)
	var c = flip(0.3)
	return a + b + c
}
viz(repeat(1000,flipping_away))
```

## Sample from probability distributions 

- webppl has a number of probability distributions that we can simply sample from. 

```javascript
var strength = gaussian(50,10) //Gaussian strength
var strength = exponential(1,2) //Exponential strength
strength
```

## Recursion 

- Recursion is a very powerful technique in which a function calls itself. 

## Memoization 


## Practice 
