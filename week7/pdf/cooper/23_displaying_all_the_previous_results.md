We need to get all the data from our database and display them. To achieve this we need to:

1. generate a page where this will be shown
2. use the existing performanceData provider to get data from the backend
3. save the data in our component
4. Go to our template and display the data

## Generate page for the data

We can call this page results since it will be used to display our results
```shell
$ ionic g page results --no-module
```
What does the `--no-module` do in this case?

On our homepage template, we need to add a button that will this trigger launching of the results page.
```javascript
   ...
      </ion-range>
    </ion-item>
  </ion-list>

  <button block ion-button (click)="calculate(user)">Calculate</button>
  <button block ion-button (click)="showResults()">All Results</button>

    ....
```
On the `home.ts` component, we need to add a method called `showResults()` that will launch the results page

Just for diversity, we will use modals in this scenario
```javascript
showResults() {
  this.modalCtrl.create(ResultsPage).present();
}
```
NOTE: For this to work we need to import the `ModalController` from `'ionic-angular'` and inject it in the constructor with a name `modalCtrl`

Make sure your app is still working before moving on

## Add function for getting this data using the provider
We reuse the `performanceData` provider in this case and add a function below the existing `saveData()` function

```javacript
  ...
  getResults() {
    return this._tokenService
      .get('performance_data')
      .map(results => results.json());
  }
  ...
```

## Store the results in our component

In `results.t`s component file we need to declare a variable `result` and assign it to an empty array. We want the application to fetch all the results from the backend every time the `result` page is loaded. To make this possible we make use of ionic [ifecycle events](https://blog.ionicframework.com/navigating-lifecycle-events/), in our case we utilize the `ionViewDidLoad` event

Our `results.ts` file should look like this:
```javascript
...


@Component({
  selector: 'page-results',
  templateUrl: 'results.html'
})
export class ResultsPage {
  results = [];
  constructor(
    private performanceData: PerformanceDataProvider,
    public navCtrl: NavController,
    public navParams: NavParams
  ) {}

  ionViewDidLoad() {
    this.performanceData
      .getResults()
      .subscribe(data => (this.results = data.entries));
  }
}
```
In the subscribe function we store what we get to the `results` variable. We can then loop over this `results` in our template and show them in nice ionic cards.

## Display the results in the view

We fetched results from the backend using ionic lifecycle event and assigned what we got to a variable called `results`. Lets loop over this in our template: `results.html`

```javascript
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
## Angular Pipes
```javascript
    <ion-card-header>
      {{result.created_at | date:'medium'}}
    </ion-card-header>
```
You can see that in the date section there is a pipe `|` that has been used. This is what we use to format our date to a more readable format

