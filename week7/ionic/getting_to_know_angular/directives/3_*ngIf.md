`*ngIf` renders a section of the template but only if a certain condition is valid or true.

```html
<p *ngIf="isShown">This should be hidden or shown</p>
```

We expect the paragraph above to be shown only if the value of `isShown` is true.
If the value of `isShown` is false the paragraph should not be displayed on the
page by the template.

We define the value of `isShown` inside the component:

```javascript
export class SuggestionsComponent {
 videos = [
  ...
 ]

 isShown: boolean = true

  constructor() { }
```

## Try it out
 - Change the value of `isShown` to false then look at your template. Is the paragraph still shown?
Answer this before moving on and **make sure to try it out!**