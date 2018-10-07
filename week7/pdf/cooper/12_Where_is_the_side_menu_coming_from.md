Can you try find out where this menu is located? Look within the `src` directory.

Since the menu is available to the entire application, no matter the page we are in, then our first guess would be that it is at the template the is associated with the root component this assumption is actually right. Our root component is called `app.component.ts` with the matching template being `app.html` - you can tell this by looking at the metadata selector

The menu is within the `ion-content` tab
```javascript
<ion-content>
  <ion-list>
    <button menuClose ion-item *ngFor="let p of pages" (click)="openPage(p)">
      {{p.title}}
    </button>
  </ion-list>
</ion-content>
```
From the above code, we can tell there is a variable called `pages` in our component that we are looping through using the directive `*ngFor` then attaching `click` event to a function in the component.

#### **`pages` variable**
Head over to the component and locate the pages variable

```javascript
pages: Array<{title: string, component: any}>;
```
This declares a variable called `pages` that is an `Array`. This array contains objects with `title` and `component`. Inside ur constructor, the variable is then assigned an array containing two objects.

```javascript
this.pages = [
  { title: 'Home', component: HomePage },
  { title: 'List', component: ListPage }
];
```
 