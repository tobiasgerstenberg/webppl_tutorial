# WebPPL basics

- Test the different code snippets by navigating here: [http://webppl.org](webppl.org)
- You can open a new file and save any code you've written.
- Check out [https://probmods.org/chapters/13-appendix-js-basics.html](https://probmods.org/chapters/13-appendix-js-basics.html) for other Javascript basics, including mathematical and logical operations.

## Declaring variables

- Use `var` to name objects and assign a value to them.
- Use print() to display results.

```javascript
var strength = 2.5 // here we bind the symbol `strength` to the value 2.5
print(strength)
```

```javascript
var participantName = "Tobi" // can bind the symbol `participantName` to a string
print(participantName)
```

- Objects are a useful data-structure in JS, similar to dictionaries in Python.
- Here is how we create an object, and then refer to an object's attribute.

```javascript
var tobi = {
	first_name: "Tobi",
	last_name: "Gerstenberg",
	strength: 2.5,
	lazy: false, //note the logical variable here
}
print(tobi.lazy)
```

- We can also create arrays, like so.
- Note that javascript/webppl use 0 indexing. The first element of an array is indexed with 0 (not a 1).

```javascript
var instructors = ["Tomer","Tobi","Kevin"] //an array of instructors
print(instructors) //displaying the whole team
print(instructors[0]) //displaying the first element of the instructor team
```

## if-then statements

- The general form is: if (condition) {consequence} else {alternative}
- Let's see whether Tobi is lazy?!

```javascript
if (tobi.lazy){
	print("Tobi is lazy!")
} else {
	print("Tobi is not lazy!")
}
```
- There is also an abbreviated form of if-then for lazy coders.
- Here, the general form is: `condition ? consequence : alternative`

```javascript
tobi.lazy ? print("Tobi is lazy!") : print("Tobi is not lazy!")
```

## Defining functions

- We use the `function()` primitive to define functions.
- The general form of a function expression is: `function(arguments) {body}`
- Here is an example:

```javascript
var return_winner = function(personA, personB){
	if (personA.strength > personB.strength)
		{return [personA.first_name, personA.last_name]}
	else
		{return [personB.first_name, personB.last_name]}  
}
```

- Let's see who would win in a tug of war between Tomer and Tobi.
- Make sure that you have all the bits and pieces you need in the same code window.

```javascript
var tomer = {
	first_name: "Tomer",
	last_name: "Ullman",
	strength: 10,
	lazy: true,
}
print(return_winner(tobi,tomer) + " would win a tug of a war")
```

## Higher order functions

- Higher order functions are functions that take other functions as their input.
- For example, `map` is such a higher order function. It takes another function as an argument, and applies that function to a list of arguments in turn.

```javascript
var my_square = function(x){return x*x};
var some_numbers = [1,2,3,4]
print(map(my_square, some_numbers))
```

## Practice

- Create several student objects (like the instructor objects from before). Assign them several properties, including strength.

- Create a function `show_strength()` for displaying the strength of an object or student.

- Map the function `show_strength()` to an array of students and instructors.

- Amend the `return_winner` function to handle ties (hint: you can use the abbreviated form of if-else to string together conditions).
