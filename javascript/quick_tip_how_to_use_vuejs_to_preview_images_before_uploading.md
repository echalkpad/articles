# Quick Tip: How to Use VueJS to Preview Images Before Uploading

[Original URL](http://taha-sh.com/blog/quick-tip-how-to-use-vuejs-to-preview-images-before-uploading)

> Let's say, for example, you're building an app where your users can upload images to their profiles. It's likely you want to preview those images before they are uploaded -- it's usually a good...

Let's say, for example, you're building an app where your users can upload images to their profiles. It's likely you want to preview those images before they are uploaded -- it's usually a good way to provide some feedback to the user.

In this short tutorial, I'm going to show you how to set up a simple Vue component that lets you do just that.

Here's a demo of what we're going to build.

## The template

Let's start first by defining the template of our component.

```
<template id="image-input-template">
 <div class="Image-input">
 <div class="Image-input__image-wrapper">
 <i v-show="! imageSrc" class="icon fa fa-picture-o"></i>
 <img v-show="imageSrc" class="Image-input__image" :src="imageSrc">
 </div>

 <div class="Image-input__input-wrapper">
 Choose
 <input @change="previewThumbnail" class="Image-input__input" name="thumbnail" type="file">
 </div>
 </div>
</template>
```

We only care about two things here. The property _imageSrc_, which contains the image's source to be previewed and then uploaded. And the event we registered on the file input _@change="previewThumbnail"_, which gets triggered when the user chooses an image.

The rest is just for styling purposes.

## The component

Here's what our component will look like.

```
Vue.component('image-input', {
 template: '#image-input-template',

 props: [ 'imageSrc' ],

 methods: {
 previewThumbnail: function(event) {
 var input = event.srcElement;

 if (input.files && input.files[0]) {
 var reader = new FileReader();
 var vm = this;

 reader.onload = function(e) {
 vm.imageSrc = e.target.result;
 }

 reader.readAsDataURL(input.files[0]);
 }
 }
 }
});
```

First, we told the component about our template and the property it can accept (_imageSrc_). After that, we implemented our event handler, which uses the _FileReader_ object to access the image data that we want to preview. Basic stuff!

If you wonder why we've defined the _imageSrc_ as a property and not as data, is because in some cases we may need to display previously uploaded images (like when the user is editing his/her profile).

## The CSS

Our component is ready to be used. However, we first need to have some CSS for the styling. Here's what you can use.

```
.Image-input {
 display: flex;
}

.Image-input__image-wrapper {
 flex-basis: 80%;
 height: 150px;
 flex: 2.5;
 border-radius: 1px;
 margin-right: 10px;
 overflow-y: hidden;
 border-radius: 1px;
 background: #eee;
 justify-content: center;
 align-items: center;
 display: flex;
}

.Image-input__image-wrapper > .icon {
 color: #ccc;
 font-size: 50px;
 cursor: default;
}

.Image-input__image {
 max-width: 100%;
 border-radius: 1px;
}

.Image-input__input-wrapper {
 overflow: hidden;
 position: relative;
 background: #eee;
 border-radius: 1px;
 float: left;
 flex: 1;
 display: flex;
 justify-content: center;
 align-items: center;
 color: rgba(0,0,0,0.2);
 transition: 0.4s background;
}

.Image-input__input-wrapper:hover {
 background: #e0e0e0;
}


.Image-input__input {
 cursor: inherit;
 display: block;
 font-size: 999px;
 min-height: 100%;
 opacity: 0;
 position: absolute;
 right: 0;
 text-align: right;
 top: 0;
 cursor: pointer;
}
```

The image wrapper will show some icon when it has no image to show. We've added that already, but we haven't yet included the CSS library that provides it. In this case, we're using the _Font Awesome library_, so include this in the <head> section.

```
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.5.0/css/font-awesome.min.css">
```

## Testing it

Now it's time to test our component. All you have to do is to put this anywhere you want.

```
// image-src is the default image to show
<image-input image-src=""></image-input>
```

Also, make sure you have instantiated the root Vue instance.

```
new Vue({
 el: 'body'
});
```

That's it! Now you have a fully-reusable image input that you can use anywhere in your projects.
