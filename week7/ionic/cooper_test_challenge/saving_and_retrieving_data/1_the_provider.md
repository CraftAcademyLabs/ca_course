## The provider

We need to use a service/provider to achieve this so its time to generate another one, we can call it `performance-data`
```shell
$ ionic g provider performance-data
```
Clean this provider and import the `Angular2TokenService` module then inject it to the `PerformanceDataProvider`. Your code should now look like this:
```javascript
import { Injectable } from '@angular/core';
import { Angular2TokenService } from 'angular2-token';
import 'rxjs/add/operator/map';

@Injectable()
export class PerformanceDataProvider {
  constructor(private _tokenService: Angular2TokenService) {}

  saveData(data) {
    return this._tokenService.post('performance_data', data).map(data => data);
  }
}
```
## The component

When we click on the `Calculate` button on the homepage we need to save the results at the same instance. By now we know that when the button is clicked then the `calculate()` function is executed. Lets add code here that will use our provider above to make call to the backend and save the data to our backend
```javascript
import { PerformanceDataProvider } from '../../providers/performance-data/performance-data';

...

  calculate(user) {
    this.person.age = user.age;
    this.person.gender = user.gender;
    this.person.doAssessment(user.distance);
    this.performanceData
      .saveData({ performance_data: { data: { message: this.person.assessmentMessage } } })
      .subscribe(data => console.log(data));
  }
	```
Now, clicking on the button will be saving our results to the database

You can confirm this by opening up the browser developer tools and looking under network tab, do you see that there is a response from the server

