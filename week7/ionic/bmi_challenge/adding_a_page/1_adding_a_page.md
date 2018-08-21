Now that we have cleaned up the code base we are ready to add our own stuff. We'll be making use of two tabs. One About tab that will route us to an "About page", and one "BMI Calculator" tab that will be displaying the view where we will be doing the calculations.

Just like with rails and angular, Ionic provide us with a generator to scaffold the most common components to an app. We will be using that to generate a new page.

```shell
$ ionic g page calculator
```

If you take a look at your `src/pages/` folder, you'll see that a new folder name calculator has been created.

Every time you created a new page, you need to tell the application about it by importing it in `app.module.ts`. Open it and add the following line at the top of it

```javascript
import { CalculatorPage } from '../pages/calculator/calculator'
```

Then add `CalculatorPage` to the `declarations` and `entryComponents` properties. Next, we also import `CalculatorPage` in `src/pages/tabs/tabs.ts` and create a new tab for it.

```javascript

// Add import statement at top of the file
import { CalculatorPage } from '../calculator/calculator'


export class TabsPage {
	calculatorTab = CalculatorPage; // Create a calculator tab
  aboutTab = AboutPage            // Rename tab2Root to aboutTab
  ...
}

```

Lastly, let's edit the `tabs.html` template file to render the new tab we just created.

```javascript
<!-- Your tabs.html file content should be updated to the following -->
<ion-tabs>
  <ion-tab [root]="calculatorTab" tabTitle="Calculator" tabIcon="calculator"></ion-tab>
  <ion-tab [root]="aboutTab" tabTitle="About" tabIcon="information-circle"></ion-tab>
</ion-tabs>
```

Now, check your browser to make sure you can see the new tab we just created. If everything looks good, let's commit our changes and in the next section we'll add some functionality to our calculator.

```shell
$ git add .
$ git commit -m 'add calculator tab'
```

![Calculator Page](https://raw.githubusercontent.com/magnus-thor/ca_course/cooper_challenge_AUT/images/bmi-add-calculator-tab.png)