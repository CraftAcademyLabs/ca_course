## Extending the SlowFood application
Opening up the SlowFood application to the outside world as well as adding some cloud based services.

This weeks challenge is about adding more functionality to the Slow Food application. The client has reached out to us and gave us some new requirements. First he wants to store all images that are used on the system on Amazon Web Services and second, he also wants us to expose the application data using a RESTful API.  

### Cloud Storage
All image assets needs to be stored on an AWS S3 bucket. 

#### Action points
* Create stories in PT regarding Cloud Storage functionality
* Create an account on AWS
* Create an S3 bucket
* Research an appropriate gem to use
* Set up necessary permissions to upload assets to the S3 bucket from your application



### RESTful API
All application data should be able to be retrieved through a separate set of uri's. Authorized users should be able to place orders using the API. 

#### Action points
* Create stories in PT regarding API functionality desribing all endpoints you deem necessary
* Create a new namespace in your application
* Set up tests (request specs) for the API endpoints
* Set up controllers for various API endpoints
* If necessary, add custom views using JBuilder

The API should be accessible using Postman.