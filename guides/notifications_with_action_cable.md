## Notifications in Rails

## About WebSockets
WebSockets are a protocol built on top of TCP. They hold the connection to the server open so that the server can send information to the client, even in the absence of a request from the client. WebSockets allow for bi-directional, "full-duplex" communication between the client and the server by creating a persistent connection between the two.

## RubSub (Publish–Subscribe) pattern

The idea behind PubSub messaging pattern is simple. There are receivers of messages, called subscribers, which are clients that listen for changes on a set of data. Then there are senders of messages, called publishers, which are clients that edit the set of data. Whenever the data is edited by a publisher, all the subscribers that are subscribed to that publisher hear it and act on it! 

## Redis

Redis is a data store that supports the PubSub messaging pattern and one that the ActionCable implementation makes use of.

Install Redis using `brew`

```bash 
$ brew install redis
```

Start the Redis server as a service

```bash 
$ brew services start redis
```

## Create and configure a Action Cable Channel

Add ActionCable engine to your `routs.rb` 

```ruby
Rails.application.routes.draw do
  mount ActionCable.server => '/cable'
end

```

Use a generator to scaffold a AC channel: 

```bash
$ rails g channel web_notifications
```

Update the channel settings by modifying the `subscribed` method:

```ruby
class WebNotificationsChannel < ApplicationCable::Channel
  def subscribed
    stream_from "web_notifications_channel"
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end
end

```

Create a javascript file in your `app/assets/javascripts/channels` folder. 

```javascript
// web_notification_channel.js
App.notifications = App.cable.subscriptions.create({
    channel: "WebNotificationsChannel"
}, {
        container() {
            const container = document.getElementById('notifications');
            return container;
        },

        connected() {
            // Called when the subscription is ready for use on the server
            // Display the active connection message (for demo purposes only)
            this.container().innerHTML = '<p>Connected to Notification server</p>'
            setTimeout(() => {
                this.container().innerHTML = '';
            }, 3000);  
            console.log('Connected to websocket server ');
        },

        disconnected() {
            // Called when the subscription has been terminated by the server
            // Display the connection has been lost message (for demo purposes only)
            this.container().innerHTML = '<p>Disconneced from server</p>'
            setTimeout(() => {
                this.container().innerHTML = '';
            }, 3000);  
            console.log('Disconneced');
        },

        received(data) {
            // Create an element that will hold the message
            let node = document.createElement('p');
            node.innerHTML = data.message;
            // Append the message to the element
            this.container().appendChild(node)

            setTimeout(() => {
                // Remove the node after 3 seconds
                this.container().removeChild(node);
            }, 3000);
        },

    }
);
```

Add div with the id `notifications` inside your main layout template's(`application.html.haml`) `body` tag:

```haml
  %body
    #notifications
    = yield
```

## Manual testing

**Note: Make sure your redis service is up and running (see above).**

Open two separate tabs in your terminal. First, let's try this out in our terminal by starting the Redis console:

```bash 
$ redis-cli
127.0.0.1:6379>
```
Next thing we want to do is to subscribe to the `WebNotificationsChannel`. In your Redis console, issue the following command (and you will get the following response) 

```bash
127.0.0.1:6379> subscribe "web_notifications_channel"
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "web_notifications_channel"
3) (integer) 1
```

Now head over to another terminal tab and open up the rails console:

```ruby
ActionCable.server.broadcast 'web_notifications_channel', message: '<p>Hello World!</p>'
```

Now, head back to the terminal tab where you run the redis console. You should see the following output:

```bash
1) "message"
2) "web_notifications_channel"
3) "{\"message\":\"\\u003cp\\u003eHello World!\\u003c/p\\u003e\"}"
``` 

If you do, the setup is okay and we are on a good path to get notifications working in our front end. **Let's try the whole thing out in our browser.**

Start the rains server in one of your terminal tabs, and in the other the rails console. Open your site in your browser by navigateing to `http://localhost:3000` 

In your console, issue the same broadcasting command again:

```ruby
ActionCable.server.broadcast 'web_notifications_channel', message: '<p>Hello World!</p>'
```

If you examine the browser window, you should see the message displayed. If you examine the logs for your server, you should see something similar to the following output: 

```bash 
Started GET "/cable/" [WebSocket] for 127.0.0.1 at 2018-05-31 11:04:12 +0200
Successfully upgraded to WebSocket (REQUEST_METHOD: GET, HTTP_CONNECTION: Upgrade, HTTP_UPGRADE: websocket)
WebNotificationsChannel is transmitting the subscription confirmation
WebNotificationsChannel is streaming from web_notifications_channel
WebNotificationsChannel transmitting {"message"=>"<p>Hello World!</p>"} (via streamed from web_notifications_channel)
```

## Usage

There's plenty of use cases we can showcase where notifications come in handy. You can broadcast messages from anywhare in your application. Here I will show you how to integrate ActionCable with an ActiveRecord model and notify all visitors whan a new instance has been created. Forr instance, we could notify all visitors about a new comment made on a `Recipe` in out recipe collection application. 

In our `Comment` model, we can add an `after_create` hook to broadcast a message:

```ruby
class Comment < ApplicationRecord
  belongs_to :recipe
  belongs_to :user

  after_create :notify

  def notify 
    ActionCable.server.broadcast 'web_notifications_channel', 
                                  message: "<p>#{self.user.email} left a comment on #{self.recipe.title}.</p>"
  end   
end
```

Now, every time a user leaves a comment a notification will be pushed out to all visitors of the application.

![](https://class.craftacademy.co/asset-v1:CraftAcademy+CA-CAMP+2018-april+type@asset+block@food-hub-comment-notification.gif)


## Wrapup

Your notifications are now configured and ready to be used in whatever way you deem fit for your application. In the next guide we will take a closer look at testing Action Cable. 
