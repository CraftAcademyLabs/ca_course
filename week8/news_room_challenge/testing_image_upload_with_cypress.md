This is a guide on how to stub out file uploading in cypress. Our example code will be a scenario where a journalist will create an article. This guide assumes that you have cypress configured.

The first thing you have to do is add the [`cypress-file-upload`](https://www.npmjs.com/package/cypress-file-upload) package.

`yarn add cypress-file-upload -D`'

Once that is done we need to import that package in one of the cypress support files. That file is `cypress/support/commands.js`. We will also create a custom command that the integration test can use in its scenarios.

```js
// cypress/support/commands.js

import 'cypress-file-upload'

Cypress.Commands.add("file_upload", (file, element, type) => {
  const selector = element;
  const fixturePath = file;
  cy.get(selector).then(subject =>
    cy.window().then(win =>
      cy
        .fixture(fixturePath, "base64")
        .then(Cypress.Blob.base64StringToBlob)
        .then(blob => {
          const el = subject[0];
          const testFile = new win.File([blob], name, { type });
          const dataTransfer = new win.DataTransfer();
          dataTransfer.items.add(testFile);
          el.files = dataTransfer.files;
          cy.wrap(subject).trigger("change", { force: true });
        })
    )
  )
}) 
```

The command we added is called `file_upload`. It will receive three arguments.
-  `file`: This will be the file that we will upload.
-  `element`: This will be the way of getting the specific element that holds the file input field
-  `type`: Will equal to what type of file we are stubbing out. e.g `image/jpeg`

Let's use this command in an integration test. The test we are providing you with here is very basic. We are filling in some input fields, then click on a submit button and then expecting to see a message that gives us some sort of confirmation that the article creation was successful. In the `before()` block we are stubbing out the response of the request our application will make when we submit the article.

```js
// cypress/integration/exampleFeatureTest.feature.js

describe("Journalist can", () => {
  before(() => {
    cy.server();
    cy.route({
      method: "POST",
      url: "http://localhost:3000/api/v1/articles",
      response: '{"message": "Your article was successfully created"}'
    });
    cy.visit("/");
  });

  it("create an article successfully", () => {
    cy.get("#new-article-form").within(() => {
      cy.get("#title").type("This is a title");
      cy.get("#content").type("This is some awesome content");
      cy.file_upload('img.jpeg', '#image-upload', 'image/jpeg');
    });

    cy.get("#create-article-button").click();
    cy.get("#message").should(
      "contain",
      "Your article was successfully created"
    );
  });
});
```

We are using the `file_upload` command inside of the it block after filling out the and content input fields. We are passing in three strings as arguments. The first one is a fixture file. Make sure you have an image in your `cypress/fixtures` folder. In our case, the image file name is `img.jpeg`. The second argument we pass in is the `id` of the file input element. The last argument is the type of file that we want to stub out the file input with. In our case it is `image/jpeg`, make sure this argument is the same as the type of file that you pass in with the first argument.

This is how you stub out a file upload in Cypress!

Here is an example of how the implementation code might look like. Notice that we have not extracted the axios call or the encoding of the image to another file, everything is in the same place so you can see the data flow a bit easier. We highly recommend that you extract this when you use this in your projects.

```js
import React, { Component } from "react";
import axios from "axios";

class CreateArticle extends Component {
  state = {

  }

  toBase64 = file => new Promise((resolve, reject) => {
    const reader = new FileReader()
    reader.readAsDataURL(file)
    reader.onload = () => resolve(reader.result)
    reader.onerror = error => reject(error)
  })

  submitArticle = async e => {
    e.preventDefault();
    let responseMessage, articleParams, encodedImage, response
    let { title, content, image } = e.target
    
    try {
      articleParams = {
        title: title.value,
        content: content.value
      }

      if (image.files[0]) {
        encodedImage = await this.toBase64(image.files[0])
        articleParams.image = encodedImage
      }

      response = await axios.post(
        "http://localhost:3000/api/v1/articles",
        { article: articleParams },
        { headers: { "Content-Type": "application/json" } }
      );

      responseMessage = response.data.message
    } catch(error) {
      responseMessage = response.data.error
    } finally {
      this.setState({ message: responseMessage });
    }
  };

  render() {
    return (
      <>
        <form id="new-article-form" onSubmit={this.submitArticle}>
          <input id="title" placeholder="Title" name="title" />
          <input id="content" placeholder="Content" name="content" />
          <input id="image-upload" name="image" type="file"/>
          <button id="create-article-button" type="submit">
            Create Article
          </button>
        </form>
        {this.state.message && <p id="message">{this.state.message}</p>}
      </>
    );
  }
}
export default CreateArticle;

```