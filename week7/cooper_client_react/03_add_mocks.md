So the login functionality works now, but do we really want to test against our live backend? In order to not do that, we can set up mocks for this.

First, we need to create the folder and files we will work with:

```shell
$ mkdir src/__mocks__
$ touch mocksConfig.js
$ touch mockResponses.js

```

Add this to `mocksConfig.js`, we will go through everything together.

```js
const MockResponses = require('./mockResponses')
beforeAll(async () => {

  const createResponse = (path, params, request) => {
    let response
    switch (path) {
      case 'sign_in':
        let user
        user = MockResponses.mockedUserResponses.find(user => {
          return user.headers.uid === JSON.parse(params).email
        })
        response = user || MockResponses.missingUserResponse
        return respohttp://localhost:3001nse
    }
    
  }

  const requests = {
    'sign_in': {}
  }

  await page.setRequestInterception(true);

  await page.on('request', interceptedRequest => {
    const requestedEndpoint = interceptedRequest.url().split("/").pop().split('?')[0];
    console.log("Making request to: " + requestedEndpoint)
    if (requests[requestedEndpoint]) {
      params = interceptedRequest.postData()
      interceptedRequest.respond(createResponse(requestedEndpoint, params, interceptedRequest));
    } else {
      interceptedRequest.continue();
    }
  })
})
```

The `beforeAll` block makes sure that everything in this file runs before every feature test. 

We need to add a puppeteer setting to intercept all requests in the test environment called `setRequestInterception`.

The requests variable we declare will store the route we want to intercept.

Underneath where we tell puppeteer to intercept all requests, we split the URL we intercept to only get the route the application is trying to send a request to. If that route is declared in the `requests` variable,  puppeteer will intercept it.

When we intercept it, we call on the `createResponse` method. Here we set what response the request will have. So first it checks the route, at this point, we only have one defined, which is `sign_up`. If the user we have in the params of the requests match the one we have defined in the `mockedUserResponses`, it will return that response. But if it does not find that one it will go with the `missingUserResponse`, which we need to have for our sad path in the feature test.

Let's add these responses to `mockResponses.js`:

```js
module.exports = {

  mockedUserResponses: [
    {
      status: 200,
      headers: {
        "access-token": "AfJSIl6P1CYM0Qc0vmTfXQ",
        client: "aGh-lsYlUZasOM3mcil9cQ",
        expiry: "1550652483",
        uid: "johndoe@mail.com",
        "token-type": "Bearer"
      },
      body: JSON.stringify({
        data:
        {
          id: 1,
          email: "johndoe@mail.com",
          provider: "email",
          uid: "johndoe@mail.com"
        }
      })
    }

  missingUserResponse: {
    status: 401,
    headers: {},
    body: JSON.stringify(
      {
        success: false,
        errors: ['Invalid login credentials. Please try again.']
      }
    )
  },
}
```

You have to require the `mocksConfig` in the `UserCanLogin.feature.js` Add this to the top of the file:

```js
require('../__mocks__/mocksConfig')
```

If you run the test again without running the backend, you will see that it goes green!

