The template can be perceived to be the view of the component. By now you should be
able to tell that a component has its own files for the template, the stylesheet and TypeScript. Templates are much like plain HTML, but with a few extra super magical abilities and extra tags.

Look closely at the syntax in the code below and you will see a few additions like `*ngFor`, `(click)`, `[video]` and `*ngIf`. Don't be nervous about all these new and weird-looking things, we will cover them later.

```html
<h2>Suggested Videos</h2>

<p><i>Click on a video to play</i></p>
<ul>
  <li *ngFor="let video of videos" (click)="selectVideo(video)">
    {{video.name}}
  </li>
</ul>

<video-detail *ngIf="selectedVideo" [video]="selectedVideo"></video-detail>
```