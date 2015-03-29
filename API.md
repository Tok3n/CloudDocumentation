# Tok3n Cloud API documentation
The Tok3n Cloud API works for two actions:

1. User Vinculation: to add a Tok3n user to your integration
2. User Authentication: once the user is viculated you can just ask for a secure authentication.

Here we will explain the needed steps to integrate both actions in the Tok3n Cloud API:

1. Adquire Keys: Go to the Tok3n Cloud Dashboard and adquire both of them.
2. Import Tok3n JavaScript: Import a JS from Tok3n Cloud API with some specifications that we will explain in a moment.
3. Custom Event: Add to the Script Tag a listener in the custom event "response"
4. Handle Response: In the response the event will send you some "validation information" formated in JSON.
5. Send to backend: Then you should send that validation information to your backend.
6. Re-validate in backend: Once the validation information is in your backend you should send it again to the Tok3n Cloud API.
7. Re-validation from Tok3n: tok3n will respond you about the validity of the "validation information". (In case of a man in the middle atack).

NOTE: This steps are the same for both actions to ensure that when we add more functionality you don't have to add any more steps to handle the new functionality.

##In depth for *User Vinculation*:
### Adquire Keys

When you enter the Tok3n Dashboard, in the Integrations section, you can create new integrations. Once created an integration, a screen similar to the following will be shown:

![alt text](https://raw.githubusercontent.com/Tok3n/CloudDocumentation/master/API/keys.png "Adquire Keys")

The *API key* field is your **Public Key** and the *Secret* field is your **Secret Key**. Store them in a very secure place you will be using them later.

### Import Tok3n JavaScript
Now in your page, where the authentication is happening, add the following tag:

```html
<script src="//secure.tok3n.com/api_v2_iframe/tok3n.js" 
  data-tok3n-integration
  action="authorize"
  public-key="{{YOUR-PUBLIC-KEY}}"></script>
```

And in the attribute `public-key` instead of `{{YOUR-PUBLIC-KEY}}` you will include the **Public Key** you got from the Tok3n Cloud Dashboard. To this tag you can add any other atribute you want for example an **id** if you want to reference it later (you will need it).

When this JS in added, an iframe from the Tok3n Cloud will appear over your screen asking user for their Tok3n credentials. As shown in following screens:

![alt text](https://raw.githubusercontent.com/Tok3n/CloudDocumentation/master/API/login1.png "Login 1")
![alt text](https://raw.githubusercontent.com/Tok3n/CloudDocumentation/master/API/login2.png "Login 2")

#### Custom Event & Handle Response
In advance, we know that our implementation is weird, beliebe us we are working (testing) in a more standard way of calling the API. For now, you will know that the user has acepted a valid authentication when the custom event "response" is called in the previous *script* tag. Remember that we recomend to add an id attribute to the *script* tag, well this **id** can help you in the task of creating the custom event.

For example if your **id** is "tok3nscripttag" you can use:

```javascript
// JAVASCRIPT CODE
function tok3n_response(e){
  console.log("Tok3n responded with:");
  console.log(e.detail);
  //DO SOMETHING WITH THE e.detail WICH CONTAIN THE 'VALIDATION INFORMATION'
}

document.getElementById("tok3nscripttag").addEventListener('response', tok3n_response, false);
```

In this example the function `tok3n_response` is the one that take care of the response of the Token Cloud API. It will recive the information of the 'validation information' inside the `e.detail` variable.

The 'validation information' is a json formated object that looks like this:

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

From this values there is one that is fundamentally important for you. The value of `UserKey` is how the Tok3n Cloud API will call this user in your system. In further comunicacions with the Tok3n Cloud API about the current user, should be in company of this value. So you might store it a side of the user record in your database.

#### Send to backend
Once you get the previous 'validation information' you should send it to your backend in any method you like. Prefereably you should send it via AJAX once you have it in the respond of the Custom Event.

#### Re-validation from Tok3n
This step is added to add more security. So you should call 
'https://secure.tok3n.com/api/v2/validate' and send the following variables via POST:

| Variable      | Value                                             | 
| ------------- | ------------------------------------------------- | 
| secret_key    | Secret Key (the one from the dashboard)           |
| user_key      | The json value of the `UserKey`                   |
| TransactionId | The json value of the `TransactionId`             |
| sqr           | The entire json of the 'validation information'   |

If there is no error you should see a json as the following

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

But if there is an atack or an error in the data the request will call you a `ERROR: ...` or the `Result` field will show you the value `INVALID USER`.

And that's pretty much it. 

## Now *User Authentication*:
For the *User Authentication* action the only difference is when you Import the Tok3n JavaScript. Instead of the tag we have show you previously you should use the following code:

```html
<script src="//secure.tok3n.com/api_v2_iframe/tok3n.js" 
  data-tok3n-integration
  action="authenticate"
  public-key="{{YOUR-PUBLIC-KEY}}"
  user-key="{{THE-USER-KEY}}"></script>
```

In the attribute `public-key` instead of `{{YOUR-PUBLIC-KEY}}` you will include the **Public Key** you got from the Tok3n Cloud Dashboard, and in the attribute `user-key` instead of `{{THE-USER-KEY}}` you will use the `UserKey` of the Tok3n Cloud API User (the one we tell you to store a side with the user in the database). Again to this tag you can add any other atribute you want for example an **id** if you want to reference it later (you will need it).

In all the other steps, the way the Tok3n Cloud API works is the same as in the *User Vinculation*.
