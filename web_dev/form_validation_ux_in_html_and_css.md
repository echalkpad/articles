
[Original URL](https://css-tricks.com/form-validation-ux-html-css/)

> You can do and impressive amount of form validation with just HTML attributes. You can make the user experience pretty clean and clear with CSS selectors. But it does require some CSS trickery to get...

You can do and impressive amount of form validation with just HTML attributes. You can make the user experience pretty clean and clear with CSS selectors. But it does require some CSS trickery to get everything just right!

## (You can) make the label look like a placeholder

First: **always use real `<label for="correct_input">` elements.** Just that alone is a UX consideration all too many forms fail on. Placeholders are suggestions for valid input, like putting "Tulsa" in an input labeled "City".

I'd say if the form is short and an obvious pattern (like sign up or log in), you could use the placeholder visual pattern, but use real labels instead.

Taking a form like...

```
<form action="#0" method="post">

 <div>
 <input type="text" id="first_name" name="first_name">
 <label for="first_name">First Name</label>
 </div>

 <!-- ... --->

</form>
```

You use the `<div>` as a positioning context and place the label directly over the form.

```
form {
 max-width: 450px;

 // positioning context
 > div {
 position: relative;

 // Looks like placeholder
 > label {
 opacity: 0.3;
 position: absolute;
 top: 22px;
 left: 20px;
 }
 }
}
```

You don't need to do any tricky cursor stuff, because it's all semantically wired up already. If they click the area taken up by the label, it will activate the input. If they click the input, it will activate the input. Both correct.

The trick is putting the input _first_ (semantically fine) so you can hide the label on focus:

```
form {

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old ... */

 > div {
 > input[type="text"],
 > input[type="email"],
 > input[type="password"] {

 &:focus {
 & + label {
 opacity: 0;
 }
 }

 }
 }
}
```

![](https://cdn.css-tricks.com/wp-content/uploads/2016/06/input-placeholder.gif)

## Make certain inputs required

Perhaps the easiest possible validation on a form you can do is use the `required` attribute to require a field. No faster way to catch an error than letting the browser do it if it can!

```
<input required type="text" id="first_name" name="first_name">
```

![](https://cdn.css-tricks.com/wp-content/uploads/2016/06/requried-fields.gif)

## Positively indicate valid input values

Let users know a field has been entered correctly. The browser can give us this information through the `:valid` CSS selector:

```
form {

 > input[type="text"],
 > input[type="email"],
 > input[type="password"] {

 // show success!
 &:valid {
 background: url(images/check.svg);
 background-size: 20px;
 background-repeat: no-repeat;
 background-position: 20px 20px;

 // continue to hide the label
 & + label {
 opacity: 0;
 }
 }
 }
 }
}
```

![](https://cdn.css-tricks.com/wp-content/uploads/2016/06/valid-fields.gif) `:valid`, in this case, is ensuring the required condition is met, but that selector is also useful for validating the type of input.

## Show reminders about validation of type

You can also require an input's value to be of a certain type, like `email` or `number`. [Here's examples](http://codepen.io/joesteinkamp/pen/ECbmu) of all of them.

```
<input type="email" id="email" name="email" required>
```

That input is both required and required to be a valid email address format. Let's do this for the UX:

1. Tell the user about the requirements when the field is focused
2. Remind them the field doesn't have a valid value otherwise

But also... don't show any reminders when the input is empty. As in, don't assume an invalid state. It's just kind of annoying and unnecessarily negative. In order to do that, we'll need to know if the input is empty or not.

### Sub trick! Testing if an input has a value or not

We want to do stuff with `:valid` and `:invalid`, but we don't want to jump the gun with `:invalid` and use it before the input has a value.

Is there a CSS selector to test if an input is empty? Not really! You'd think `:empty` would be it, but it's not. That's for matching things like `<p></p>`... container elements with nothing inside them. Inputs are no-content elements already.

The trick is to make sure the input has a placeholder value, then:

```
input:not(:placeholder-shown) {

}
```

We're not really using placeholder in our demo, but a value of a single space works:

```
<input placeholder=" ">
```

[:placeholder-shown](https://css-tricks.com/almanac/selectors/p/placeholder-shown/) is super useful for us here! It's basically the secret selector for testing if an input currently has a value or not.

There is no IE or Firefox [support](http://caniuse.com/#feat=css-placeholder-shown) though, which is particularly difficult to navigate around. With new features like this, `@supports` is normally a savior, but...

```
/* This doesn't work */
@supports (input:placeholder-shown) {
 input:not(:placeholder-shown) {
 }
}
```

You can't use `@supports` for selectors, only property/values (e.g. `@supports (display: flex)`)

Testing for placeholder support in JavaScript is easy enough:

```
var i = document.createElement('input');
if ('placeholder' in i) {

}
```

But there doesn't appear to be a simple way to test for `:placeholder-shown`. So.... this might just need to wait for deeper browser support to use on big production stuff.

Assuming wonderful support, this is how the logic would play out...

```
form {

 > div {

 > input[type="text"],
 > input[type="email"],
 > input[type="password"] {

 // When input is...
 // 1\. NOT empty
 // 2\. NOT in focus
 // 3\. NOT valid
 &:invalid:not(:focus):not(:placeholder-shown) {
 // Show a light reminder
 background: pink;
 & + label {
 opacity: 0;
 }
 }

 // When that invalid input becomes in focus (and also still isn't empty)
 &:invalid:focus:not(:placeholder-shown) {
 // Show the more robust requirement instructions below.
 & ~ .requirements {
 max-height: 200px;
 padding: 0 30px 20px 50px;
 }
 }

 }

 // <input> ~
 // <label> ~ 
 // <div class="requirements">
 .requirements {
 padding: 0 30px 0 50px;
 color: #999;
 max-height: 0;
 transition: 0.28s;
 overflow: hidden;
 color: red;
 font-style: italic;
 }

 }
}
```

![](https://cdn.css-tricks.com/wp-content/uploads/2016/06/reminders.gif)

## You can create robust validations

It's not just `required` or `type="email"` (and the like), you can also client-side validate things like length (e.g. minimum password length or maximum characters in bio textarea) and even go full-on regex.

Here's [an article](http://www.the-art-of-web.com/javascript/validate-password/) on that. Say you wanted password requirements like

- At least 6 characters
- At least 1 uppercase character
- At least 1 lowercase character
- At least 1 number

You can do that like:

```
<input pattern="(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,}" type="password" id="password" name="password" required placeholder=" ">
```

![](https://cdn.css-tricks.com/wp-content/uploads/2016/06/password.gif)

## Demo

I'm leaving in the `:placeholder-shown` stuff here, which makes this not work great in Firefox and IE. It's just a demo! Feel free to pick and choose stuff from here that works for you.

See the Pen [Form with a bunch of HTML5 Validation and CSS3 Help](http://codepen.io/chriscoyier/pen/JXgKjb/) by Chris Coyier ([@chriscoyier](http://codepen.io/chriscoyier)) on [CodePen](http://codepen.io).

## More
