# Send an SMS easily

Send an SMS easily using [webtask.io](https://webtask.io)

![Send SMS](https://cdn.auth0.com/webtask/sendSms.gif)

## Interesting pieces

### `smsSender.js` WebTask

The [`smsSender.js` WebTask](https://github.com/auth0/webtask-samples/blob/master/sms-sender/smsSender.js) which will get run by [webtask.io](https://webtask.io)

````js
var request = require('request');

return function (context, callback) {
    var required_params = ['TWILIO_AUTH_TOKEN', 'TWILIO_ACCOUNT_SID', 'to', 'TWILIO_NUMBER', 'message'];
    for (var p in required_params)
        if (!context.data[required_params[p]])
            return callback(new Error('The `' + required_params[p] + '` parameter must be provided.'));

    request({
        url: 'https://api.twilio.com/2010-04-01/Accounts/' + context.data.TWILIO_ACCOUNT_SID + '/Messages',
        method: 'POST',
        auth: {
            user: context.data.TWILIO_ACCOUNT_SID,
            pass: context.data.TWILIO_AUTH_TOKEN
        },
        form: {
            From: context.data.TWILIO_NUMBER,
            To: context.data.to,
            Body: context.data.message
        }
    }, function (error, res, body) {
        callback(error, body);
    });
}
````

### Calling the Webtask using JS proxies

We'll use the JS proxies generated by [webtaskify](http://github.com/auth0/webtaskify) to call [webtask.io](https://webtask.io) from our [index.html](https://github.com/auth0/webtask-samples/blob/master/sms-sender/index.html#L33-L43)

```js
$('#send').click(function(e) {
  e.preventDefault();
  webtask.smsSender.run({
    message: $('#text').val(),
    to: $('#phone').val()
  }).then(function(response) {
    alert("Text message was sent");
  }, function(error) {
    alert("Error sending message");
  });
});
```


## Using the example

### 1. Install `webtaskify` & login

First, you need to install `webtaskify` to create JS proxies to call Webtask.io.

```bash
npm install -g webtaskify
```

```bash
webtaskify login
> Enter your WebTask account name: [accountName]
> Enter your WebTask Token (hidden): [accountToken]
```

### 2. Configure the SMS sender with your Twilio account

Create a `.env` file with your Twilio configuration with the following format:

```properties
TWILIO_ACCOUNT_SID= #Twilio Account SID
TWILIO_AUTH_TOKEN= #Twilio Auth token
TWILIO_NUMBER= # From number for the SMS
```

### 3. Create the JS proxies to call webtask.io

```bash
webtaskify create -f ./smsSender.js
```

### 4. Run & Enjoy

It's time to run the app and see it working. Just serve the current directory with `serve` or any other tool



