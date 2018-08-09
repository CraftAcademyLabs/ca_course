`*ngFor` is used for looping over a collection of data. In the example below, we have a collection of videos in a component and we use `ngFor` to loop over the collection in order to display data about each video.

```html
<ul>
  <li *ngFor="let video of videos">
    Name: {{video.name}}
    Liked: {{video.liked}}
  </li>
</ul>
```

## Try it out
Add the code above to your template. Now we only need a collection of videos in our component for this to work, so let's put some videos in our component.

```javascript
export class SuggestionsComponent {
 videos = [
    {name: 'video one', liked: true},
    {name: 'video two', liked: false}
 ]

  constructor() { }

  ...

  }
```