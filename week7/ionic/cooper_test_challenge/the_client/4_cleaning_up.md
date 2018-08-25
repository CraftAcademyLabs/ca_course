## Remove the list page

We will now clean up our application a bit by removing some sections that we do not need. Let's get started by deleting the `src/pages/list` folder. Go ahead and remove it from the menu list (do this from the `app.component.ts` file)

Remove its imports from the `app.component.ts`. On the `app.module.ts` file remove it from the imports and from the decorator too (`entryComponents` and `declarations`) then make sure you are not getting any errors in your terminal

## Remove homepage content

Head over to the hompage template and remove everything withing the `ion-content` tag, afterwhich you will see an empty page on the browser

Hint: Homepage template is located at `src/pages/home/home.html`

The template should now look like this

```javascript
<ion-header>
  <ion-navbar>
    <button ion-button menuToggle>
      <ion-icon name="menu"></ion-icon>
    </button>
    <ion-title>Home</ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
</ion-content>
```
If you head over to your browser you should see something like this.

![](https://raw.githubusercontent.com/magnus-thor/ca_course/cooper_challenge_AUT/images/cooper-clean-home.png)
￼
This will do for now. We will add the Login functionality further down the road. Right now, we want to focus on the main functionality of this application - to calculate the test results and give the user some feedback.