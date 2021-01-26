## Fetching a single article

This method of getting a single article assumes that the user needs to be authenticated before accessing the endpoint for single article.

### Set up a service

First wee need to setup a service to fetch the article and we will send the token with the `Get` request to the backend and when we have the response we will save the new token.

In `ArticlesApiService`.

```js
export const GetArticle = async id => {
  try {
    let headers = await AsyncStorage.getItem("credentials");
    headers = JSON.parse(headers);
    let response = await axios.get(url + "/api/articles/" + id, {
      method: "GET",
      mode: "cors",
      headers: headers
    });
    const article = response.data.article;
    return article;
  } catch (error) {
    console.error(error);
  }
};
```

Next in our `ArticleScreen` lets call that `GetArticle` service and display the article we got.

```js
import React from "react";
import { StyleSheet, Text, View, FlatList, Button } from "react-native";
import { Image } from "react-native-elements";
import { GetArticle } from "../../Services/ArticlesApiService";

export default class HomeScreen extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      articleId: this.props.navigation.state.params.articleId,
      article: {
        id: 0,
        title: "",
        description: "",
        content: "",
        image: "",
        category: {
          name: ""
        }
      }
    };
  }

  componentDidMount() {
    GetArticle(this.state.articleId).then(article => {
      this.setState({
        article: {
          id: article.id,
          title: article.title,
          description: article.description,
          content: article.content,
          image: article.image,
          category: {
            name: article.category.name
          }
        }
      });
    });
  }

  render() {
    return (
      <View>
        <Image
          style={{ width: 100, height: 100 }}
          source={{ uri: this.state.article.image }}
        />
        <Text>{this.state.article.category.name}</Text>
        <Text>{this.state.article.title}</Text>
        <Text>{this.state.article.description}</Text>
        <Text>{this.state.article.content}</Text>
      </View>
    );
  }
}
```

Notice that when we set the state initially we expect to receive an article id

```js
articleId: this.props.navigation.state.params.articleID;
```

Lets update our `HomeScreen` to pass in the id number when we select a article.

```js
showArticle(id) {
  this.props.navigation.navigate("Article", {
    articleId: id
  });
}

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
      <Button title="View Article" onPress={() => this.showArticle(article.id)} />
    </View>
  );
}
```
