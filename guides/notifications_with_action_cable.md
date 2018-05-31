## Notifications in Rails


## Redis

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
        connected() {
            // Called when the subscription is ready for use on the server
            console.log('Connected to websocket server ');
        },

        disconnected() {
            // Called when the subscription has been terminated by the server
            console.log('Disconnectd');
        },

        received(data) {
            // Data received
            let container = document.getElementById('notifications');
            container.innerHTML = data.message;
            console.log(data);
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

## Maual testing

**Note: Make sure your redis service is up and running (see above).**

Open two separate tabs in your terminal. In one of them start the rains server and in the other the rails console. Open your site in your browser by navigateing to `http://localhost:3000` 

In your console, issue the following command:

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

## Wrapup

Your notifications are now configured and ready to be used in whatever way you deem fit for your application. In the next guide we will take a closer look at testing Action Cable. 
