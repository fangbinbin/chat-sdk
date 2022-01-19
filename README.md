# LiveChat chat-sdk
​
Lightweight JavaScript SDK for LiveChat Messaging APIs.
​
Chat SDK was designed to help developers build apps in a quick and easy way. Under the hood, it makes use of the LiveChat Messaging APIs, taking care of this part for you. By default, Chat SDK comes with a method for sending plain text messages. Based on the method template we provide, you can easily build other methods. Thanks to that, you have the flexibility to cover the functionalities you need for your app.
​

For full documentation, please head to __[LiveChat Docs](https://developers.livechat.com/docs/messaging/chat-sdk/)__.
​
## Installation
​
The package can be installed directly from NPM.
​
```
npm i @livechat/chat-sdk
```
​
## Initial configuration
​
###
| Parameter         | Required | Data type | Default   | Notes                                                    |
| ----------------- | -------- | --------- | --------- | -------------------------------------------------------- |
| apiVersion        | `false`  | `string`  | `v3.4`    | Call a different API version than the default one.       |
| debug             | `false`  | `boolean` | `false`     | Display all messages exchanged with the LiveChat API.    |
| region            | `false`  | `string`  | `america` | Specify a data center. Possible values: `europe` and `america`. |
​
```js
const chatSDK = new ChatSDK({ debug: true })
```
​
### Authorization

  To use Chat SDK, you will need to provide an access token authorizing you with the [Agent Chat API](https://developers.livechat.com/docs/getting-started/authorization/sign-in-with-livechat/#sdk-documentation).

  If you don't know how to get one, make sure to check out these resources:

 - [Agent authorization flows](https://developers.livechat.com/docs/getting-started/authorization/#agent-authorization-flows/)
 - [Messaging APIs in practice](https://developers.livechat.com/docs/getting-started/guides/messaging-apis-in-practice/)
 - [Personal Access Tokens](https://developers.livechat.com/docs/authorization/authorizing-api-calls#personal-access-tokens)
  
  
## Methods
​
### init
​
It initializes the WebSocket connection, attaches event listeners, and then logs in the Agent.
​
```js
chatSDK.init({
    access_token: access_token
    // OR 
    personal_access_token: personal_access_token
})
```

  #### Parameters

| Parameter      | Required | Data type | Notes                                                                                                                                                                                                                                                                                              |
| -------------- | -------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `access_token` | Yes/No<sup>**1**</sup>      | `string`  | See [Authorization](#authorization) to learn how to get an access token. Optionally, you can acquire it directly from [Accounts SDK](/getting-started/authorization/sign-in-with-livechat/#sdk-documentation) and pass it in to the `init()` method. The **Simple Agent** app uses this mechanism. |
| `personal_access_token` |   Yes/No<sup>**1**</sup>    | `string`  | See [Authorization docs](/authorization/authorizing-api-calls#personal-access-tokens) to learn how to get a Personal Access Token. Provided value should be encoded in `base64`. |

**1)** The `init` function requires one of the parameters to be provided.

​
### destroy
​
It clears any stored resources, removes all listeners, and disconnects from the network. After using this method you won't be able to use the destroyed SDK instance.
​
```js
chatSDK.destroy()
```
​
### getAgentDetails
​
It allows you to get information about the currently logged in Agent.
​

```js     
chatSDK.getAgentDetails() 
    .then(agentData => {
        // access to the agentData object
    }) 
    .catch(error => {
        // catch an error object
    })
 ```
​
### sendMessage
​
It sends a plain text message.
​
#### Arguments
| Argument   | Data type | Notes                                       |
| ---------- | --------- | ------------------------------------------- |
| chat_id    | `string`  | Id of chat that you want to send a message to. |
| message    | `string`  | Message content                             |
| recipients | `string`  | Possible values: `all` (default), `agents`  |
​
#### Returned value 
| Value      | Data type |
| ---------- | --------- |
| event_id   | `string`  |
​
```js
const chatId = 'PJ0MRSHTDG'
const message = 'Hello world'
​
chatSDK.sendMessage(chatId, message)
    .then(event => {
        // get event_id or handle a success scenario
    })
    .catch(error => {
        // catch an error object
    })
```
​
### How to create other methods - methodFactory
​
This SDK supports the RTM transport. For that reason, make sure you use the [Agent Chat API RTM](https://developers.livechat.com/docs/messaging/agent-chat-api/rtm-reference/) reference. When creating your custom methods, base on the payloads from the [Agent Chat RTM API methods](https://developers.livechat.com/docs/messaging/agent-chat-api/rtm-reference/#methods).
​
In the example below, we're creating a custom method that returns a chat. 
As you can see in the [documentation](https://developers.livechat.com/docs/messaging/agent-chat-api/rtm-reference/#get-chat), only `chat_id` is required, but you can include other optional parameters in your custom method.
​
```js
const getChat = (chat_id) =>
  ChatSDK.methodFactory({
    action: "get_chat",
    payload: { chat_id }
  });

​
getChat("PJ0MRSHTDG")
    .then(data => {
        // get a chat details with the latest thread (if exists)
    })
    .catch(error => {
        // catch an error
    })
```
## on
​
The on method subscribes to emitted events.
​
```js
chatSDK.on("event_name", (data) => {
    console.log(data)
})
```
Here's what you can listen for:

  | Event name   | Action     | 
 | ------------ | ---------- |
 | `ready`      | You've been successfully logged in. You're now ready to use all API methods. |
 |  Pushes      | Refer to [documentation](https://developers.livechat.com/docs/messaging/agent-chat-api/rtm-reference/#pushes).   |


## once
​
The once method subscribes to emitted events and unsubscribes right after the callback function has been called.
​
```js
chatSDK.once("event_name", (data) => {
    console.log(data)
})
```
​
## off
​
The off method unsubscribes from emitted events.
​
```js
chatSDK.off("event_name", (data) => {
    console.log(data)
})
```
​
## Simple Agent - Example

To show you **Chat SDK** in action, we've prepared a sample app, **Simple Agent** . Its primary function is to send text messages. Apart from that, it gives you access to previous conversations, as well as the info about the current Agent.

To run the app, follow a few steps:

1. [Create an app](/getting-started/guides/#creating-livechat-apps) in Developer Console.

2. Add the **Authorization** building block. Configure it by entering _http://localhost:3000/_ in **Redirect URI whitelist** and by adding the following **scopes**:
    - `chats--all:rw`
    - `chats--access:rw`
    - `customers:ro`
    - `multicast:ro`
    - `agents--all:ro`
    - `agents-bot--all:ro`

3. Go to the **Private installation** tab and install the app.

4. Clone the <a href="https://github.com/livechat/chat-sdk/" target="_blank" rel="noopener noreferrer">Chat SDK repository</a> from GitHub and go to the **example** folder.

5. In <a href="https://developers.livechat.com/console/" target="_blank" rel="noopener noreferrer">Developer Console</a>, go to the **Authorization** building block of your app. 

6. Copy **Client Id** and paste it into the `.env` file in the **example** folder.

7. Run **Simple Agent** with the following commands (from the **example** folder perspective):

```js
npm install // install dependencies 
npm start //start the app
```

To start a chat, log in to you LiveChat account and choose the **Preview live** option available in the **Settings** tab. You'll now be able to receive messages and respond to them from within **Simple Agent**.

It's worth mentioning that all functions invoked before logging in are queued. Once you're logged in, they are executed in the same order as they were invoked.  

## Feedback
​
If you find some bugs or have troubles implementing the code on your own, please create an issue on this repo.
​
## If you're new to LiveChat
​
LiveChat is an online customer service software with live support, help desk software, and web analytics capabilities. It's used by more than 34,000 companies all over the world. For more info, check out [LiveChat for Developers](https://developers.livechat.com).
