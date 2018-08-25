```javascript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-suggestions',
  templateUrl: './suggestions.component.html',
  styleUrls: ['./suggestions.component.css']
})
export class SuggestionsComponent {

  constructor() { }

  playVideo(video){
    ...
  }

}
```
We start by importing various libraries we need from the Angular core. In this case we have `Component` and `OnInit`.

The `@Component` decorator then tells us that the `SuggestionsComponent` class declared after the decorator (`@Component`) is a component. The decorator itself has metadata about the `selector`, `templateUrl`, `styleUrls`, which we will discuss the details later.

Following the decorator we find the `SuggestionsComponent` class, which contains the following
functions:

- `constructor`(): used for initialization when instantiating an object out of the class, much like ruby class initializers
- `playVideo`(): a function that can be called from the template