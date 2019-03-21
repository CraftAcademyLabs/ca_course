## Authentication

### Authentication service

Start by creating a service that will handle the authentication for us.

```sh
$ touch Service/AuthService.js
```

In the `AuthService.js` we will setup a `try` - `catch` block. Inside the try part we will make a axios post request to the backend and we store the credentials using `AsyncStorage`, next we will will get either the name or the email of the user and return it and a authenticated true state back. in the `catch` part we will just console log the error and return a authenticated false state back.

```js
import axios from "axios";
import { AsyncStorage } from "react-native";

// const apiUrl = "http://192.168.1.178:3000";
const apiUrl = `https://your-heroku-address`;

const authenticate = async (email, password) => {
  const path = apiUrl + "/api/auth/sign_in";
  try {
    let response = await axios.post(path, {
      email: email,
      password: password
    });
    await storeAuthCredentials(response);
    const user = response.data.data.name
      ? response.data.data.name
      : response.data.data.email;
    return { authenticated: true, user: user };
  } catch (error) {
    console.log(error);
    return { authenticated: false };
  }
};

const storeAuthCredentials = ({ data, headers }) => {
  return new Promise(resolve => {
    const uid = headers["uid"],
      client = headers["client"],
      accessToken = headers["access-token"],
      expiry = headers["expiry"];

    AsyncStorage.setItem(
      "credentials",
      JSON.stringify({
        uid: uid,
        client: client,
        access_token: accessToken,
        expiry: expiry,
        token_type: "Bearer",
        "Content-type": "application/json",
        Accept: "application/json"
      })
    );
    resolve(true);
  });
};

export { authenticate };
```

### Setup the login functionality on HomeScreen

Start by creating a button to display the login form.
When we want to create button we need to import the `Button` component from `react-native`.

```js
import { StyleSheet, Text, View, FlatList, Button } from "react-native";
```

And lets crete a `renderLogin` function that will return either the button to login or the login form.

```js
renderLogin() {
  return (
    <>
      <Button
        title="Login"
        onPress={() => this.setState({ renderLoginForm: true })}
      />
    </>
  );
}
```

As you can see when a user clicks the button its going set the state for `renderLoginForm` to true, lets add that to the list of states in the constructor and set it to false.

```js
this.state = {
  articles: [],
  renderLoginForm: false
};
```

Lets display that button show we can get the login form.

```js
render() {
  let renderLogin = this.renderLogin();

  return (
    <View style={styles.container}>
      <FlatList
        data={this.state.articles}
        renderItem={this.renderArticles.bind(this)}
        keyExtractor={item => item.id.toString()}
      />
      {renderLogin}
    </View>
  );
}
```

Now that we have the button and it will switch the state on renderLoginForm to true lets set a condition in `renderLogin` function to return the login form instead of the button.

```js
renderLogin() {
    if (this.state.renderLoginForm === true) {
      return (
        <View>
          <LoginForm
            loginHandler={this.onLogin.bind(this)}
            handleEmail={this.emailStateHandler}
            handlePassword={this.passwordStateHandler}
          />
        </View>
      );
    } else {
      return (
        <>
          <Button
            title="Login"
            onPress={() => this.setState({ renderLoginForm: true })}
          />
        </>
      );
    }
  }
```

Now it will return a `LoginForm` component but we dont have that component yet, so lets remedy that.

```sh
$ touch Screens/Home/LoginForm.js
```

```js
import React from "react";
import {
  Text,
  View,
  TextInput,
  StyleSheet,
  TouchableOpacity
} from "react-native";

const LoginForm = props => {
  return (
    <View style={styles.container}>
      <TextInput
        style={styles.input}
        underlineColorAndroid="transparent"
        placeholder="Email"
        placeholderTextColor="#9a73ef"
        autoCapitalize="none"
        onChangeText={props.handleEmail}
      />

      <TextInput
        style={styles.input}
        underlineColorAndroid="transparent"
        placeholder="Password"
        placeholderTextColor="#9a73ef"
        autoCapitalize="none"
        onChangeText={props.handlePassword}
      />

      <TouchableOpacity
        style={styles.submitButton}
        onPress={e => props.loginHandler(e)}
      >
        <Text style={styles.submitButtonText}> Submit </Text>
      </TouchableOpacity>
    </View>
  );
};

export default LoginForm;

const styles = StyleSheet.create({
  container: {
    paddingTop: 23
  },
  input: {
    margin: 15,
    height: 40,
    borderColor: "#7a42f4",
    borderWidth: 1
  },
  submitButton: {
    backgroundColor: "#7a42f4",
    padding: 10,
    margin: 15,
    height: 40
  },
  submitButtonText: {
    color: "white"
  }
});
```

The two main things in there that are new to us are:

1. TouchableOpacity which is a wrapper for making views respond properly to touches.
2. TextInput which is a basic component that allows the user to enter text.

Now lets get back to the `HomeScreen` component and import the `LoginForm` component and the `authenticate` function from `Authservice`.

```js
import LoginForm from "./LoginForm";
import { authenticate } from "../../Services/AuthService";
```

The `LoginForm` component needs 3 things to work.

```js
<LoginForm
  loginHandler={this.onLogin.bind(this)}
  handleEmail={this.emailStateHandler}
  handlePassword={this.passwordStateHandler}
/>
```

Lets add them to the `HomeScreen` component

```js
async onLogin(e) {
  let resp = await authenticate(this.state.email, this.state.password);
  if (resp.authenticated === true) {
    this.setState({ authenticated: true, user: resp.user });
  } else {
    this.setState({ message: resp.message, renderLoginForm: false });
  }
}

emailStateHandler = text => {
  this.setState({ email: text });
};
passwordStateHandler = text => {
  this.setState({ password: text });
};
```

We will need to set a few new states for all of this to work.

```js
constructor(props) {
    super(props);
    this.state = {
      articles: [],
      renderLoginForm: false,
      authenticated: false,
      user: "",
      email: "",
      password: ""
    };
  }
```

Now we should be able to login but the login form is still being shown, lets fix that by making the `renderLogin` function return a small greeting to the user.

```js
renderLogin() {
    if (this.state.authenticated === true) {
      return <Text>Hi {this.state.user}</Text>;
    } else {
      if (this.state.renderLoginForm === true) {
      return (
        <View>
          <LoginForm
            loginHandler={this.onLogin.bind(this)}
            handleEmail={this.emailStateHandler}
            handlePassword={this.passwordStateHandler}
          />
        </View>
      );
    } else {
      return (
        <>
          <Button
            title="Login"
            onPress={() => this.setState({ renderLoginForm: true })}
          />
        </>
      );
    }
  }
}
```

And now when we login successfully we should get screen similar to this one

![Successfull Login](successful_login.png)
