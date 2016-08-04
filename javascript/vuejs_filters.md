# VueJs: Filters

[Original URL](http://coligo.io/vuejs-filters/)

> In this tutorial we take a look filters in VueJs and how we can use them through a number of examples. We explore some of the powerful built-in filters such as orderBy and filterBy and how we can...

In this tutorial we take a look filters in VueJs and how we can use them through a number of examples. We explore some of the powerful built-in filters such as orderBy and filterBy and how we can chain several filters to one another. We then go on to defining our own custom filters and using them throughout our Vue instance.

Be sure to read [VueJs: The Basics](http://coligo.io/vuejs-the-basics/) tutorial before diving into this one to have a solid understanding of the basic concepts in VueJs.

## The Basics of Filters in VueJs

Filters are simply functions that you can pass data into which gets processed and a filtered version of this data is returned. Vue conveniently has a number of built in filters that you can check out in the [filters section of the VueJs guide](http://vuejs.org/api/#Filters). The pipe symbol (`|`) is used to denote a filter like so:

```
{{ 'coligo' | uppercase }}
```

In this case we are using the built-in VueJs filter called **uppercase** and we are passing a string into it, which is transformed into all uppercase characters (COLIGO) and rendered onto the page.

Of course you are not restricted to hardcoding strings. In the example below we bind the user input to the `message` variable and pass it into the uppercase filter. Go ahead and try it for yourself:

## Chaining Filters

VueJs allows you to chain multiple filters to each other, passing the output of one filter to another. Let's take a look at less trivial example in which we use Vue's `filterBy` filter chained to an `orderBy` filter to display all products that belong to the **electronics** category in **alphabetical order** of name.

Let's take a second and break down the demo. We have an array of products that we wish to iterate over and display in a list. This is easily achieved using the `v-for` directive like so:

```
<li v-for="product in products">
 {{ product.name | capitalize }} - {{ product.price | currency }}
</li>
```

We're using the `capitalize` and `currency` filters to display the product name and price in a nicely formatted manner.

To get only the products that belong to the **electronics** category we can use the `filterBy` method on the products array:

```
<li v-for="product in products | filterBy 'electronics' in 'category'">
 {{ product.name | capitalize }} - {{ product.price | currency }}
</li>
```

In the above snippet we are telling the `filterBy` method to look for the _string_ 'electronics' within the 'category' key of the objects in our products array. This will return a filtered array that contains only 'electronics' and no 'accessories'.

In case you're wondering, you can also search across multiple keys in an object:

```
<li v-for="product in products | filterBy 'electronics' in 'category' 'name'">
 {{ product.name | capitalize }} - {{ product.price | currency }}
</li>
```

This would search for the word 'electronics' in both the category and name properties.

The last thing we need to do is order them alphabetically. Vue makes this extremely easy using the `orderBy` filter. We can simply chain this to the output of `filterBy` to sort the filtered array alphabetically:

```
<ul>
 <li v-for="product in products
 | filterBy 'electronics' in 'category'
 | orderBy 'name'"
 >
 {{ product.name | capitalize }} - {{ product.price | currency }}
 </li>
</ul>
```

`filterBy` takes the products array and returns a filtered array that only contains 'electronics'. This new array is then fed into the `orderBy` filter which sorts it alphabetically according to the product name.

The `orderBy` filter in VueJs sorts items in ascending order by default. If you would like to reverse that and have it in _descending_ order instead you can simply pass a `-1` argument to it:

```
<li v-for="product in products
 | filterBy 'electronics' in 'category'
 | orderBy 'name' -1 "
>
```

## Custom Filters

Although VueJs gives us a bunch of handy, built-in filters, you will quickly realize that those filters aren't enough. Thankfully, Vue gives us a clean and elegant way of defining our own filters that we can use with the pipe (`|`) notation.

A global, custom filter is defined using the `Vue.filter()` constructor that takes 2 parameters:

1. **filterID**, which is simply a unique name for your filter
2. **filter function** - a function that takes a value as a parameter and returns the transformed value

Let's go back to our products example:

> Imagine we have a online store and we are having a 50% discount on all our products

To achieve this we can:

- Create a filter called **discount** using the `Vue.filter()` constructor
- Pass in the original price of each product and return the discounted price

<!--  -->

```
Vue.filter('discount', function(value){
 return value articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii 0.5;
});

new Vue({
 el: 'body',
 data: {
 products: [
 {name: 'microphone', price: 25, category: 'electronics'},
 {name: 'laptop case', price: 15, category: 'accessories'},
 {name: 'screen cleaner', price: 17, category: 'accessories'},
 {name: 'laptop charger', price: 70, category: 'electronics'},
 {name: 'mouse', price: 40, category: 'electronics'},
 {name: 'earphones', price: 20, category: 'electronics'},
 {name: 'monitor', price: 120, category: 'electronics'}
 ]
 }
});
```

You can now use the custom filter in the same way you would use any of the built-in VueJs filters:

```
<ul>
 <li v-for="product in products">
 {{ product.name | capitalize }} - {{ product.price | discount | currency }}
 </li>
</ul>
```

This will now apply a 50% discount to all our products and pass the discounted price to the currency filter to maintain the formatting.

So that's great - we were able to define a discount filter to automatically apply a 50% discount. What if we wanted to apply a 25% discount? or a 10% discount?

Since this is a global filter and it can be used by any Vue instance, it doesn't make much sense to hard code the discount percentage because that would mean any Vue instance using this filter is forced to apply a 50% discount.

To avoid this, we can pass the amount of discount as an inline argument to our `discount` filter. We can now define our filter with a **discount** argument:

```
Vue.filter('discount', function(value, discount){
 return value articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii (discount / 100);
});
```

and we can simply pass in the amount of discount like so:

```
<ul>
 <li v-for="product in products">
 {{ product.name | capitalize }} - {{ product.price | discount 25 | currency }}
 </li>
</ul>
```

Here's what the finished product looks like:

Filters can also be defined locally within an instance so that it is not shared with every other Vue instance. It is usually better to define a filter in this way if you don't intend to use it in other instances. To define the `discount` filter as a local one, we can simply add it to the `filters` object within our Vue instance:

```
Vue.filter('discount', function(value, discount){
 return value articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii (discount / 100);
});

new Vue({
 el: 'body',
 data: {
 products: [
 {name: 'microphone', price: 25, category: 'electronics'},
 {name: 'laptop case', price: 15, category: 'accessories'},
 {name: 'screen cleaner', price: 17, category: 'accessories'},
 {name: 'laptop charger', price: 70, category: 'electronics'},
 {name: 'mouse', price: 40, category: 'electronics'},
 {name: 'earphones', price: 20, category: 'electronics'},
 {name: 'monitor', price: 120, category: 'electronics'}
 ]
 },
 filters: {
 discount: function(value, discount){
 return value articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii (discount / 100);
 }
 }
});
```

Now you can use it anywhere within that specific Vue instance in exactly the same way as we did earlier. The only difference is that no other instance has access to this filter.

## Wrapping Up

That's it folks! Filters in VueJs are incredibly simple thanks to the clean and elegant pipe notation and the flexible `Vue.filter()` constructor. Now that you have the main concepts of filters down, you can start building your own custom filters and utilizing them within your projects for cleaner and more maintainable code.

If you haven't checked out the [Components in VueJs](http://coligo.io/vuejs-components/) tutorial yet, I'd encourage you to read it as they are just as easy to use, yet extremely powerful.
