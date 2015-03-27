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

