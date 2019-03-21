## Navigation

As it stands we only have one screen in our application the `HomeScreen`, lets create a `ArticleScreen`, setup navigation to reach that screen and then we will fetch a article from our backend.

### ArticleScreen

Create a new screen.

```sh
$ mkdir Screens/Article
$ touch Screens/Article/ArticleScreen.js
```

And in it we will just render a simple hello world message initially.

```js
import React from "react";
import { Text, View } from "react-native";

export default class ArticleScreen extends React.Component {
  render() {
    return (
      <View>
        <Text>Hello World from Article Screen !!!!</Text>
      </View>
    );
  }
}
```

### react-navigation

We will be using a package called `react-navigation` to manage all of our screens. Lets start with adding that package.

```sh
$ yarn add react-navigation --save
```

And create a file `AppNavigator.js` to handle our screens.

```sh
$ touch AppNavigator.js
```

And add this to that file.

```js
import { createAppContainer, createStackNavigator } from "react-navigation";
import HomeScreen from "./Screens/Home/HomeScreen";
import ArticleScreen from "./Screens/Article/ArticleScreen";

const NavStack = createStackNavigator({
  Home: { screen: HomeScreen },
  Article: { screen: ArticleScreen }
});

const AppNavigator = createAppContainer(NavStack);

export default AppNavigator;
```

- createStackNavigator it provides a way for your app to transition between screens where each new screen is placed on top of a stack.
- createAppContainer creates the main component for react to render.

Lets update `App.js` and import the `AppNavigator` and have it render the component.

```js
import React from "react";
import AppNavigator from "./AppNavigator";

export default class App extends React.Component {
  render() {
    return <AppNavigator />;
  }
}
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
  this.props.navigation.navigate("ArticleScreen");
}
```
