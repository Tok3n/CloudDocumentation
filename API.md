# Tok3n Cloud API documentation
The Tok3n Cloud API provides two actions:

1. User Vinculation: to add a new Tok3n user to your integration
2. User Authentication: once the user has been vinculated, start a  secure authentication.

The needed steps to integrate both actions in the Tok3n Cloud API are as follows:

1. Acquire integration keys from the Tok3n Cloud Dashboard.
2. Load the Tok3n JavaScript snippet in your webpage.
3. Handle API Response.
4. Send to backend.
5. Re-validate in backend.

NOTE: This steps are the same for both actions to ensure that when we add more functionality you don't have to add any more steps to handle the new functionality.

##*User Vinculation* in depth:
### Acquire Keys

Login to the the Tok3n Dashboard, select the Integrations tab on the left. Select your current integration or create a new one.

![alt text](https://raw.githubusercontent.com/Tok3n/CloudDocumentation/master/API/keys.png "Acquire Keys")

The *API key* field is your **Public Key** and the *Secret* field is your **Secret Key**. Keep them in a secure place.

### Load Javascript Snippet
Insert the following script tag in the bottom of webpage you plan to use Tok3n authentication:

```html
<script src="https://secure.tok3n.com/api_v2_iframe/tok3n2.js"></script>
```

This will allow you to have the `Tok3n` Javascript object available. 

When you are ready to run the Tok3n integration just call:

```javascript
Tok3n.showIFrame("authorize", "{{YOUR-PUBLIC-KEY}}", "",tok3nResonseMethod);
```

Replace the `{{YOUR-PUBLIC-KEY}}` placeholder with the **Public Key** retrieved from the Tok3n Dashboard. And the `tok3nResonseMethod` for the name of the method that will be called when the Authentication has a response.

A couple of seconds after the method `Tok3n.showIFrame()` has been called, the Tok3n modal window will appear on top of your web content. The authentication process will begin for the user:

![alt text](https://raw.githubusercontent.com/Tok3n/CloudDocumentation/master/API/login1.png "Login 1")

#### Handle Response

For now, you will know that the user has accepted a valid authentication when method `tok3nResonseMethod` is called.

A example of this is the following method:

```javascript
// JAVASCRIPT CODE
function tok3nResonseMethod(e){
  console.log("Tok3n response:");
  console.log(e.detail);
  // You can then do something with e.detail
  // which contains the Tok3n response.
}

```

In this example the function `tok3nResonseMethod` handles the response of the Token Cloud API. It will receive the information of the 'validation data' in `e.detail`.

The 'validation data' will be a JSON formatted object similar to this:

```json
{
  "Valid":"YES",
  "CertKey":"7448aa2e-3463-53b8-5245-5b594be5b129",
  "PublicKey":"9765601a-433b-5e1b-7a65-c07c555afe9d",
  "UserKey":"37912139-5d4a-5c7a-5e93-f8db229e64a7",
  "TransactionId":"ba4851d2-37dc-5292-626b-1beb87e3728d",
  "Hash":"48 c9 63 52 ce e0 bd 8b 44 de d6 81 fc e7 f1 f7 70 f8 b9 53 c2 c8 9a fe d0 9f 0b f8 6b fc aa 93",
  "Result":""
}
```

From this values there is one that is fundamentally important for you. The value of `UserKey` is how the Tok3n Cloud API will call this user in your system. In further communications with the Tok3n Cloud API about the current user, it should be in company of this value. So you might store it aside of the user record in your database.

#### Send to backend
Once you get the previous 'validation data' you should send it to your backend in any method you like.

#### Re-validation from Tok3n
This step is added to ensure more security. So you should call 
`https://secure.tok3n.com/api/v2/validate` and send the following variables via POST:

| Variable      | Value                                             | 
| ------------- | ------------------------------------------------- | 
| secret_key    | Secret Key (retrieved from the dashboard)           |
| user_key      | The JSON value of the `UserKey`                   |
| TransactionId | The JSON value of the `TransactionId`             |
| sqr           | The entire JSON of the 'validation data'   |

If there is no error you should see a JSON as the following

```json
{
  "Valid":"",
  "CertKey":"",
  "PublicKey":"",
  "UserKey":"",
  "TransactionId":"",
  "Hash":"",
  "Result":"VALID USER"
}
```

But if there is an attack or an error in the data the request will call you a `ERROR: ...` or the `Result` field will show you the value `INVALID USER`.

And that's pretty much of it. 

## *User Authentication*:
The authentication happen when you user already has authorized at least once with your integration and you already have its UserKey.

<!---
The authentication happens when the user you sent has been authorized at least once  and you have recieved its UserKey.
-->


The differences for the *User Authentication* is the action attribute (the fist one) **"authenticate"** instead of "authorize". And the third argument you provide the **UserKey** as follow:

```html
Tok3n.showIFrame("authenticate", "{{YOUR-PUBLIC-KEY}}", "{{THE-USER-KEY}}", tok3nResonseMethod);
```

Replace the placeholder `{{YOUR-PUBLIC-KEY}}` with the **Public Key** retrieved from the Tok3n Dashboard, and the `{{THE-USER-KEY}}` placeholder with the **UserKey** received in the vinculation process.

When you insert this tag only the QR code will appear (as shown in the next image) because in this scenario we already know who is trying to authenticate.

![alt text](https://raw.githubusercontent.com/Tok3n/CloudDocumentation/master/API/login2.png "Login 2")

In all the other steps, the way the Tok3n Cloud API works the same as in the *User Vinculation* process.

Send comments here http://goo.gl/forms/yMiOYifjGT
