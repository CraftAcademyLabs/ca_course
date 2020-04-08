`yarn add react-facebook`

```js
import React, { useState } from 'react'
import { FacebookProvider, LoginButton } from 'react-facebook';
import axios from 'axios';

const FacebookLogin = () => {
  const [authenticated, setAuthenticated] = useState(false)

  const handleResponse = async data => {
    const response = await axios.post("http://localhost:3000/api/v1/auth", {
      uid: data.profile.id,
      email: data.profile.email,
      provider: "facebook"
    })

    if (response.status == 200) {
      setAuthenticated(true) 
      // here we are using local state
      // but perhaps you want to send off if the user is authenticated
      // to the redux store instead
    } else {
      // other code, handle error message
    }
  }

  return (
    <>
      <FacebookProvider appId="your-fb-app-id" >
        <LoginButton
          scope="email"
          onCompleted={handleResponse()}
        >
          <span>Login via Facebook</span>
        </LoginButton>
      </FacebookProvider>
    </>
  )
}

export default FacebookLogin
```