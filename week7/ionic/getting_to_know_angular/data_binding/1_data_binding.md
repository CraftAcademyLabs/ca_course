* Remember when we said that data is able to flow from the template to the component and vice versa? Let's talk about how to do this by using data binding.

## Data flowing from the template to the component

In the YouTube example above, when a user clicks on a particular video suggestion the template passes information to the component related to that suggestion telling it that the suggestion was clicked. It also passes along any other information about that particular suggestion that may be embedded in the template. The user's actions on the page are interpreted as events by functions inside the component that handle events (event handler functions). These interpreted user events are used to trigger or perform certain functionality or commands within the app. That is how data flows from the template to the component.

In the code below the `handleClick()` function handles the user's `click` event, so the `handleClick()` function is found in the component related to that template. What other events can you think of when interacting with a web page or app?

Try it out
Add the following to our template `application.compontent.html`

```html
<button (click)="handleClick()">Console log me</button>
```

When data is flowing from template to the component we use `()`. Inside this () we have the name of the event the user just triggered eg `(click)="doSomething()"` where:

 - `(click)`: event wrapped in ()
 - `doSomething()`: function in the matching component.

Add the following code to our matching component inside our class. You should be able to match components to templates at this point.

```javascript
@Component({
...
})

export class AppComponent {
  title = 'app';

  handleClick(){
    console.log('Someone clicked on button on the template :-)')
  }
}
```
Let's see the code in action in the browser: open the app and the developer tools to see the results. What do you see? Click on the button, is anything printed out to the console (in developer tools)?