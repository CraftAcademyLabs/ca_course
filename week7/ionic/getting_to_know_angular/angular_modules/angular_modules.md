Every angular application must have at least one module, the root module, mostly called `AppModule`. In most of our applications, this is possibly the only module you will ever use. This file can be found at `src/app/app.module.ts` and it looks like this:
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```
- @NgModule: The tag `@NgModule` is called a decorator which we will discuss in detail later. This decorator takes an object with different properties, such as `declarations`, `imports`, `providers` and `bootstrap`. We will mostly be working with the `providers` and `declarations`.

- providers: Services are values, functions, or features that your application needs. We will come back to a more detailed description of services later. All our services are usually registered with the application by adding them to this section. By doing this you make the services available for use in the entire application.

- declarations: our components are usually declared in this section. In our case, the generated `AppComponent` is declared here.

### Angular modules are not JavaScript modules
OK, as we learned earlier JavaScript has modules too, but they are different from Angular modules. An Angular module (NgModule) is decorated by `@NgModule` and it is completely unrelated to the JavaScript modules.