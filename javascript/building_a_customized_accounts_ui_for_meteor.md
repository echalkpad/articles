# Building a customized accounts ui for Meteor

[Original URL](http://blog.benmcmahen.com/post/41741539120/building-a-customized-accounts-ui-for-meteor)

> This is the third article in a series aimed at explaining how I built fiddleware Subtitles. You can also learn how to build the timeline using d3 and SVG, and how to embed local videos into your web...

This is the third article in a series aimed at explaining how I built fiddleware Subtitles. You can also learn how to [build the timeline](http://blog.benmcmahen.com/post/41124327100/using-d3-and-meteor-to-generate-scalable-vector) using d3 and SVG, and how to [embed local videos](http://blog.benmcmahen.com/post/41058353353/strategies-for-using-local-files-in-your-web) into your web application.

The Meteor framework comes with a handy accounts system, including the Accounts-UI package, which makes it incredibly easy to add an accounts system (including login, registration, and password recovery) to your preexisting web app. It's especially useful for prototyping, when you don't particularly care about how it works, or integrates into your website. But you'll probably find that, eventually, you'll want a bit more flexibility with your login system. Some of this can be achieved by using the `Accounts-UI-Unstyled` package, which allows you to fully stylize the accounts ui to match your other styles. But anything beyond that -- say, building a more traditional login system, like that found on Twitter or Facebook -- requires building your own accounts ui. Thankfully, Meteor's accounts api makes it suprisingly easy to hook your custom templates into the provided [accounts functions](http://docs.meteor.com/#accounts_api). This article will provide a basic introduction to building a traditional login/password based accounts ui for Meteor, like that found on [fiddleware Subtitles](http://subtitles.fiddleware.com).

## Installing the necessary packages

To start with, you need to add the appropriate accounts packages to your meteor project. For this example, I'll be using the standard password login system.

```
 meteor add accounts-base
 meteor add accounts-password
```

## Registration and login forms

Next, we will want to build our login and registration form templates. These are just standard forms with the appropriate fields, which will be cached, trimmed, validated, and passed to the appropriate `Accounts` function.

```
 <form id="login-form" action="action">
 <div>
 <input type="email" id="login-email" />
 <input type="password" id="login-password" />
 <input type="submit" id="login-button" value="Sign in" />
 </div>
 </form> 
```

Then we will setup an event listener for the form submission. You should listen to the form's `submit` event, rather than the submit button's `click` event to ensure proper form behaviour (For example, allowing the submission of the form using the `enter` key).

```
 Template.login.events({

 'submit #login-form' : function(e, t){
 e.preventDefault();
 // retrieve the input field values
 var email = t.find('#login-email').value
 , password = t.find('#login-password').value;

 // Trim and validate your fields here.... 

 // If validation passes, supply the appropriate fields to the
 // Meteor.loginWithPassword() function.
 Meteor.loginWithPassword(email, password, function(err){
 if (err)
 // The user might not have been found, or their passwword
 // could be incorrect. Inform the user that their
 // login attempt has failed. 
 else
 // The user has been logged in.
 });
 return false; 
 }
 });
```

These are the absolute basics of building your own login form. As you can see, it's incredibly easy. You listen for the form submission, retrieve the input values, trim and validate the fields, and supply these values to the `Meteor.loginWithPassword()` function. The function does it's thing, and either performs the login request or calls back with an error. If there is an error, you should inform the user. Finally, you need to `return false` to prevent the form submission from reloading the page.

Our registration acts like you might expect. We create our template.

```
 <form id="register-form" action="action">
 <div>
 <input type="email" id="account-email" />
 <input type="password" id="account-password" />
 <input type="submit" id="create-account" value="Create an Account" />
 </div>
 </form>
```

Again, we listen for the form submission, retrieve the input values, and then pass them to `Accounts.createUser()` function.

```
 Template.register.events({
 'submit #register-form' : function(e, t) {
 e.preventDefault();
 var email = t.find('#account-email').value
 , password = t.find('#account-password').value;

 // Trim and validate the input

 Accounts.createUser({email: email, password : password}, function(err){
 if (err) {
 // Inform the user that account creation failed
 } else {
 // Success. Account has been created and the user
 // has logged in successfully. 
 }

 });

 return false;
 }
 });
```

This provides the user with the basic means of creating an account and logging in.

## Validations

When accepting user input, it's important to do client-side validation to immediately inform the user if the values they have entered are invalid in some way. You'll most likely want to trim whitespace, too, from the email field (but not the password field). To trim whitespace from the email field, you can setup a simple helper function:

```
 // trim helper
 var trimInput = function(val) {
 return val.replace(/^\s*|\s*$/g, "");
 }
```

And then invoke it using the `email field` value.

```
 var email = trimInput(email);
```

For validation, you can create functions that determine if the value is a proper email, isn't empty, or is long enough. For example, here's a very basic function that checks to see if the password is 6 characters or longer. If the password is at least 6 characters long, return true. Otherwise, return false.

```
 var isValidPassword = function(val) {
 return val.length >= 6 ? true : false; 
 }
```

Then, before using the `Meteor.createUser()` function, you'll want to validate the field first. Let's ensure that the user has entered a 6 character or longer password.

```
 if (isValidPassword(userPassword) // && other validations) {
 // Then use the Meteor.createUser() function
 }
```

## Error Handling

Of course, it's not enough to simply prevent `Meteor.createUser()` from being called if validation fails. You need a way to **inform** the user when and why form submission fails. For this, I use [ui kit's notification module](http://visionmedia.github.com/uikit/) combined with a `Session` variable within a reactive context to alert the user when one of the validations fail. This approach is limited, but good enough for my purposes. You may want to implement something a bit more advanced that displays multiple error messages next to the fields instead.

```
 Meteor.autorun(function() {
 // Whenever this session variable changes, run this function.
 var message = Session.get('displayMessage');
 if (message) {
 var stringArray = message.split('&');
 ui.notify(stringArray[0], stringArray[1])
 .effect('slide')
 .closable();

 Session.set('displayMessage', null);
 }
 });
```

Then, if validation fails, within each validation function I set the `displayMessage` session variable.

```
 var isValidPassword = function(val, field) {
 if (val.length >= 6) {
 return true;
 } else {
 Session.set('displayMessage', 'Error & Too short.')
 return false; 
 }
 }
```

Since `Session.get('displayMessage')` is set within a reactive context, the notification is displayed any time the session variable changes. This displays the error message in a notification that appears in the top right of browser window.

## Password Recovery

Lastly, we need to implement the password recovery system. To do this, we need to create a template that has two primary functions: (1) If the user is requesting a reset password email, then we need a form for them to enter their email address. Upon submission (with a validated email address) the user will receive an email with a password reset URL which contains a password reset token. (2) If the user is entering our page after clicking this link, we need to supply them with a means of entering a new password. This requires a different form.

```
 {{#if resetPassword}}
 <form id="new-password" action="action">
 <div>
 <input type="text" id="new-password-password" />
 <input type="submit" value="Change Password" />
 </div>
 </form>
 {{else}}
 <form id="recovery-form" action="action">
 <div>
 <input type="text" id="recovery-email" /> 
 <input type="submit" value="Send Reset Instructions" />
 </div>
 </form>
 {{/if}}
```

If a `resetPassword` token is present in the URL, then we will display the `new-password` form. Otherwise, we will display the `recovery-email` form.

```
 Template.passwordRecovery.helpers({
 resetPassword : function(t) {
 return Session.get('resetPassword');
 }
 });
```

We need to determine if the `resetPassword` token is present in the URL, and if it is, we need to set the Session variable with that token. This will render the appropriate form, and allow us to pass this token to the `Accounts.resetPassword` function. The Accounts package stores the reset token in `Accounts._resetPasswordToken`.

```
 if (Accounts._resetPasswordToken) {
 Session.set('resetPassword', Accounts._resetPasswordToken);
 } 
```

And finally, we need to setup our event listeners to process both forms. The pattern is similar to our login and registration forms.

```
 Template.passwordRecovery.events({

 'submit #recovery-form' : function(e, t) {
 e.preventDefault()
 var email = trimInput(t.find('#recovery-email').value)

 if (isNotEmpty(email) && isEmail(email)) {
 Session.set('loading', true);
 Accounts.forgotPassword({email: email}, function(err){
 if (err)
 Session.set('displayMessage', 'Password Reset Error & Doh')
 else {
 Session.set('displayMessage', 'Email Sent & Please check your email.')
 }
 Session.set('loading', false);
 });
 }
 return false; 
 },

 'submit #new-password' : function(e, t) {
 e.preventDefault();
 var pw = t.find('#new-password-password').value;
 if (isNotEmpty(pw) && isValidPassword(pw)) {
 Session.set('loading', true);
 Accounts.resetPassword(Session.get('resetPassword'), pw, function(err){
 if (err)
 Session.set('displayMessage', 'Password Reset Error & Sorry');
 else {
 Session.set('resetPassword', null);
 }
 Session.set('loading', false);
 });
 }
 return false; 
 }
 });
```

And there you have your basic password recovery functionality. You'll notice that I have a `Session.set('loading', true)` variable. This renders a canvas loading animation to indicate that the we are communicating with the server. When the server responds, we set the loading variable to `false`. You can use this pattern in your other accounts forms, or any time you make a call using a `Meteor.method()`.

## Conclusion & Resources

These are the absolute basics of creating a customized accounts ui for your Meteor application. You can see the source for fiddlware Subtitles [here](https://github.com/bmcmahen/Subtitles/blob/master/client/scripts/login.js). It's also worth checking out the [Accounts-UI](https://github.com/meteor/meteor/tree/master/packages/accounts-ui-unstyled) code to see how the Accounts-UI package is built. And, of course, you should read the excellent [Meteor docs](http://docs.meteor.com/#accounts_api) for a detailed description of the functions at your disposal.

[Tweet](https://twitter.com/share)
