### Working against an api on localhost

**Use the ip address of the computer otherwise you can/will get network errors.**

### JSX Components

```js
<div> becomes <View>
<p>,<h1> becomes <Text>
```

Both need to be imported from react-native

```js
import { Text, View } from "react-native";
```

You need to import Buttons from react-native

```js
import { StyleSheet, Text, View, FlatList, Button } from "react-native";
```

and then you can use them

```js
<Button
  title="Fetch Article"
  style={styles.button}
  onPress={() => this.showArticle(article.id)}
/>
```

### Images

Import it from react-native-elements

```js
import { Image } from "react-native-elements";
```

Use it

```js
<Image style={{ width: 100 }} source={{ uri: this.state.article.image }} />
```

### Styling

To style you first need to import style sheet from react-native

```

import { StyleSheet, Text, View } from 'react-native';

```

Then we can create the styles

```js
const styles = StyleSheet.create({
  bigblue: {
    color: "blue",
    fontWeight: "bold",
    fontSize: 30
  },
  red: {
    color: "red"
  }
});
```

Then we can use the styles

```js
render() {
    return (
      <View>
        <Text style={styles.red}>just red</Text>
        <Text style={styles.bigblue}>just bigblue</Text>
        <Text style={[styles.bigblue, styles.red]}>bigblue, then red</Text>
        <Text style={[styles.red, styles.bigblue]}>red, then bigblue</Text>
      </View>
    );
  }
```

### Flatlist

We use Flatlist to iterate through an array and display the data

```js
<View style={styles.container}>
  <FlatList
    data={this.state.articles}
    renderItem={item => this.renderArticles(item)}
    keyExtractor={item => item.id.toString()}
  />
</View>
```

### Navigation

Start by creating an file containing `createStackNavigator` and set a screen to navigate to.

```js
// AppNavigator.js
import { createAppContainer, createStackNavigator } from "react-navigation";
import HomeScreen from "./Components/HomeScreen";
import ArticleScreen from "./Components/ArticleScreen";
import ContextScreen from "./Components/ContextScreen";

const NavStack = createStackNavigator({
  Home: { screen: HomeScreen },
  Article: { screen: ArticleScreen },
  Context: { screen: ContextScreen }
});

const AppNavigator = createAppContainer(NavStack);

export default AppNavigator;
```

App.js will be the container for all of our screens to switch between.

```js
// App.js

import React from "react";
import AppNavigator from "./AppNavigator";

export default class App extends React.Component {
  render() {
    return <AppNavigator />;
  }
}
```

Then when we want to switch to a different screen.

```js
this.props.navigation.navigate("Context");
```

If we want to pass data with the navigation.

```js
this.props.navigation.navigate("Article", {
  articleID: id
});
```

### AsyncStorage

AsyncStorage replaces SessionStorage and it is used in similar way.

Import it

```js
import { AsyncStorage } from "react-native";
```

Storing data

```js
AsyncStorage.setItem("current_user", JSON.stringify({ id: data.data.id }));
```

Retrieving data, set the function as async so we can use await.

```js
let headers = await AsyncStorage.getItem("credentials");
```
