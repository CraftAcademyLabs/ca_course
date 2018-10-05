We will now remove our home and contact pages, then add a new page called calculator. Before we do that, let's create a new branch called calculator

```shell
$ git checkout -b calculator
```

Now, navigate to `src/pages` and delete `home` and `contact`. Then open `src/app/app.module.ts` (in your text editor) and remote both `HomePage` and `ContactPage` from the `declarations` and `entryComponents` properties. You also want to remove respective `import` statements from this file.

```javascript
// Remove the following lines
import { ContactPage } from '../pages/contact/contact';
import { HomePage } from '../pages/home/home';
```

Next, open `src/pages/tabs.ts` and remove `import` statements for `HomePage` and `ContactPage` and remove the following lines too

```javascript
// Remove the following lines
tab1Root = HomePage;
tab3Root = ContactPage;
```

And finally in `src/pages/tabs.html` , we will remove lines that set both `tab1Root` and `tab3Root`

```javascript
<!-- Remove the following lines -->
<ion-tab [root]="tab1Root" tabTitle="Home" tabIcon="home"></ion-tab>
<ion-tab [root]="tab3Root" tabTitle="Contact" tabIcon="contacts"></ion-tab>
```

At this point, if you take a look at your browser, you should see something similar to

![BMI cleaned app](https://github.com/magnus-thor/ca_course/blob/cooper_challenge_AUT/images/bmi-cleaned-app.png)

Next up, let's see how to add new pages to our app. But before we carry on, let's commit all the changes we just made

```shell
$ git add .
$ git commit -m 'remove some of the scaffolded pages'
```