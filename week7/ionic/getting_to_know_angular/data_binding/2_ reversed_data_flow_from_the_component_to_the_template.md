Data is also able to flow in the opposite direction, from inside the app all the way back to the template. In this case, we have some information in the component that we want to display in the template.

Let's go ahead and create a video in our component!

```javascript
@Component({
...
})

export class AppComponent {
  title = 'app';
  video = {
    title: 'Despacito',
    views: 2,
    liked: true
  }

  handleClick(){
    console.log('Someone clicked the 'Like' button in the template :-)')
}
```

Now that we have created a video in our component, how do we go about to display the video inside the template? In order to achieve this, we create an expression by using double squiggly brackets `{{ }}`!

In our view lets add the code below to the button:

```html
<p>Video title: {{video.title}}</p>
<p>Views: {{video.views}}</p>
<p>Liked: {{video.liked}}</p>
```

This way of writing data within double squiggly brackets is known as string interpolation - does this ring a bell?

