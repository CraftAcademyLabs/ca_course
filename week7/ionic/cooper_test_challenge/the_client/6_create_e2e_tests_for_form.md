At this stage we have a good skeleton app to get started with so we can add a form that asks a user for some details so our app will look like this:

￼![Cleaned out not needed pages](/images/cc_app_cleaned.png)

First we write our acceptance tests.

```javascript
#app.e2e-spec.ts

it('fill in form', () => {
	page.fillInForm(1000, 'Female', 20);
	expect(page.results_card_header()).toContain('Cooper Test Result');
	expect(page.results_card_content()).toContain('Gender: female, Age: 20  Result: Poor');
});
```

And then in our `page` object we need to create the `fillInForm` function and we want that function to return the results of our calculations.

```javascript
#app.po.ts

fillInForm(distance, gender, age) {

	element(by.css('.text-input')).clear().then(() => {  // first we need to clear the default value
		element(by.css('.text-input')).sendKeys(distance); // fill in the distance
    }) 
  
	element(by.css('.select')).click().then(() => {  // find the dropdown and click on it
		browser.sleep(500); // we sleep for a half a second to make sure the popup has popped up

		element(by.cssContainingText('.button-inner', gender)).click(); // click the gender option you want
		element(by.cssContainingText('.button-inner', 'OK')).click();  // click the OK button

	})

	browser.sleep(500); // we sleep for half a second to make sure the popup has disappeared

	element(by.cssContainingText('.button', 'Calculate')).click();  // click the calculate button

}

results_card_header() {
	return element(by.css('ion-card-header')).getText();
} 

results_card_content() {
	return element(by.css('ion-card-content')).getAttribute('textContent');
} 
```

Now we can create that form by typing the following markup to your `home.html` file within the ion-content tag

```javascript
...
<ion-list>
	<ion-item>
		<ion-label floating>Distance</ion-label>
		<ion-input type="number" [(ngModel)]="user.distance"></ion-input>
	</ion-item>
	<ion-item>
		<ion-label>Gender</ion-label>
		<ion-select [(ngModel)]="user.gender">
			<ion-option value="female">Female</ion-option>
			<ion-option value="male">Male</ion-option>
		</ion-select>
	</ion-item>
	<ion-item>
		<ion-label>Age:
			<ion-badge *ngIf="user.age"> {{user.age}}</ion-badge>
		</ion-label>
		<ion-range min="10" max="120" [(ngModel)]="user.age" >
			<ion-label range-left>10</ion-label>
			<ion-label range-right>120</ion-label>
		</ion-range>
	</ion-item>
</ion-list>

<button block ion-button (click)="calculate()">Calculate</button>
...
```

At this stage, if you visit the app in your browser, you will get an error about not being able to read property `distance` or `undefined`. This is because we haven't defined a `user` in our component. Let's fix that. We can assign an empty object to it for now.

first we write our test in `home.spec.ts`

```javascript
it("should have user array", () => {
  expect(homepage.user).toEqual({});
});
```

And in our `home.ts`

```javascript
export class HomePage {
	user: any = {};
	...

}
```

Save this change and head back to your browser and everything should work now.

Click on the button, what happens? Why do you think it fails? Read through the error and try to figure out what the problem might be here.

IMPORTANT Try to answer this before you move on to the next step.

Incase you were not able to know why it failed, it failed because the function tied to the button is not yet defined in the component. First we create the test for it and then we define it:

```javascript
# home.spec.ts
it("should have calculate function", () => {
  expect(homepage.calculate).toBeTruthy();
});
```

```javascript
# home.ts
export class HomePage {
	user: any = {};

	constructor(public navCtrl: NavController) {}

	calculate() {
		console.log(this.user);
	}
}
```

Our function currently logs out the `user` object everytime you click on the button. Open your developer tools console on the browser and play around.

Did you realize that the app starts with every fields empty? We can give it default values that can then be used when the app starts, we do this by initializing `user` within our constructor function.

First we update our `should have user array` test.

```javascript
# home.spec.ts
it("should have user array default values", () => {
  expect(homepage.user).toEqual({ distance: 1000, age: 20 });
});
```

```javascript
# home.ts
export class HomePage {
	...
	constructor(public navCtrl: NavController) {
		this.user = { distance: 1000, age: 20 };
	}

	...
}
```

￼![cooper initial values](/images/cooper-intial-values.png)
