# The Fetch method

Just for the case of learning, we will take a look at the `fetch` function (a native part of JavaScript).

Please examine the following code and tests. As before, we have left the code we don't use in place.

```js
import React, { Component } from 'react'
// import axios from 'axios'

class EmployeeList extends Component {
  state = {
    employees: []
  }

  async componentDidMount() {
    // for XMLHttpRequest
    // const xhr = new XMLHttpRequest();
    // xhr.open('GET', 'https://reqres.in/api/users')
    // xhr.onload = () => {
    //   this.setState({ employees: JSON.parse(xhr.responseText).data })
    // }
    // xhr.send()
    // for Axios
    // let response = await axios.get('https://reqres.in/api/users')
    // for fetch
    let response = await (await fetch(`https://reqres.in/api/users`)).json()

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

And the test file:

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
      { "id": 6, "email": "tracey.ramos@reqres.in", "first_name": "Tracey", "last_name": "Ramos", "avatar": "https://reqres.in/img/faces/6-image.jpg" }
    ],
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
  // for XMLHttpRequest
  // jest.spyOn(window, 'XMLHttpRequest').mockImplementation(() => xhrMock);
  // for Axios
  // axiosSpy = jest.spyOn(axios, 'get').mockResolvedValue(mockedResponse)
  // for fetch
  jest.spyOn(window, 'fetch').mockResolvedValue({
    json: async () => (mockedResponse)
  })

  render(<EmployeeList />);
  // xhrMock.onload()
});

it('calls the API using XHR/Axios/Fetch on render', () => {
  // for XMLHttpRequest
  // expect(xhrMock.open).toBeCalledWith('GET', 'https://reqres.in/api/users');
  // for Axios
  // expect(xhrMock.send).toHaveBeenCalledTimes(1);
  // expect(axiosSpy).toHaveBeenCalledTimes(1);
  // for fetch
  expect(fetch).toHaveBeenCalledTimes(1);
});
it('renders learn react link', () => {
  const listElement = screen.getByRole('list')
  expect(listElement.children.length).toBe(6);
});
```