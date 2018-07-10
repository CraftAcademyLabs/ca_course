### Datatypes
```js
var age =  18; // number

var name =  "Thomas"; // string

var name = {first:"Thomas", last:"Ochman"}; // object

var truth =  false; // boolean

var company = ["Craft","Academy","SE"]; // array

var a; typeof a; // undefined

var a =  null; // value null
```

### Objects
```js
var student = { // object name  
	firstName:"Thomas", // list of properties and values  
	lastName:"Ochman",  
	age:46,  
	height:186,  
	fullName : function() { // object function  
		return  this.firstName +  " "  +  this.lastName;  
	}  
};  


student.age =  47; // setting value  
student[age]++; // incrementing  
name = student.fullName(); // call object function
```

### Array
```js
var team = ["Magnus", "Faraz", "Sophie"];  
var team =  new  Array("Magnus", "Faraz", "Sophie"); // declaration  

alert(team[1]); // access value at index, first item being [0]  
team[0] =  "Thomas"; // change the first item  
```
### Function
A function is a block of code designed to perform a particular task.
```js
function ourAgeCombined(yourage, myage) {  
	return yourage + myage;
}
```
### Promises
```js
var isMomHappy = false;

// Promise
var willIGetNewPhone = new Promise(
    function (resolve, reject) {
        if (isMomHappy) {
            var phone = {
                brand: 'Samsung',
                color: 'black'
            };
            resolve(phone); // fulfilled
        } else {
            var reason = new Error('mom is not happy');
            reject(reason); // reject
        }

    }
);
```

### Const, let & var
1.  `var`  declarations are globally scoped or function scoped while  `let`and  `const`  are block scoped.

2.  `var`  variables can be updated and re-declared within its scope;  `let`variables can be updated but not re-declared;  `const`  variables can neither be updated nor re-declared.

3.  They are all hoisted to the top of their scope but while  `var`variables are initialized with  `undefined`,  `let`  and  `const`  variables are not initialized.

4.  While  `var`  and  `let`  can be declared without being initialized,  `const`must be initialized during declaration.
