# Doing inference

- Test the different code snippets by navigating here: [webppl.org](http://webppl.org)
- You can find a complete function reference here: [http://webppl.readthedocs.io/en/master/](http://webppl.readthedocs.io/en/master/)
- Check out this chapter of the probmods book for more information: [03-conditioning](http://probmods.org/chapters/03-conditioning.html)

## Conditioning on variables

### Rejection sampling

- We can use recursion to implement a rejection query.
- Here we are interested in the value of `a`, conditioning on the fact that the sum of `a`, `b`, and `c` is `>= 2`.

```javascript
var flippingAway = function () {
	var a = flip(0.3)
	var b = flip(0.3)
	var c = flip(0.3)
	var d = a + b + c
	return d >= 2 ? a : flippingAway()
}
viz(repeat(100, flippingAway))
```

- Note how the `flippingAway()` function is called inside the `flippingAway()` function. The return statement is an if-else statement. If the condition is met `d >= 2`, the value of `a` is returned, otherwise the `flippingAway()` function is called again.
- Comprehension check: Why would you not want to do this if you were flipping 20 coins and conditioning on at least 19 heads?

### Using WebPPL's inference procedures

- To do inference, we simply add a `condition` statement to our model.
- The general template is:

```javascript
var model = function(){
	var ... //our model description
	condition (...) // what we condition on
	return ... // what we are interested in
}
```

- To run inference, we use the webppl procedure `Infer`.
- `Infer` takes **options** (that define what kind of inference procedure to run), and a **function** as input.

```javascript
var flippingAway = function(){
	var a = flip(0.3)
	var b = flip(0.3)
	var c = flip(0.3)
	var d = a + b + c
	condition(d >= 2) //condition
	return a
}
var options = {method: 'rejection', samples: 1000}
var dist = Infer(options, flippingAway)
viz(dist)
```

- You can compare this posterior distribution to the prior distribution by simply changing the `condition(d >= 2)` to `condition(true)` (or by commenting out the `condition(...)` statement).

## Conditioning on arbitrary expressions

- One of the powerful features of the webppl programming language is that it allows conditioning on arbitrary expressions composed of the variables in the model.
- You can also return arbitrary expressions.
- Here is an example:

```javascript
var flippingAway = function(){
	var a = flip(0.3)
	var b = flip(0.3)
	var c = flip(0.3)
	condition(a + b + c >= 2) //arbitrary expression
	return a + b //arbitrary expression
}
var options = {method: 'rejection', samples: 1000}
var dist = Infer(options, flippingAway)
viz(dist)
```

- This is one key strength since it allows us to cleanly separate out the description of the generative model, and the inference procedure.

## Other inference procedures

- In the previous examples, we used rejection sampling (`method: 'rejection'`) to do inference.
- WebPPL implements a number of inference procedures. You can find out more about these here: [http://webppl.readthedocs.io/en/master/inference/methods.html](http://webppl.readthedocs.io/en/master/inference/methods.html)
- In general, you will **not** want to use rejection sampling. For simple models, the `MCMC` method is usually good enough to get by without the drawbacks of rejection sampling.
- Here is how you can use MCMC instead of rejection sampling (note you also need the `kernel` argument for this method):

```javascript
var flippingAway = function(){
	var a = flip(0.3)
	var b = flip(0.3)
	var c = flip(0.3)
	condition(a + b + c >= 2) //arbitrary expression
	return a + b //arbitrary expression
}
var options = {method: 'MCMC', kernel: 'MH', samples: 1000}
var dist = Infer(options, flippingAway)
viz(dist)
```

## Practice

### Inference in the tug of war model

- You now have learned about all the bits and pieces that make up the tug of war model.
- Here is the model:

```javascript
var model = function(){
	var strength = mem(function (person) {return gaussian(50, 10)})
	var lazy = function(person) {return flip(1/3) }
	var pulling = function(person) {
			return lazy(person) ? strength(person) / 2 : strength(person)
			}
	var totalPulling = function (team) {return sum(map(pulling, team))}
	var winner = function (team1, team2) {
			totalPulling(team1) > totalPulling(team2) ? team1 : team2
			}
	var beat = function(team1,team2){winner(team1,team2) == team1}
	// Condition and Return statements go here ...
}
// Infer and visualize here ...
```

- First, make sure that you understand all the bits and pieces.
- Condition on the fact that `"Tom"` beat both `"Steve"` and `"Bill"` at once, and return the strength of `"Tom"`. (Note: The `beat` function takes teams as input (i.e. two arrays of players). So even if the team only has one player, you still need to put that player into an array.)
- For the inference options, please use the following: `var options = {method: 'MCMC', kernel: 'MH', samples: 10000}`. This implements a Markov Chain Monte Carlo inference using the Metropolis-Hastings algorithm.
- If all goes well, the `viz` function will output a density function. You can print out the mean of the distribution by using the `expectation()` function: `display('Expected strength: ' + expectation(dist))`

<!--
- SOLUTION:

 ```javascript
var model = function() {
	//MODEL
	var strength = mem(function (person) {return gaussian(50, 10)})
	var lazy = function(person) {return flip(1/3) }
	var pulling = function(person) {
		return lazy(person) ? strength(person) / 2 : strength(person) }
	var totalPulling = function (team) {return sum(map(pulling, team))}
	var winner = function (team1, team2) {
		totalPulling(team1) > totalPulling(team2) ? team1 : team2 }
	var beat = function(team1,team2){winner(team1,team2) == team1}

	//CONDITION
	condition(beat(['Tom'], ['Steve','Bill']))

	//QUERY
	return strength('Tom')
}
var options = {method: 'MCMC', kernel: 'MH', samples: 10000}
var dist = Infer(options, model)
viz(dist)
display('Expected strength: ' + expectation(dist))
``` -->

### Extending the tug of war model

- Extend the tug of war model so that you can ask whether a player was lazy in a particular match.
- To do so, you need to amend the functions to not only feature persons (or teams) but also matches.
- You need to make laziness a persistent property (using `mem`) that applies to a person in a match (but not always to the same person).
- Once you've rewritten the code then try the following:
	+ Condition on the fact that Tom beat Tim in match 1 (hint: `condition(beat(['Tom'],['Tim'],1))`), and ask for whether Tom was lazy in match 1 (and whether Tim was lazy in match 1).
	+ How does the inference whether Tim was lazy in match1 change for the following series of matches?:
		* Match 1: Tim loses against Tom
		* Match 2: Tim wins against Steve
		* Match 3: Tim wins against Bill
		* Match 4: Tim wins against Mark
		* Match 5: Tim wins against Kevin
		* Match 6: Tim wins against Tobi
	+  (Note: Use `&` to combine multiple pieces of evidence in the condition statement `condition()`).
- You can also ask who would win in a game between Tim and Steve, based on prior information about each player.

<!--
- SOLUTION:

```javascript

var model = function(){
	//MODEL
	var strength = mem(function(person) {
		return gaussian(50, 10)
	})

	var lazy = mem(function(person, match) {
		return flip(0.3)
	})

	var pulling = function(person, match) {
		return lazy(person, match) ? strength(person) / 2 : strength(person)
	}

	var totalPulling = function(team, match) {
		return sum(map(function(person) {
			return pulling(person, match)
		}, team))
	}

	var winner = function(team1, team2, match) {
		return totalPulling(team1, match) > totalPulling(team2, match) ? team1 : team2
	}

	var beat = function(team1,team2, match) {
		return winner(team1,team2, match) == team1
	}

	// CONDITION
	condition(
		beat(['Tom'],['Tim'],1) &
		beat(['Tim'],['Steve'],2) &
		beat(['Tim'],['Bill'],3) &
		beat(['Tim'],['Mark'],4) &
		beat(['Tim'],['Kevin'],5) &
    beat(['Tim'],['Tobi'],6)
	)

	//QUERY
	return lazy('Tim',1)
}
var options = {method: 'MCMC', kernel: 'MH', samples: 10000}
var dist = Infer(options,model)

viz(dist)
```
 -->
