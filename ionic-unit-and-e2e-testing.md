\#\# Ionic Testing guide

Lets start with the scaffolded BMI\_calculator app and Setup the testing environment.

We follow this guide, [https://class.craftacademy.co/courses/course-v1:CraftAcademy+CA-CAMP+2018-april/courseware/84536a87ce074509bcf543dc7593035c/1e390334d58245f0975c714271e94216/?activate\_block\_id=block-v1%3ACraftAcademy%2BCA-CAMP%2B2018-april%2Btype%40sequential%2Bblock%401e390334d58245f0975c714271e94216](https://class.craftacademy.co/courses/course-v1:CraftAcademy+CA-CAMP+2018-april/courseware/84536a87ce074509bcf543dc7593035c/1e390334d58245f0975c714271e94216/?activate_block_id=block-v1%3ACraftAcademy%2BCA-CAMP%2B2018-april%2Btype%40sequential%2Bblock%401e390334d58245f0975c714271e94216), to setup Unit and Acceptance tests for Ionic.

\#\#\# Next we write tests for the scaffolded app BMI\_calculator

Start our test script and have it run in background.

```
$ npm run test-coverage
```

This will open a new browser window![](/assets/karma_init_window.png)

we need to click the DEBUG button to get a overview of our tests.![](/assets/karma_debug_window.png)

\#\#\#\# Unit Testing

Lets start by creating our first unit test, within \`src/app\` folder create a file \`app.component.spec.ts\`

// introduce TestBed

```javascript
# src/app/app.component.spec.ts
import { MyApp } from './app.component'
import { TestBed } from '@angular/core/testing'
import { IonicModule, Platform } from 'ionic-angular';
import { PlatformMock, StatusBarMock, SplashScreenMock } from 'ionic-mocks'
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';
describe('AppComponent', () => {
    let fixture, component;

    beforeEach(() =>  {
        TestBed.configureTestingModule({
           declarations: [
                 MyApp
           ],
           imports: [
                 IonicModule.forRoot(MyApp)
           ],
            providers: [
                { provide: Platform, useFactory: () => PlatformMock.instance() },
                { provide: StatusBar, useFactory: () => StatusBarMock.instance() },
                { provide: SplashScreen, useFactory: () => SplashScreenMock.instance() }
           ]
         })

        fixture = TestBed.createComponent(MyApp);
        component = fixture.componentInstance;
    })

    it('should create the app',() => {
        expect(component).toBeTruthy();
        expect(component instanceof MyApp).toEqual(true);
    });
})
```

We use:

* configureTestingModule to setup the testing environment for the component, including any imports and providers we need.

* createComponent to create an instance of the component we want to test.



Lets check our debug window

![](/assets/debug_window_1_test.png)

Also open the coverage report

```
# Mac OS
$ open coverage/index.html
```

```
# Linux
$ xdg-open coverage/index.html
```

![](/assets/test_coverage_app_100.png)

\#\#\# Acceptance test

// introduce protractor

We start with these tests

```javascript
import { by, browser, element } from 'protractor'
describe('App', () => {
  describe('default screen', () => {
    beforeEach(() => {
      browser.get('/');
    });

    it('App should have a title', () => {
      expect(browser.getTitle()).toContain('Ionic App')
    });

    it('the about tab is displayed by default', () => {
      expect(element(by.css('[aria-selected=true] .tab-button-text')) // First we find the selected tab  
        .getAttribute('innerHTML')) // Then we grab the html content
        .toContain('About'); // Then we check if it matches our expectations
    });

    it('should have a title saying About', () => {
      expect(element(by.css('.toolbar-title'))
        .getAttribute('innerHTML'))
        .toContain('About');
    });
  });
});
```

// tell them how to run the e2e tests and show screenshot

now our scaffolded app is tested we can move on by writing a test for a tab for the calculator page. So inside our default screen describe block we add this test

```javascript
it('Should have a Calculator Tab', () => {
  expect(element(by.css('[aria-controls="tabpanel-t0-0"]'))
    .getAttribute('innerHTML'))  
    .toContain('Calculator');
})
```

    This page has the code to make this test go green but it will also make two of our first acceptance test fail, the the about tab is displayed by default and should have a title saying About you can safely update them to expect `Calculator`.

```
https://class.craftacademy.co/courses/course-v1:CraftAcademy+CA-CAMP+2018-april/courseware/96bf29b196214229a1f5b420c670ac7f/068336cc02d6478ca19066842dd0166d/?activate_block_id=block-v1%3ACraftAcademy%2BCA-CAMP%2B2018-april%2Btype%40sequential%2Bblock%40068336cc02d6478ca19066842dd0166d
```

Lets setup the unit tests for the Calculator page. start by creating a file calculator.spec.ts in src/pages/calculator

```
$ touch src/pages/calculator/calculator.spec.ts
```

Before we start adding content to the file lets import ionic-mocks to isolate our unit test.

```
$ npm install --save-dev ionic-mocks
```

Now we add this to our spec file.

```javascript
# src/pages/calculator/calculator.spec.ts
import { CalculatorPage } from './calculator'
import { TestBed } from '@angular/core/testing'
import { IonicPageModule, Platform, NavController, NavParams, Config,
App, DomController, Keyboard } from 'ionic-angular';
import { PlatformMock, StatusBarMock, SplashScreenMock, NavControllerMock,
NavParamsMock, ConfigMock, Key } from 'ionic-mocks'
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';

describe('AppComponent', () => {
    let fixture, component;

    beforeEach(() => {
       TestBed.configureTestingModule({
           declarations: [
               CalculatorPage
           ],
           imports: [
               IonicPageModule.forChild(CalculatorPage)
           ],
           providers: [
                { provide: Platform, useFactory: () => PlatformMock.instance() },
                { provide: StatusBar, useFactory: () => StatusBarMock.instance() },
                { provide: SplashScreen, useFactory: () => SplashScreenMock.instance() },
                { provide: NavController, useFactory: () => NavControllerMock.instance() },
                { provide: NavParams, useFactory: () => NavParamsMock.instance() },
                { provide: Config, useFactory: () => ConfigMock.instance() },
                App, DomController, Keyboard
           ]
         })

        fixture = TestBed.createComponent(CalculatorPage);
        component = fixture.componentInstance;
     })

    it('should create the calculator page', () => {
        expect(component).toBeTruthy();
        expect(component instanceof CalculatorPage).toEqual(true);
    });
})
```

// review this sentence.

When we want to use a mock from ionic-mocks we need to import both the real service and the mock and under the configureTestingModule providers we provide the real service but use the factory of the mock.

```javascript
{ provide: NavController, useFactory: () =>; NavControllerMock.instance() }
```

// ? link to this blog for more info on useClass, useFactory. [https://medium.com/front-end-hacking/angular-2-an-introduction-of-bootstrap-and-providers-1c60ffbb7604](https://medium.com/front-end-hacking/angular-2-an-introduction-of-bootstrap-and-providers-1c60ffbb7604)

As it stands our app does not do anything so lets write acceptance test for a form on the calculator.html that takes inputs for weight\(kg\) and height\(cm\) and a button to calculate the BMI. Start by creating a new file calculator.e2e-spec.ts in the e2e folder.

```javascript
# e2e/calculator.e2e-spec.ts
describe('Calculator', () => {
    it('fill in form', () => {
        element(by.css('[aria-labelledby="lbl-0"]')).sendKeys(90); // fill in the weight
        element(by.css('[aria-labelledby="lbl-1"]')).sendKeys(186); // fill in the height
        element(by.buttonText('Calculate')).click(); // click the calculation button
        browser.sleep(1000); // gives the browser some time to load the answer to the page

        expect(element(by.css('.card-content'))
            .getText())
            .toContain('Person: Weight 90 kg, Height 186 cm')

        expect(element(by.css('.card-content'))
            .getText())
            .toContain('You are Overweight')

    });
});
```

we add this test to the calculator.spec.ts file

```javascript
it('CalculateBMI', () => {
    spyOn(component, 'setBMIMessage') // we use jasmine to spy on a function
    component.weight = 90;
    component.height = 186;
    component.calculateBMI();

    expect(component.bmiValue).toEqual(26.01);
    expect(component.setBMIMessage).toHaveBeenCalled // check if the function has been called
})
```

If we check the test debug window we will see that we are missing a GestureController. ![](/assets/missing_gesture_controller.png)

To fix that we need to import it from ionic/angular

```javascript
import { IonicPageModule, Platform, NavController, NavParams, Config,
App, DomController, Keyboard, GestureController } from 'ionic-angular';
```

Then we need to check the documentation for the ionic-mocks package if they have a mock for it.

```
https://www.npmjs.com/package/ionic-mocks
```

Since they dont have a mock for it we can just add it to the providers list

```javascript
providers: [
            { provide: Platform, useFactory: () => PlatformMock.instance() },
            { provide: StatusBar, useFactory: () => StatusBarMock.instance() },
            { provide: SplashScreen, useFactory: () => SplashScreenMock.instance() },
            { provide: NavController, useFactory: () => NavControllerMock.instance() },
            { provide: NavParams, useFactory: () => NavParamsMock.instance() },
            { provide: Config, useFactory: () => ConfigMock.instance() },
            { provide: Keyboard, useFactory: () => KeyboardMock.instance() },
            App, DomController, GestureController
        ]
```

Now go back to the debug window and refresh, you will see that we are missing a provider for form

![](/assets/missing_form.png)

Start by importing it from ionic/angular

```javascript
import { IonicPageModule, Platform, NavController, NavParams, Config,
App, DomController, Keyboard, GestureController, Form } from 'ionic-angular';
```

Check the documentation for the ionic-mocks package if they have a mock for it. They have a mock for form so we need to import that mock.

```javascript
import { PlatformMock, StatusBarMock, SplashScreenMock, NavControllerMock,
NavParamsMock, ConfigMock, KeyboardMock, FormMock } from 'ionic-mocks'
```

And now we need to provide Form but use the mock and we do that by adding this line to our providers list.

```javascript
{ provide: Form, useFactory: () => FormMock.instance() },
```

Our provider list should look like this.

```
providers: [
            { provide: Platform, useFactory: () => PlatformMock.instance() },
            { provide: StatusBar, useFactory: () => StatusBarMock.instance() },
            { provide: SplashScreen, useFactory: () => SplashScreenMock.instance() },
            { provide: NavController, useFactory: () => NavControllerMock.instance() },
            { provide: NavParams, useFactory: () => NavParamsMock.instance() },
            { provide: Config, useFactory: () => ConfigMock.instance() },
            { provide: Keyboard, useFactory: () => KeyboardMock.instance() },
            { provide: Form, useFactory: () => FormMock.instance() },
            App, DomController, GestureController
        ]
```

Now go refresh the debug window and see that we now have 3 greens.

![](/assets/3_specs_green.png)

Next we write a unit test for the setBMIMessage function

```js
# calculator.spec.ts
it('setBMIMessage if bmiValue is under 18.5', () => {
    component.bmiValue = 18; // set the bmi value
    component.setBMIMessage(); // run the function we want to test
    
    expect(component.bmiMessage).toEqual('Underweight')  // check if the outcome is correct
});

it('setBMIMessage if bmiValue is over 18.5 and under 25', () => {
    component.bmiValue = 22;
    component.setBMIMessage();
    
    expect(component.bmiMessage).toEqual('Normal')
});

it('setBMIMessage if bmiValue is over 25 and under 30', () => {
    component.bmiValue = 27;
    component.setBMIMessage();

    expect(component.bmiMessage).toEqual('Overweight')
});

it('setBMIMessage if bmiValue is over 30', () => {
    component.bmiValue = 31;
    component.setBMIMessage();
    
    expect(component.bmiMessage).toEqual('Obese')
});
```

Lets check our debug window for the status of our tests and it should be 7 green 0 failures.

![](/assets/7_specs_green.png)





