
We will use [Angular Chart](http://jtblin.github.io/angular-chart.js/) to display users historical data.
```shell
$ npm install ng2-charts --save
$ npm install chart.js --save
```
Open and edit your `app.module.ts` the add this import.
```javascript
# src/app/app.module.ts
import { ChartsModule } from 'ng2-charts';
```
Then declare the charts module in imports array.
```javascript
# src/app/app.module.ts
imports: [
  BrowserModule,
  IonicModule.forRoot(MyApp),
  ChartsModule
],
```
Next, in your `results` page component, declare two charts type, `data` and `labels` variables
```javascript
# src/pages/results/results.ts
export class ResultsPage {
  results = [];
  labels = [];
  data = [];
  doughnutChartType: string = 'doughnut';
  radarChartType: string = 'radar';

  view: string = 'data';

  constructor(
    private performanceData: PerformanceDataProvider,
    public navCtrl: NavController,
    public navParams: NavParams,
  ) {}
}
```
Okay, here comes the tricky part. We want to display two charts on our view. One Doughnut Chart and one Radar Chart. The tricky part is that we only want to display labels for values that are actually stored in the collection of historical data. Meaning for instance that if a user has stored several "Average" and "Above Average" entries, then we should only show those two labels with a value. Nothing else. Same thing goes for both chart types.

So what we need to do is to go through the `this.results` and get unique values and store them in an array. This is the responsibility of the following function.
```javascript
# src/pages/results/results.ts
getLabels(collection: any) {
  let uniqueLabels = [];

  collection.forEach(entry => {
    if (entry.data.message && uniqueLabels.indexOf(entry.data.message) === -1) {
      uniqueLabels.push(entry.data.message);
    }
  })

  return uniqueLabels;
}
```
The next thing we need to do is to get the count for how many times each label occurs in results We will do this with the following function:
```javascript
# src/pages/results/results.ts
getCount(collection:any, value:any) {
  let count = 0;

  collection.forEach(entry => {
    count += entry.data.message == value ? 1 : 0;
  })

  return count;
}
```
And we put those methods to use:
```javascript
# src/pages/results/results.ts
ionViewDidLoad() {
  this.performanceData
    .getResults()
    .subscribe(data => {
      this.results = data.entries;
      this.labels = this.getLabels(this.results);

      this.labels.forEach(label => {
        this.data.push(this.getCount(this.results, label));
      })
    });
}
```
Modify the content of your `results` template with the following code. We make use of a segment here to split the raw data with the charts.
```javascript
# src/pages/results/results.html
<ion-header>
  <ion-navbar>
    <ion-title>Results</ion-title>
  </ion-navbar>
</ion-header>


<ion-content padding>
  <div padding>
    <ion-segment [(ngModel)]="view">
      <ion-segment-button value="data">
        Data
      </ion-segment-button>
      <ion-segment-button value="charts">
        Charts
      </ion-segment-button>
    </ion-segment>
  </div>


  <div [ngSwitch]="view">
    <ion-list *ngSwitchCase="'data'">
      <ion-card  *ngFor="let result of results">
        <ion-card-header>
          {{ result.created_at | date:'medium' }}
        </ion-card-header>
        <ion-card-content>
          Result: {{ result.data.message }}
        </ion-card-content>
      </ion-card>
    </ion-list>

    <ion-list *ngSwitchCase="'charts'">
      <div padding-bottom style="display: block">
        <canvas baseChart [data]="data"
                [labels]="labels"
                [chartType]="doughnutChartType"
                (chartHover)="chartHovered($event)"
                (chartClick)="chartClicked($event)">
        </canvas>
      </div>

      <div padding-top style="display: block">
        <canvas baseChart [data]="data"
                [labels]="labels"
                [chartType]="radarChartType"
                (chartHover)="chartHovered($event)"
                (chartClick)="chartClicked($event)">
        </canvas>
      </div>
    </ion-list>
  </div>
</ion-content>
```
Note that we are adding two event handlers for each chart. One that will be triggered on hoover and another one that will be triggered on click. These methods will need to be defined (please see below) but the question you want to ask yourself is if they are relevant in the context of a mobile application?
```javascript
# src/pages/results/results.ts
chartClicked(event: any): void {
    console.log(event);
}

chartHovered(event: any): void {
  console.log(event);
}
```
If you run the application now it should look something like this.

![charts_image](/images/charts_image.png)

That looks pretty cool, right? ;-)

There is of course many more charts that you can display on this view if you like. Especially if you make the choice of storing not only the `cooperMessage` but also the distance for each entry.