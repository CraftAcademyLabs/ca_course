# Saving and retrieving data - step 5

## The provider

We need to use a service/provider to achieve this so its time to generate another one, we can call it `perfomance-data` 

```shell
$ ionic g provider perfomance-data
```

Clean this provider and import the `Angular2TokenService` module then inject it to the `PerformanceDataProvider`. Your code should now look like this:

```typescript
import { Injectable } from '@angular/core';
import { Angular2TokenService } from 'angular2-token';
import 'rxjs/add/operator/map';

@Injectable()
export class PerfomanceDataProvider {
  constructor(private tokenService: Angular2TokenService) {}

  saveData(data) {
    return this.tokenService.post('performance_data', data).map(data => data);
  }
}
```

## The component

When we click on the `Calculate` button on the homepage we need to save the results at the same instance. By now we know that when the button is clicked then the `calculate()` function is executed. Lets add code here that will use our provider above to make call to the backend and save the data to our backend

```typescript
import { PerfomanceDataProvider } from '../../providers/perfomance-data/perfomance-data';

...

  calculate(user) {
    this.result = this.person.doAssessment(user, user.distance);
    this.perfomanceData
      .saveData({ performance_data: { data: { message: this.result } } })
      .subscribe(data => console.log(data));
  }
```

Now, clicking on the button will be saving our results to the database

You can confirm this by opening up the browser developer tools and looking under network tab, do you see that there is a response from the server



## Displaying all the previous results \(Retrieving data\)

We need to get all the data from our database and display them. To achieve this we need to:

1. generate page where this will be shown
2. use the existing \`perfomanceData\` provider to get data from the backend
3. save the data in our component
4. Go to our template and display the data



### generate page for the data

We can call this page \`results\` since it will be used to display our results

```shell
$ ionic g page results --no-module
```

What does the `--no-module` do in this case?



On our homepage template we need to add a button that will this trigger launching of the results page.

```html
   ...
      </ion-range>
    </ion-item>
  </ion-list>

  <button block ion-button (click)="calculate(user)">Calculate</button>
  <button block ion-button (click)="showResults()">All Results</button>

  <ion-card *ngIf="result">
    <ion-card-header>
    ....
```

On the `home.ts` component we need to add a method called \`showResults\(\)\` that will launch the results page

Just for diversity we will use modals in this scenario

```typescript
  showResults() {
    this.modalCtrl.create(ResultsPage).present();
  }
```

**NOTE**: For this to work we need to import the `ModalController` from `'ionic-angular'`  and inject it in the constructor with a name `modalCtrl`

Make sure your app is still working before moving on



### Add function for getting this data using the provider

We reuse the `perfomanceData` provider in this case and add a function below the existing `saveData()`  function

```typescript
  ...
  getResults() {
    return this.tokenService
      .get('performance_data')
      .map(results => results.json());
  }
  ...

```

### Store the results in our component

In `results.ts` component file we need to declare a variable `result` and assign it to and empty array. We want the application to fetch all the results from the backend every time the `results` page is loaded. To make this possible we make use of ionic [lifecycle events](http://blog.ionic.io/navigating-lifecycle-events/), in our case we utilize the `ionViewDidLoad` event

Our `results.ts` file should look like this:

```typescript
...


@Component({
  selector: 'page-results',
  templateUrl: 'results.html'
})
export class ResultsPage {
  results = [];
  constructor(
    private perfomanceData: PerfomanceDataProvider,
    public navCtrl: NavController,
    public navParams: NavParams
  ) {}

  ionViewDidLoad() {
    this.perfomanceData
      .getResults()
      .subscribe(data => (this.results = data.entries));
  }
}

```

In the subscribe function we store what we get to the `results` variable . We can then loop over this `results` in our template and show them in nice ionic cards.



### Display the results in the view

We fetched results from the backend using ionic lifecycle event and assigned what we got to a variable called `results`. Lets loop over this in our template: `results.html`

```html
...
<ion-content padding>

  <ion-card  *ngFor="let result of results">
    <ion-card-header>
      {{result.created_at | date:'medium'}}
    </ion-card-header>
    <ion-card-content>
      Result: {{result.data.message}}
    </ion-card-content>
  </ion-card>

</ion-content>

...
```



#### Angular Pipes

```html
    <ion-card-header>
      {{result.created_at | date:'medium'}}
    </ion-card-header>
```



You can see that in the date section there is a pipe `|` that has been used. This is what we use to format our date to a more readable format



