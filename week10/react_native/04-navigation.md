## Navigation

As it stands we only have one screen in our application the `HomeScreen`, lets create a `ArticleScreen`, setup navigation to reach that screen and then we will fetch a article from our backend.

### ArticleScreen

Create a new screen.

```sh
$ mkdir src/screens/Articles
$ touch src/screens/Articles/ArticleScreen.js
```

And in it we will just render a simple hello world message initially.

```js
import React, { Component } from "react";
import { Text, View } from "react-native";

 class ArticleScreen extends Component {
  render() {
    return (
      <View>
        <Text>Hello World from Article Screen !!!!</Text>
      </View>
    );
  }
}

export default ArticleScreen
```

### react-navigation

We will be using a package called `react-navigation` to manage all of our screens. Lets start with adding that package.

```sh
$ yarn add react-navigation --save
```

And create a file `AppNavigator.js` to handle our screens.

```sh
$ touch src/AppNavigator.js
```

And add this to that file.

```js
import { createAppContainer, createStackNavigator } from "react-navigation";
import HomeScreen from "./screens/Home/HomeScreen";
import ArticleScreen from "./screens/Articles/ArticleScreen";

const navStack = createStackNavigator({
  Home: { screen: HomeScreen },
  Article: { screen: ArticleScreen }
});

const AppNavigator = createAppContainer(navStack);

export default AppNavigator;
```

- `createStackNavigator` provides a way for your app to transition between screens where each new screen is placed on top of a stack.
- `createAppContainer` creates the main component for react to render.

Lets update `App.js` and import the `AppNavigator` and have it render the component.

```js
import React from "react";
import AppNavigator from "./AppNavigator";

 class App extends React.Component {
  render() {
    return <AppNavigator />;
  }
}

export default App
```

At this point the application run fine again but we no way of navigating to the `ArticleScreen` lets add a button to each article that navigates to the `ArticleScreen`.

```js
renderArticles({ item }) {
  const article = item;
  return (
    <View>
      <Image
        style={{ width: 100, height: 100 }}
        source={{ uri: article.image }}
      />
      <Text>{article.category.name}</Text>
      <Text>{article.title}</Text>
      <Text>{article.description}</Text>
      <Button
        title="View Article"
        onPress={() => this.showArticle()}
      />
    </View>
  );
}
```

And we need to create the `showArticle` function that will navigate to the `ArticleScreen`.

```js
showArticle() {
  this.props.navigation.navigate("Article");
}
```

![Articles Screen Hello World](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/339e12e46d487566421adbf13b66995fb08761ff/week10/article_screen_hello_world.png)

Lets send some data into the `ArticleScreen` we can do tha by passing in a object after the screen we want.

```js
showArticle() {
  this.props.navigation.navigate("Article", {
    message: "This was sent from HomeScreen",
    anotherMessage: "Hello from HomeScreen"
  });
}
```

And then in the `ArticleScreen` we can receive the data with `this.props.navigation.getParam('message', 'this field is just for some default value incase message is empty')` or we can use `this.props.navigation.state.params.anotherMessage`, lets try it out.

```js
import React from "react";
import { Text, View } from "react-native";

class ArticleScreen extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      message: this.props.navigation.getParam(
        "message",
        "this field is just for some default value incase message is empty"
      ),
      anotherMessage: this.props.navigation.state.params.anotherMessage
    };
  }
  render() {
    return (
      <View>
        <Text>Hello World from Article Screen !!!!</Text>
        <Text>{this.state.message}</Text>
        <Text>{this.state.anotherMessage}</Text>
      </View>
    );
  }
}

export default ArticleScreen
```

![Article screen receives params](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/339e12e46d487566421adbf13b66995fb08761ff/week10/article_nav_params.png)
