# WebPPL basics

- Test the different code snippets by navigating here: [webppl.org](http://webppl.org)
- You can open a new file and save any code you've written.
- Check out [https://probmods.org/chapters/13-appendix-js-basics.html](https://probmods.org/chapters/13-appendix-js-basics.html) for other Javascript basics, including mathematical and logical operations.

## Declaring variables

- Use `var` to name objects and assign a value to them.
- Use display() to display results.

```javascript
var strength = 2.5 // here we bind the symbol `strength` to the value 2.5
display(strength)
```

- Objects are a useful data-structure in JS, similar to dictionaries in Python.
- Here is how we create an object, and then refer to an object's attribute.

```javascript
var tobi = {
	firstName: "Tobi",
	lastName: "Gerstenberg",
	strength: 2.5,
	lazy: false, //note the logical variable here
}
display(tobi.lazy)
```

- We can also create arrays, like so.
- Note that javascript/webppl use 0 indexing. The first element of an array is indexed with 0 (not a 1).

```javascript
var instructors = ["Tobi","Kevin"] //an array of instructors
display(instructors) //displaying the whole team
display(instructors[0]) //displaying the first element of the instructor team
```

## if-then statements

- The general form is: if (condition) {...} else if (condition) {...} else {...}
- Let's see whether Tobi is strong:

```javascript
if (tobi.strength > 5){
	display("Tobi is strong!")
} else if (tobi.strength < 5) {
	display("Tobi is weak!")
} else {
	display("Tobi is neither weak nor strong.")
}
```

- There is also an abbreviated form of if-then for lazy coders.
- Here, the general form is: `condition ? consequence : alternative`

```javascript
tobi.lazy ? display("Tobi is lazy!") : display("Tobi is not lazy!")
```

## Defining functions

- We use the `function()` primitive to define functions.
- The general form of a function expression is: `function(arguments){body; return ... }`
- Here is an example:

```javascript
var returnWinner = function(personA, personB){
if (personA.strength > personB.strength){
		return personA.firstName
	} else {
		return personB.firstName
	}  
}
```

- Let's see who would win in a tug of war between Kevin and Tobi.
- Make sure that you have all the bits and pieces you need in the same code window.

```javascript
var tobi = {
	firstName: "Tobi",
	lastName: "Gerstenberg",
	strength: 2.5,
	lazy: false, //note the logical variable here
}

var kevin = {
	firstName: "Kevin",
	lastName: "Smith",
	strength: 10,
	lazy: true,
}

var returnWinner = function(personA, personB){
if (personA.strength > personB.strength){
		return personA.firstName
	} else {
		return personB.firstName
	}  
}

display(returnWinner(kevin,tobi) + " would win a tug of a war")
```

## Higher order functions

- Higher order functions are functions that take other functions as their input.
- For example, `map` is such a higher order function. It takes another function as an argument, and applies that function to a list of arguments in turn.

```javascript
var mySquare = function(x){return x*x}
var someNumbers = [1,2,3,4]
display(map(mySquare, someNumbers))
```

## Some useful keyboard shortcuts for use in `webppl.org`

- `cmd+/`: comment/uncomment code 
- select code and press `tab`to fix indentation -
- `cmd+Enter`: run code 

## Practice

1. Create several student objects (like the instructor objects from before). Assign them several properties, including strength.

<!--
- SOLUTION:

```javascript
var kevin = {
	firstName: "Kevin", lastName: "Smith", strength: 4
}

var andrei = {
	firstName: "Andrei", lastName: "Barbu", strength: 12
}

var xavier = {
	firstName: "Xavier", lastName: "Boix", strength: 12
}
```-->

2. Create a function `showStrength()` for displaying the strength of a student.

<!--
- SOLUTION:

```javascript
var showStrength = function(person) {
	display(person.strength)
}
showStrength(tobi)
```-->

3. Map the function `showStrength()` to an array of students and instructors.

<!--
- SOLUTION:

```javascript
// Map the showStrength() function onto a group
var everyone = [tobi, tomer, kevin, andrei, xavier]
display("Everyone's strengths:")
map(showStrength, everyone)
```-->

4. Amend the `returnWinner()` function to handle ties.

<!--
-SOLUTION:

```javascript
var returnWinner = function(personA, personB){
	if (personA.strength > personB.strength){
		return [personA.lastName, personA.firstName]
	} else  if (personA.strength < personB.strength){
		return [personB.lastName, personB.firstName]
	} else {
		return ["tied","match"]
	}
}

display(returnWinner(tobi, tomer) + " won a tug of war")
display(returnWinner(andrei, xavier) + " won a tug of war")

```-->
