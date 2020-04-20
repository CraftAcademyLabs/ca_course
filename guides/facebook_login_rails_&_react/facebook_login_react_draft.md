So now it is time to set up Facebook login on the client. So what is it that we need to do here?

We need to get a unique user id and an email from Facebook when the user has successfully authenticated with Facebook and send that to the API to create a user with that information. That will also create a current session in the backend and will return a token to the client that we need to store in the browser `localStorage`.

The first thing we need to do is add the [`react-facebook` package](https://www.npmjs.com/package/react-facebook).

`yarn add react-facebook`


I will add the functionality for Facebook login to a functional component called `FacebookLogin`. This component will only handle this functionality. In this example, we are not sending off the response data to the redux store or any other component. We are setting a state of authenticated to true if everything works out and we set the token headers in `sessionStorage` so the client can send them back to the API when it is needed to authorize the users' current session.

```js
import React, { useState } from 'react'
import { FacebookProvider, LoginButton } from 'react-facebook';
import axios from 'axios';

const FacebookLogin = () => {
  const [authenticated, setAuthenticated] = useState(false)

  const handleResponse = async data => {
    try {
      let userEmail = data.profile.email
      const response = await axios.post("http://localhost:3000/api/v1/auth", {
        uid: data.profile.id,
        email: userEmail,
        provider: "facebook"
      })

      let tokenHeaders = {
        ...response.headers,
        ...response.data,
        email: userEmail
      }
      sessionStorage.setItem("J-tockAuth-Storage", JSON.stringify(tokenHeaders))
      setAuthenticated(true) 
      // here we are using local state
      // but perhaps you want to send it off to the redux store
      // if the user is authenticated
    } catch(error) {  
      // other code, handle error message
    }
    
  }

  return (
    <>
      <FacebookProvider appId="your-fb-app-id-here" >
        <LoginButton
          scope="email"
          onCompleted={handleResponse}
        >
          <span>Login via Facebook</span>
        </LoginButton>
      </FacebookProvider>
      {authenticated && <p id="welcome-message">
        Welcome {JSON.parse(sessionStorage.getItem("J-tockAuth-Storage")).email}
      </p>}
    </>
  )
}

export default FacebookLogin
```

Let's go over what is happening here.
We are importing `FacebookProvider` and `LoginButton` from `react-facebook`.
`FacebookProvider` is the component that we need to pass in our Facebook app id to. If you need help with getting an app id from Facebook, contact a coach.
`LoginButton` is the actual component that adds the button, this will lead to a pop up that leads you to Facebook. The user will have to accept that our application can use their Facebook information. If the user accepts this, it has been completed. You can see that we have an `onCompleted` event on the `LoginButton`, so that will call on the `handleResponse` function.

The `handleResponse` takes the data that we got from Facebook and sends it to the API to create a user from it and create a current session for the user. If everything works out, we will grab the response headers and data and put it in a variable called `tokenHeaders`. We will also put the email we got from Facebook in this variable. Then we will stringify this and put it in `sessionStorage` in the key of `J-tockAuth-Storage`. That data needs to be sent back to the API on requests that go to a controller action that requires the user to be logged in. 

Once that is done we set the state of `authenticated` to true. If something were to go wrong during the request, it would hit the catch block. If you want to display some sort of error message, that is the place to do it.

That's it, everything should work for you now!
