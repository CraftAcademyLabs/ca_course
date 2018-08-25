We will be using dependency injection to equip our components with the services that they need. Let's say that we have a service that returns a collection of videos from YouTube. In order to be able to use this service we will have to `inject` it into our component:

```javascript
constructor(private videoService: VideoService){}

playVideo(){
 this.videoService.play(video)
}
```

The way to `inject` a dependency is by writing `constructor(private videoService: VideoService){}`. Let's have a look at the different parts of this:

 - `VideoService`: is the service that we are instantiating and used with the videoService local variable. We must import this service into our component before we are able to use it ( remember javascript modules import?)
 - `private`: shows that whatever we are declaring is private to this class
 - `videoService`: the local variable name that uses within the class. When used this local variable usually preceded by the `this` keyword as we see in the example above:`this.videoService.play(video)`