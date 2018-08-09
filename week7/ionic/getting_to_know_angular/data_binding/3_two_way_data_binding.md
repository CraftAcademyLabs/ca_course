In the previous two scenarios data can only flow in one direction at a time.
Now we are going to see how data can flow from component to template and from template back to
component.

This is done by using the syntax `[( )]`, also called "Banana in a box".

In order to demo this we need to import Angular form module into our
`app.module.ts` – do you recall how we did our imports?

```javascript
  import { FormsModule } from '@angular/forms';

  ...

  @NgModule({
    imports: [
      ...
      FormsModule
    ],
    ...
  })
```

Now let's add this to our root template:
```html
 <p>Video name: {{video.name}}</p> <!-- keep in mind that '{{ }}' gets data from the compontent -->
 <input [(ngModel)] = "video.name"/>
```

Now if you try to type something (anything) into the input field, what happens?

In the diagram below, on the left, we have the template while on the right we have the component. The arrows show the direction of data flow.

![data binding](https://raw.githubusercontent.com/magnus-thor/ca_course/cooper_challenge_AUT/images/data-binding.png)
*<center>Figure: data binding </center>*
