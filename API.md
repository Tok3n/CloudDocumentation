# Tok3n Cloud API documentation
Here we will document the needed stepst to integrate the Tok3n Cloud API.
To authenticate users in your integration first you need that the user where vinculated to your integration, and we thinking on you we design a pretty simple and similar way to to both steps. So when you integrate the Tok3n Cloud API you only need to learn one flow and use it for several actions.

The general flow of an Tok3n Cloud API integration is the following:

1. Adquire Keys: Go to the Tok3n Cloud Dashboard and adquire both of them.
2. Import Tok3n JavaScript: Import a JS from Tok3n Cloud API with some specifications that we will explain in a moment.
3. Custom Event: Add to the Script Tag a listener in the custom event "response"
4. Handle Response: In the response the event will send you some "validation information" formated in JSON.
5. Send to backend: Then you should send that validation information to your backend.
6. Re-validate in backend: Once the validation information is in your backend you should send it again to the Tok3n Cloud API.
7. Re-validation from Tok3n: tok3n will respond you about the validity of the "validation information". (In case of a man in the middle atack).
8. Optional Crypto Hash revalidation: with the information of the validation information and the response from Tok3n in the point 7 you can re calculate a hash that tells you the validity of both documents.

##In depth:
### Adquire Keys

When you enter the Tok3n Dashboard in the Integrations section you can create there new integrations. Once created a screen similar to the following will be shown:

![alt text](https://raw.githubusercontent.com/Tok3n/CloudDocumentation/master/API/keys.png "Adquire Keys")

The *API key* field is your **Public Key** and the *Secret* field is your **Secret Key**. Store them in a very secure place you will be using them.

### Import Tok3n JavaScript
The next step is that in your page where the authentication is happening add the following tag:

```html
<script src="//secure.tok3n.com/api_v2_iframe/tok3n.js" 
  data-tok3n-integration
  action="authorize"
  public-key="{{YOUR-PUBLIC-KEY}}"></script>
```
And in the attribute `public-key` inthead of `{{YOUR-PUBLIC-KEY}}` you will include the **Public Key** you got from the Token Cloud Dashboard. To this tag you can add any other atribute you want for example an id if you want to reference it later (you will need it).

#### Custom Event & Handle Response
In advance, we know that our implementation is weird, beliebe me we are working (testing) in a more standard way of calling the API. For now, for you to know that the user has acepted a valid authentication is to add a listener to the event "response" in the previous *script* tag. Remember that we recomend to add an id attribute to the *script* tag, well this id can help you in the task of creating the custom event.

For example if your id is "tok3nscripttag" you can use:

```javascript
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
{"Valid":"YES","CertKey":"7448aa2e-3463-53b8-5245-5b594be5b129","PublicKey":"9765601a-433b-5e1b-7a65-c07c555afe9d","UserKey":"37912139-5d4a-5c7a-5e93-f8db229e64a7","TransactionId":"ba4851d2-37dc-5292-626b-1beb87e3728d","Hash":"48 c9 63 52 ce e0 bd 8b 44 de d6 81 fc e7 f1 f7 70 f8 b9 53 c2 c8 9a fe d0 9f 0b f8 6b fc aa 93","Result":""}
```

