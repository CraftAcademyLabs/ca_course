We will make use of the [Ionic Tabs](https://github.com/ionic-team/ionic2-starter-tabs) template. Navigate to the folder where you want to create your project and run the following command to start your application.

```shell
$ ionic start bmi_calculator tabs
```

Once that is complete, run the following commands to add support for iOS and Android.

```shell
$ cd bmi_calculator
$ ionic cordova platform add ios     # if you're on macOS
$ ionic cordova platform add android # if you need to publish for android.
```

At this point, you usually want to make sure you initialize a git repository inside the project folder. This is such so important that the good folks from the ionic team built this in as part of the process of scaffolding the app. If you were paying attention, you might have noticed that after installing all necessary project dependencies, a git repository was initialized and the Initial commit was made. All you have to do now is, create a new repository on GitHub and add it as a remote. *Git flow is outside the scope of this walkthrough but, do make use of version control when working on this project*.