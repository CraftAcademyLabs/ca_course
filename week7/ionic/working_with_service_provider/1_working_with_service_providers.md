Now that we have our interface setup with all the necessary inputs, we need to implement the actual logic to perform the calculations. This is where the code base we pointed you to at the first step will come to play. Now we could simply get that code and add it directly to our component. We did something similar in the previous challenge (BMI Calculator). This is okay for small applications, but as your app grows, it becomes difficult to maintain it. We want to be as modular as we can generate components that do one thing and do it well. In software development, this is often referred to as the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle). Following this kind of patterns while building your applications will lead to better software, that is easy to maintain and extend.

With this in mind, how do we then go about integrating our logic to the application? You might think, oh! let's just generate a new component for this. That's not entirely wrong, but, you really want to make use of components when you have some visual aspect to them. When it comes to business logic, we can encapsulate them in services. So, we'll create a service which role in our application will be to perform the calculations we need. Then we'll use that service in our page component. The component doesn't need to know how the calculations are made. The service here is like a black box, to which we will feed some information (i.e. gender, distance, etc...) and it'll return the assessments results to the component.

So how do we add services to an ionic application? We can do so by generating a provider. In your terminal run the following command:

```shell
$ ionic g provider person
```

Adjust the provider src/providers/person/person.ts to look like this:

```javascript
import { Injectable } from '@angular/core';
import 'rxjs/add/operator/map';

import { CooperProvider } from '../cooper/cooper';

@Injectable()
export class PersonProvider {
public gender: string;
public age: number;
public assessmentMessage: string;

    constructor(private cooper: CooperProvider) {}

    doAssessment(distance: number): void {
    	this.assessmentMessage = this.cooper.assess(this, distance);
    }

}
```

And create our test file for the provider `src/providers/person/person.spec.ts`

```javascript
# person.spec.ts

import { CooperProvider } from './../../providers/cooper/cooper';  // TODO Replace this with correct tests
import { PersonProvider } from './../../providers/person/person';
import { HomePage } from "./home";
import { TestBed, async, inject } from "@angular/core/testing";
import { IonicModule, Platform, NavController } from "ionic-angular";
import { StatusBar } from "@ionic-native/status-bar";
import { SplashScreen } from "@ionic-native/splash-screen";
import { PlatformMock, StatusBarMock, SplashScreenMock, NavControllerMock } from "ionic-mocks";


describe("Person Component", () => {
	let personProvider, fixture;

	beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [
        PersonProvider
      ],
      imports: [IonicModule.forRoot(PersonProvider)],
      providers: [
        { provide: Platform, useFactory: () => PlatformMock.instance() },
        { provide: StatusBar, useFactory: () => StatusBarMock.instance() },
        { provide: SplashScreen, useFactory: () => SplashScreenMock.instance() },
        { provide: NavController, useFactory: () => NavControllerMock.instance() },
        CooperProvider
      ]
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(PersonProvider);
    personProvider = fixture.componentInstance;
  });

	it("should create the person provider", () => {
		expect(personProvider).toBeTruthy();
		expect(personProvider instanceof PersonProvider).toEqual(true);
	});

	it('doassessment should be defined', () => {
		spyOn(personProvider, 'doAssessment');

		personProvider.doAssessment(2500);

		expect(personProvider.doAssessment).toHaveBeenCalled();
		expect(personProvider.doAssessment).toHaveBeenCalledWith(2500);
	});

	it('cooper provider should be called', inject([CooperProvider], (cooper) => {
		spyOn(cooper, 'assess');
		personProvider.doAssessment(2500);

		expect(cooper.assess).toHaveBeenCalled();

	}));
});
```

Note that we have imported another provider called `CooperProvider` that we need to define. Let's generate it.

```shell
$ ionic g provider cooper
```

Then edit it to look like this:

```javascript
import { Injectable } from '@angular/core';
import 'rxjs/add/operator/map';

@Injectable()
export class CooperProvider {
constructor() {
console.log('Hello CooperProvider Provider');
}

    private ratings: any = [
    	'Excellent',
    	'Above average',
    	'Average',
    	'Below average',
    	'Poor'
    ];

    private cooperTable: any = {
    	female: {
    		'13-14': ['>2000', '1900-1999', '1600-1899', '1500-1599', '<1500'],
    		'15-16': ['>2100', '2000-2099', '1700-1999', '1600-1699', '<1600'],
    		'17-19': ['>2300', '2100-2299', '1800-2099', '1700-1799', '<1700'],
    		'20-29': ['>2700', '2200-2699', '1800-2199', '1500-1799', '<1500'],
    		'30-39': ['>2500', '2000-2499', '1700-1999', '1400-1699', '<1400'],
    		'40-49': ['>2300', '1900-2299', '1500-1899', '1200-1499', '<1200'],
    		'50+': ['>2200', '1700-2199', '1400-1699', '1100-1399', '<1100']
    	},
    	male: {
    		'13-14': ['>2700', '2400-2699', '2200-2399', '2100-2199', '<2100'],
    		'15-16': ['>2800', '2500-2799', '2300-2499', '2200-2299', '<2200'],
    		'17-19': ['>3000', '2700-2999', '2500-2699', '2300-2499', '<2300'],
    		'20-29': ['>2800', '2400-2799', '2200-2399', '1600-2199', '<1600'],
    		'30-39': ['>2700', '2300-2699', '1900-2299', '1500-1999', '<1500'],
    		'40-49': ['>2500', '2100-2499', '1700-2099', '1400-1699', '<1400'],
    		'50+': ['>2400', '2000-2399', '1600-1999', '1300-1599', '<1300']
    	}
    };

    public assess(person, distance: number): string {
    	return this.getRating(person, distance);
    }

    private ageRange(age: number): string {
    	switch (true) {
    		case age >= 13 && age <= 14:
    			return '13-14';
    		case age >= 15 && age <= 16:
    			return '15-16';
    		case age >= 17 && age <= 19:
    			return '17-19';
    		case age >= 20 && age <= 29:
    			return '20-29';
    		case age >= 30 && age <= 39:
    			return '30-39';
    		case age >= 40 && age <= 49:
    			return '40-49';
    		case age >= 50:
    			return '50+';
    		default:
    			return 'invalid range';
    	}
    }

    private getRating(person, distance): string {
    	const ageRange = this.ageRange(person.age);

    	if (ageRange === 'invalid range') {
    		return 'Invalid age range';
    	}

    	const distanceRanges = this.cooperTable[person.gender.toLowerCase()][
    		ageRange
    	];

    	let ratingIndex: number;

    	distanceRanges.forEach((dRange, index) => {
    		if (
    			(dRange.match(/>\d*/) && distance >= parseInt(dRange.slice(1), 10)) ||
    			(dRange.match(/<\d*/) && distance < parseInt(dRange.slice(1), 10))
    		) {
    			ratingIndex = index;
    		} else {
    			const minMax = dRange.split('-');
    			const min = parseInt(minMax[0], 10);
    			const max = parseInt(minMax[1], 10);

    			if (distance >= min && distance <= max) {
    				ratingIndex = index;
    			}
    		}
    	});

    	return this.ratings[ratingIndex];
    }

}
```

And now we can create the test file for the cooper provider `touch src/providers/cooper/cooper.spec.ts`

```javascript
# cooper.spec.ts

import { PersonProvider } from "./../person/person";
import { CooperProvider } from "./cooper";

describe("CooperProvider", () => {
  let cooperProvider: CooperProvider;

  beforeEach(() => {
    cooperProvider = new CooperProvider();
  });

  it('should create the cooper provider', () => {
    expect(cooperProvider).toBeTruthy();
    expect(cooperProvider instanceof CooperProvider).toEqual(true);
  });

  it('assess should return Excellent', () => {
    let person = { age: 13, gender: 'male' };
    expect(cooperProvider.assess(person, 4000)).toBeDefined();
    expect(cooperProvider.assess(person, 4000)).toEqual('Excellent');
  });

  it('assess should return Above average', () => {
    let person = { age: 15, gender: 'male' };
    expect(cooperProvider.assess(person, 2500)).toEqual('Above average');
  });

  it('assess should return Average', () => {
    let person = { age: 17, gender: 'male' };
    expect(cooperProvider.assess(person, 2500)).toEqual('Average');
	});

  it('assess should return Below average', () => {
    let person = { age: 25, gender: 'male' };
    expect(cooperProvider.assess(person, 2100)).toEqual('Below average');
	});

  it('assess should return Poor', () => {
    let person = { age: 32, gender: 'male' };
    expect(cooperProvider.assess(person, 1400)).toEqual('Poor');
	});

	it('assess should return Invalid age range when outside of age range', () => {
		let person = { age: 12, gender: 'male' };
    expect(cooperProvider.assess(person, 1400)).toEqual('Invalid age range');
	})
});
```

Go over to the root module, `src/app/app.module.ts`, you will realize that the providers we generated are automatically imported.

We now have our two providers, let's update the `calculate()` function to make use of them in order to perform the assessment.

Start by adding this test to the `home.spec.ts` file.

```javascript
it("calculate function should call person provider doAssessment function", inject(
  [PersonProvider],
  person => {
    homepage.user = { age: 25, gender: "female", distance: 2500 };
    spyOn(person, "doAssessment").and.returnValue("Above average");

    homepage.calculate();

    expect(person.doAssessment).toHaveBeenCalled();
    expect(person.doAssessment).toHaveBeenCalledWith(2500);
    expect(person.age).toEqual(25);
    expect(person.gender).toEqual("female");
  }
));
```

Back in `src/pages/home/person.ts`, import the `PersonProvider` at the top of the file.

`import { PersonProvider } from '../../providers/person/person';`
Then update your constructor and calculate function as follows:

```javascript
...
constructor(
public navCtrl: NavController,
public person: PersonProvider
) {
this.user = { distance: 1000, age: 20, gender: 'female' };
}

calculate() {
	this.person.age = this.user.age;
	this.person.gender = this.user.gender;

	this.person.doAssessment(this.user.distance);
	console.log(this.person.assessmentMessage);

}
```

If you now head back to your your browser and set some values then press the calculate button, you should see the result printed in your browser's console. Next, we will add some markup to our page to display the result to our user.
