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
var inference = Infer({model:'enumerate'},flipping_away)
viz(inference)
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
var inference = Infer({model:'enumerate'},flipping_away)
viz(inference)
```

- This is one key strength since it allows us to cleanly separate out the description of the generative model, and the inference procedure. 

## Inference procedures 

- In the previous examples, we used enumeration (`model: `enumerate``) to do inference. This was only feasible, since the space of possible program executions was rather small. 
- 

## Practice 