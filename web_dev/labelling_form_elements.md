# Labelling Form Elements

[Original URL](http://bitsofco.de/labelling-form-elements/)

> One context in which providing the correct labels for elements can be crucial is in HTML forms. Because users are interacting with forms more directly unlike with some UI elements, it is important...

One context in which providing the correct labels for elements can be crucial is in HTML forms. Because users are interacting with forms more directly unlike with some UI elements, it is important that each form widget - `input`, `textarea`, `select` etc. - is correctly labelled.

Depending on context, there are a number of ways to provide labels for form widgets.

## The `name` and `id` attributes

For most form widgets, it is important to include both `name` and `id` attributes.

- The `name` attribute provides the label the form widget is referenced by when submitted with the form data.
- The `id` attribute is used to reference the form widget when labelling using the `<label for="">` element.

It is helpful to have both these labels be identical, so that the form widget is referenced in the same way on the front end as well as server-side.

```
<input type="text" name="username" id="username"> 
```

Besides the typical form widgets, it is useful to give the `<form>` element itself a unique `name` and/or `id`.

```
<form name=“myForm” id="myForm"> 
 <!-- form widgets -->
</form> 
```

Besides providing a label for the form, it can be used to reference the form and check it's validity.

```
var myForm = $("#myForm");

myForm.validate(); // Check the form values 
myForm.valid(); // Boolean value for if form is valid 
```

## The `<label>` element with the `for` attribute

The `<label>` element (with the `for` attribute) is the most commonly used way of labelling form widgets. Using the `for` attribute, we can reference the ID of the form widget that the label is for.

```
<label for=“username”>Your Username:</label> 
<input type=“text” id=“username” name="username"> 
```

We can also have multiple labels for one form widget.

```
<label for=“pwd”>Your Password:</label> 
<input type=“password” id=“pwd” name="pwd"> 
<label for=“pwd”>Your password must be at least 8 characters long. 
```

## The `<label>` element as a parent

Another way of using the `<label>` element is to simply make it the parent of both the form widget and labelling text. Using this method, we do not need to specify the label's `for` attribute or the widget's `id` attribute.

All text within the element is taken as the label for the form widget.

```
<label> 
 Your Password:
 <input type=“password” name=“pwd”>
 Your password must be at least 8 characters long.
</label> 
```

The `<fieldset>` element is used to group semantically related form widgets, and the `<legend>` element provides a label for the group. One common example of when this is used is when an address is broken up into multiple fields.

```
<fieldset> 
  <legend>You Address:</legend>

  <label>
  Street Address
  <input type=“text” name="street_address">
  </label>

 <label>
  City
  <input type=“text” name="city">
  </label>

 <label>
  State
  <input type=“text” name="state">
  </label>

 <label>
  Country
  <input type=“text” name="country">
  </label>

 <label>
  Postal Code
  <input type=“text” name="postal_code">
  </label>
</fieldset> 
```

Another great use for `<fieldset>` and `<legend>` is for **radio inputs**. Because each radio input has its own `<label>` element, we can't use another `<label>` to group together the group of radio inputs.

As a solution, we can use the `<fieldset>` element to group all the radio inputs together, and the `<legend>` element as a label for the group.

```
<fieldset> 
  <legend>Have you read the terms and conditions?</legend>

  <label>
  Yes
  <input type="radio" name="terms" value="yes">
  </label>

  <label>
  No
  <input type="radio" name="terms" value="no">
  </label>
</fieldset> 
```

Using this method for radio/checkbox inputs is also great because some screen readers read the text within the legend before the label for each widget.

In the example above, a screen reader may read aloud "Have you read the terms and conditions? Yes" and "Have you read the terms and conditions? No" for each radio input.

## The `label` attribute for `<optgroup>`

The `<optgroup>` element may be the only form element that using a `<label>` or `<fieldset>`+ `<legend>` elements is not a practical way to label it.

Because of this, we have available the `label` attribute to provide a label for it.

```
<label for="colours">Choose a colour</label>

<select id=“colours” name=“colours”> 
 <option>Black</option>
 <option>White</option>

 <optgroup label=“Primary”>
  <option>Red</option>
 <option>Yellow</option>
 <option>Blue</option>
 </optgroup>

 <optgroup label=“Secondary”>
  <option>Green</option>
 <option>Purple</option>
 <option>Orange</option>
 </optgroup>
</select> 
```

## The `aria-*` attributes

For cases where, because of the structure of our HTML, we cannot use any of these typical methods, we can use one of the ARIA labelling attributes.

Use `aria-labelledby` to reference the ID of the labelling element -

```
<p id="username-label">Your Username</p> 
<div> 
 <input type="text" name="username" aria-labelledby="username-label">
</div> 
```

Use `aria-label` to provide the label text itself -

```
<input type="text" name="username" aria-label="Your Username"> 
```

Use `aria-describedby` to reference the ID of an element that describes the form widget -

```
<p id="username-label">Username</p> 
<p id="username-description">Your username must be at least 4 characters long.</p> 
<div> 
 <input type="text" name="username" aria-labelledby="username-label" aria-describedby="username-description">
</div> 
```

You can read more about using these ARIA attributes in my article on [HTML For Screen Readers](http://bitsofco.de/html-for-screen-readers-labelling-elements/)
