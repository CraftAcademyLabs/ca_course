### Setup testing environment

Before we continue with our application we want to setup the testing environment. Follow this guide [Unit and Acceptance testing environment setup](https://class.craftacademy.co/courses/course-v1:CraftAcademy+CA-CC-01+2018/courseware/96bf29b196214229a1f5b420c670ac7f/dddd401a80e24483911288b387b1bdbf/?activate_block_id=block-v1%3ACraftAcademy%2BCA-CC-01%2B2018%2Btype%40sequential%2Bblock%40dddd401a80e24483911288b387b1bdbf) and when you are done return to this page.

## Create our test files


### Unit test

Lets start with running our unit and test coverage script.
```shell
$ npm run test-coverage
```
This will open a new browser window

![initial karma](/images/karma_init_window.png?raw=true)

We need to click the DEBUG button to get a overview of our tests.

![initial jasmine](/images/karma_debug_window.png)

Lets create a unit test file for our main component `app.component.ts` and we do that in the terminal with the command `touch src/app/app.component.spec.ts`

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

- Testbed configures and initializes environment for unit testing and provides methods for creating components and services in unit tests.

- configureTestingModule allows overriding default providers, directives, pipes, modules of the test injector.

- createComponent creates a ComponentFixture which is a test environment around the component and its template.

- componentInstance creates the component from the componentFixture.

Lets check our debug window

![1st unit test](/images/debug_window_1_test.png)


Also open the coverage report
```shell
# Mac OS
$ open coverage/index.html
```

```shell
# Linux
$ xdg-open coverage/index.html
```

![coverage window 1st test](/images/test_coverage_app_94.png)

Lets also create the spec file for `home` component
```shell
$ touch src/pages/home/home.spec.ts
```

and we copy this into that file

```javascript
# src/pages/home/home.spec.ts



import { TestBed } from "@angular/core/testing";
import { HomePage } from "./home";
import {
  IonicPageModule,
  Platform,
  NavController,
  MenuController,
  Config,
  App,
  DomController,
  Keyboard,
	GestureController,
	Form,
	DeepLinker,
	Haptic
} from "ionic-angular";
import {
  PlatformMock,
  StatusBarMock,
  SplashScreenMock,
  NavControllerMock,
  MenuControllerMock,
	ConfigMock,
	KeyboardMock,
	FormMock,
	HapticMock
} from "ionic-mocks";
import { DeepLinkerMock} from '../../../test-config/mocks'
import { StatusBar } from "@ionic-native/status-bar";
import { SplashScreen } from "@ionic-native/splash-screen";

describe("HomeComponent", () => {
  let fixture, component;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [HomePage],
      imports: [IonicPageModule.forChild(HomePage)],
      providers: [
        { provide: Platform, useFactory: () => PlatformMock.instance() },
        { provide: StatusBar, useFactory: () => StatusBarMock.instance() },
				{ provide: SplashScreen, useFactory: () => SplashScreenMock.instance() },
				{ provide: NavController, useFactory: () => NavControllerMock.instance() },
				{ provide: MenuController, useFactory: () => MenuControllerMock.instance() },
				{ provide: Config, useFactory: () => ConfigMock.instance() },
				{ provide: Keyboard, useFactory: () => KeyboardMock.instance() },
				{ provide: Form, useFactory: () => FormMock.instance() },
				{ provide: Haptic, useFactory: () => HapticMock.instance() },
				{provide: DeepLinker, useClass: DeepLinkerMock},
				App, DomController, GestureController

      ]
    });

    fixture = TestBed.createComponent(HomePage);
    component = fixture.componentInstance;
  });

  it("should create the home page", () => {
    expect(component).toBeTruthy();
    expect(component instanceof HomePage).toEqual(true);
  });
});
```


When we want to use a mock from ionic-mocks we need to import both the real service and the mock and under the configureTestingModule providers we provide the real service but use the factory of the mock.

```javascript
{ provide: NavController, useFactory: () =>; NavControllerMock.instance() }
```

We can check the documentation for the ionic-mocks package if they have a mock for it.
```shell
https://www.npmjs.com/package/ionic-mocks
```
Lets take a look at our test coverage.

![Initial home test coverage](/images/initial_home_test_coverage.png)
