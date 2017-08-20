# Doing inference 

- Test the different code snippets by navigating here: [http://webppl.org](webppl.org)
- Check out this chapter of the probmods book for more information: [https://probmods.org/chapters/03-conditioning.html](03-conditioning)

## Conditioning on variables 

- Here is a our simple coin flip model from earlier. 

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
- `Infer` takes an inference procedure, and a function was input. 
- Let's see what happens if we condition on the fact that `a = true`.

```javascript
var flipping_away = function(){
    var a = flip(0.3)
    var b = flip(0.3)
    var c = flip(0.3)
    var result = a + b + c
    condition(a == true) //condition
    return result
}
var options = {method: 'enumerate'}
var dist = Infer(options, flipping_away)
viz(dist)
```

- You can compare this posterior distribution to the prior distribution by simply changing the `condition(a == true)` to `condition(true)` (or by simply commenting out the condition). 

## Conditioning on arbitrary expressions 

- One of the very powerful features of the webppl programming language is that it can condition on arbitrary expressions composed of the variables in the model. 
- You can also return arbitrary expressions. 
- Here is an example: 

```javascript
var flipping_away = function(){
    var a = flip(0.3)
    var b = flip(0.3)
    var c = flip(0.3)
    condition(a + b < 2) //arbitray expression
    return a + b + c //arbitrary expression
}
var options = {method: 'enumerate'}
var dist = Infer(options, flipping_away)
viz(dist)
```

- This is one key strength since it allows us to cleanly separate out the description of the generative model, and the inference procedure. 

## Recursion and rejection sampling

- Recursion is a very powerful technique in which a function calls itself. 
- We can use recursion to implement rejection query.
- Here we are interested in the value of `a`, conditioning on the fact that the sum of `a`, `b`, and `c` is `>= 2`.
- Note how the `flipping_away` function is called inside the `flipping_away` function. The return statement is an if-else statement. If the condition is met `d >= 2`, the value of `a` is returned, otherwise the `flipping_away` function is called again. 

```javascript
var flipping_away = function () {
    var a = flip()
    var b = flip()
    var c = flip()
    var d = a + b + c
    return d >= 2 ? a : flipping_away()
}
viz(repeat(100, flipping_away))
```

## Other inference procedures 

- In the previous examples, we used enumeration (`model: `enumerate``) to do inference. This was only feasible, since the space of possible program executions was rather small. 
- 

## Practice 

- You now have learned about all the bits and pieces that make up the tug of war model. 
- Here is the model: 

```javascript
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
```

- Change the code so that you can do inference with the model. 
- Condition on the fact that `"Tom"` beat `"Bill"`, and return the strength of `"Tom"`. (Note: The `beat` function takes teams as input (i.e. arrays). So even if the team only has one player, you still need to put that player into an array.)
- For the inference options, please use the following: `var options = {{method: 'MCMC', kernel: 'MH', samples: 25000}`. This implements a Markov Chain Monte Carlo inference. 
- If all goes well, the `viz` function will output a density function. You can print out the mean of the distribution by using the `expectation()` function: `print('Expected strength: ' + expectation(dist))`

- Solution: 
<!-- ```javascript
var model = function() {
  var strength = mem(function (person) {return gaussian(50, 10)})
  var lazy = function(person) {return flip(1/3) }
  var pulling = function(person) {
    return lazy(person) ? strength(person) / 2 : strength(person) }
  var totalPulling = function (team) {return sum(map(pulling, team))}
  var winner = function (team1, team2) {
    totalPulling(team1) > totalPulling(team2) ? team1 : team2 }
  var beat = function(team1,team2){winner(team1,team2) == team1}

  condition(beat(['Tom'], ['Steve','Bill']))
  condition(true)
  //condition(beat(['bob', 'sue'],  ['tom', 'jim']))
  // interesting! 
  
  return strength('Tom')
}
var options = {method: 'MCMC', kernel: 'MH', samples: 25000}
var dist = Infer(options,
                 model)

viz(dist)
print('Expected strength: ' + expectation(dist))
``` -->
