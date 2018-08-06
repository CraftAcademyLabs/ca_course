As usual we start by writing our tests. Update the `app.component.spec.ts` like this

```javascript
import { MyApp } from "./app.component";
import { TestBed, async, inject } from "@angular/core/testing";
import { IonicModule, Platform } from "ionic-angular";
import { PlatformMock, StatusBarMock, SplashScreenMock } from "ionic-mocks";
import { StatusBar } from "@ionic-native/status-bar";
import { SplashScreen } from "@ionic-native/splash-screen";
import { Http, BaseRequestOptions, RequestMethod } from '@angular/http'
import { MockBackend } from '@angular/http/testing';
import { Angular2TokenService } from 'angular2-token';

describe("AppComponent", () => {
  let fixture, component;

  let signInData = {
    email: 'test@test.com',
    password: 'password',
    userType: String
  };

  beforeEach(() => {

    TestBed.configureTestingModule({
      declarations: [MyApp],
      imports: [
        IonicModule.forRoot(MyApp)
      ],
      providers: [
        BaseRequestOptions,
        MockBackend,
        {
          provide: Http,
          useFactory: (backend, defaultOptions) => {
            return new Http(backend, defaultOptions)
          },
          deps: [MockBackend, BaseRequestOptions]
        },
        { provide: Platform, useFactory: () => PlatformMock.instance() },
        { provide: StatusBar, useFactory: () => StatusBarMock.instance() },
        { provide: SplashScreen, useFactory: () => SplashScreenMock.instance() },
        Angular2TokenService
      ]
    });

    fixture = TestBed.createComponent(MyApp);
    component = fixture.componentInstance;
  });


  it("should create the app", () => {
    expect(component).toBeTruthy();
    expect(component instanceof MyApp).toEqual(true);
  });

  it('login method', inject([Angular2TokenService, MockBackend], (tokenService, mockBackend) => {

    mockBackend.connections.subscribe(
      c => {
        expect(c.request.getBody()).toEqual(JSON.stringify(signInData));
        expect(c.request.method).toEqual(RequestMethod.Post);
        expect(c.request.url).toEqual('https://your-cooper-api.herokuapp.com/api/v1/auth/sign_in');
      }
    );

    component.login(signInData);
  }));

  it('signOut method', inject([Angular2TokenService, MockBackend], (tokenService, mockBackend) => {

    mockBackend.connections.subscribe(
      c => {
        expect(c.request.method).toEqual(RequestMethod.Delete);
        expect(c.request.url).toEqual('https://your-cooper-api.herokuapp.com/api/v1/auth/sign_out');
      }
    );

    component.logout();
  }));
});

```