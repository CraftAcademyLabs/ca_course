# Axios

Axios is a library for making asynchronous network requests. It is designed to handle http requests and responses.
It's used more often than Fetch (see next section) because it has a larger set of features and it supports older browsers.

Axios deals with responses using Promises, so it's streamlined and easy to use in our code. Axios uses methods like `get()` and `post()` that perform http `GET` and `POST` requests for retrieving or creating resources (there's also `put()` and `delete()` functions built into Axios).

You need to install it in your project. Run:
```
$ yarn add axios
```

We will modify our test a bit. I've left the commands we don't need anymore as comments. Please make sure you examine the code  and remove the unnecassary code before you start running the tests.

```js
import { render, screen } from '@testing-library/react';
import EmployeeList from '../components/EmployeeList';
import axios from 'axios'

const mockedResponse =
{
  "page": 1,
  "per_page": 6,
  "total": 12,
  "total_pages": 2,
  "data":
    [
      { "id": 1, "email": "george.bluth@reqres.in", "first_name": "George", "last_name": "Bluth", "avatar": "https://reqres.in/img/faces/1-image.jpg" },
      { "id": 2, "email": "janet.weaver@reqres.in", "first_name": "Janet", "last_name": "Weaver", "avatar": "https://reqres.in/img/faces/2-image.jpg" },
      { "id": 3, "email": "emma.wong@reqres.in", "first_name": "Emma", "last_name": "Wong", "avatar": "https://reqres.in/img/faces/3-image.jpg" },
      { "id": 4, "email": "eve.holt@reqres.in", "first_name": "Eve", "last_name": "Holt", "avatar": "https://reqres.in/img/faces/4-image.jpg" },
      { "id": 5, "email": "charles.morris@reqres.in", "first_name": "Charles", "last_name": "Morris", "avatar": "https://reqres.in/img/faces/5-image.jpg" },
      { "id": 6, "email": "tracey.ramos@reqres.in", "first_name": "Tracey", "last_name": "Ramos", "avatar": "https://reqres.in/img/faces/6-image.jpg" }],
  "support": { "url": "https://reqres.in/#support-heading", "text": "To keep ReqRes free, contributions towards server costs are appreciated!" }
}
// const xhrMock = {
//   open: jest.fn(),
//   send: jest.fn(),
//   setRequestHeader: jest.fn(),
//   readyState: 4,
//   status: 200,
//   responseText: JSON.stringify(mockedResponse)
// };

let axiosSpy
beforeEach(() => {
  // jest.spyOn(window, 'XMLHttpRequest').mockImplementation(() => xhrMock);
  axiosSpy = jest.spyOn(axios, 'get').mockResolvedValue(mockedResponse)
  render(<EmployeeList />);
  // xhrMock.onload()
});

it('calls the API using XHR on render', () => {
  // expect(xhrMock.open).toBeCalledWith('GET', 'https://reqres.in/api/users');
  // expect(xhrMock.send).toHaveBeenCalledTimes(1);
  expect(axiosSpy).toHaveBeenCalledTimes(1);
});
it('renders learn react link', () => {
  const listElement = screen.getByRole('list')
  expect(listElement.children.length).toBe(6);
});
```

Now, we need to change our implementation code and make use of Axios instead of the more traditional XHR request.

Here, I also leave the old code as comments for you to review.

```js
import React, { Component } from 'react'
import axios from 'axios'

class EmployeeList extends Component {
  state = {
    employees: []
  }

  async componentDidMount() {
    // const xhr = new XMLHttpRequest();
    // xhr.open('GET', 'https://reqres.in/api/users')
    // xhr.onload = () => {
    //   this.setState({ employees: JSON.parse(xhr.responseText).data })
    // }
    // xhr.send()
    let response = await axios.get('https://reqres.in/api/users')
    this.setState({ employees: response.data })

  }


  render() {
    let employeeList
    employeeList = this.state.employees.map(employee => {
      return (
        <li key={employee.id}>
          {`${employee.first_name} ${employee.last_name}`}
        </li>
      )
    })
    return (
      <ul role="list">
        {employeeList}
      </ul>
    )
  }
}

export default EmployeeList;
```

As you can see, the only thing we change is the way we get the data. Nothing else needs to change.
