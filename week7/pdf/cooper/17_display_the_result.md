We'll add some markup to the page template and this section of the page should only be displayed once the result of our calculations are available. Add the following snippet just before the closing `</ion-content>`
```javascript
<ion-card *ngIf="person?.assessmentMessage">
	<ion-card-header>
		Cooper Test Results
	</ion-card-header>
	<ion-card-content>
		Gender: {{person?.gender}}, Age: {{person?.age}} <br/> Result: {{person?.assessmentMessage}}
	</ion-card-content>
</ion-card>
```
Let's see if that works! 

Run e2e tests 
```shell
$ npm run e2e
```

![e2e fill in form](https://raw.githubusercontent.com/magnus-thor/ca_course/cooper_challenge_AUT/images/e2e_fill_in_form_passing.png)

Head over to your browser and give it a try. You should now see the results of the test displayed on the page as follows:

![Display results](https://raw.githubusercontent.com/magnus-thor/ca_course/cooper_challenge_AUT/images/cooper-display-result.png)



Feel free to play around with different values.

Now that the basic functionality of our application is in place. We can move to the next steps which will involve connecting it to our backend to save the results of our calculations for every users.