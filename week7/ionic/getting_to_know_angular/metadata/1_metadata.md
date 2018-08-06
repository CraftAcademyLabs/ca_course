In TypeScript, in order to attach additional information to a class – for example, information about what the class does, where the styling is and what role that class plays in the app – you can use a decorator.

Looking at the code below you can see that we have a decorator in `@Component`. The decorator holds and prefixes a JavaScript object, which in turn carries additional information about the class `SuggestionsComponent` (the JavaScript object is within `{` and `}`, remember?).
```javascript
@Component({
  selector: 'app-suggestions',
  templateUrl: './suggestions.component.html',
  styleUrls: ['./suggestions.component.css']
})

export class SuggestionsComponent {
...
}
```
Basically, the decorator is used to give us a brief summary of the class, much like
the information on hospital wrist bands worn by hospital patients does.
![hospital wrist band](/images/hospital-wrist-band.png)
*<center>Figure: Hospital wrist band</center>*


```javascript
@Component({
  selector: 'app-suggestions',
  templateUrl: './suggestions.component.html',
  styleUrls: ['./suggestions.component.css']
})

export class SuggestionsComponent {
...
}
```
In the code above you see that the metadata has three properties:

- `selector`: This is a CSS selector that tells Angular where to place this particular component in the page. We will later use this component in our view by putting `<app-suggestions></app-suggestions>` in the code of the view.
- `templateUrl`: This tells Angular where in our code structure the file containing the view for this component is located. In our code above it is located at `./suggestions.component.html`
- `styleUrls`: By now you can guess what this does, which is to tell Angular where our stylesheet for the component is located.