`yarn add cypress-file-upload -D`

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
    cy.visit("http://localhost:3001");
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

