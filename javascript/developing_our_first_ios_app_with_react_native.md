# Developing our first iOS App with React Native

[Original URL](http://code.hireart.com/2016/02/24/react-native-ios-app/)

> When we started talking about creating a mobile app for our candidates, we were pretty clueless about what it would actually take to build a mobile app. We knew generally that we wanted something...

When we started talking about creating a mobile app for our candidates, we were pretty clueless about what it would actually take to build a mobile app. We knew generally that we wanted something cool, fun to use, and really slick, but none of us had ever built a mobile app before.

We jumped on the React Native train and we're glad we did. These are some of the things we thought about when we made the decision and some of the things we learned along the way.

## Reasons to use React Native

We're web developers, not iOS developers. I've been to a few meetups in NYC about how cool Swift is and how Objective C is still relevant, but I'm most comfortable writing Ruby and Javascript. Our team had started using Facebook's React in early 2015 and initial react(ion) was overwhelmingly positive. When React Native came on the scene, we were initially skeptical (cross device platforms can be problematic). But the more we looked into it, the more we liked it. These are the most compelling reasons for us:

1. **Learn once, write anywhere** Cross-device platforms are generally bad. They usually satisfy the lowest common denominator and the result is almost always sub-optimal. React Native uses the same React.js framework, but writing for Android vs iOS are actually different projects with some shared code. That means code for each device can be written specifically for that device using entirely native(surprise!) components.

![](http://code.hireart.com/images/20160224/platform-code-b8f9d64a.png)

1. **Declarative views** We fell in love with React's declarative views when we used them for the web, being able to use declarative views on iOS development was a huge plus for us as it meant more predictable code and less bugginess.<br>
  <https://en.wikipedia.org/wiki/Reactive_programming>

2. **Flexbox for mobile** We didn't know for sure how the constraints solver works for iOS, but we weren't sure we wanted to go down that road. Lucky for us, React Native uses flexbox, which by now is supported by _almost_ every browser and makes layouts much more intuitive. (in our humble opinion) <https://css-tricks.com/snippets/css/a-guide-to-flexbox/>

![ios constraint](http://www.techotopia.com/images/3/3d/Ios_8_greater_or_less_constraint.png "iOS constraint solver")

iOS constraints

![flexbox](https://static.bocoup.com/blog/flex-pack.svg "flexbox")

flexbox

1. **Javascript** This falls into the category of what we don't know (Objective C and Swift). To state the obvious, not having to learn another language really cuts down on development time! Also, we were excited about using ES6, which react-native supports out of the box. We were avid users of coffeescript and transitioning to ES6 was easy. <https://robots.thoughtbot.com/replace-coffeescript-with-es6>

## Reservations about using React Native

We knew from the start that initial gains in development time might be offset by limited customizability and dependency on the existence of React Native Component. Here are some reasons we were hesitant about using React Native

1. **Limited to React Native Ecosystem** When we initially looked at React Native in September of 2015, we saw that a lot of the iOS native elements had already been implemented and more would be on the way. However, we were still worried that a particular component we need won't be available in React Native. And later we did find that we had to build out our own React Native Bridge for certain SDKs (AWS and Mixpanel).

2. **Bleeding Edge** Updates to React Native happen quickly and code which worked on a previous build may (on rare occasions) become obsolete. We encountered this a few times during our development cycle and tried to mitigate its effects by upgrading intentionally only when we need a new feature followed by rigorous testing.

3. **Ungoogleable** Writing code for a new system often means encountering errors no one else has seen before. The number of 'ungoogleable' problems were significantly higher than we had ever encountered with Ruby on Rails. Oftentimes finding the solution required tenacity: referencing documentation and digging through source code (like the good old days). However, much credit goes to the React Native team and its ecosystem for regular updates that address unexpected behavior.

## React Native Packages

It turned out that component support for react-native was exceptional. Everything from ActivityIndicator (the spinner) to Alerts to Sliders worked seamlessly via Javascript. It's hard to emphasize just how effortless it was to create a native iOS app despite not knowing Objective C or Swift. Furthermore, new packages were becoming available everyday via npm. These are a few of our favorites in no particular order:

- **react-native-simple-store** <https://www.npmjs.com/package/react-native-simple-store> We started out using AsyncStorage, but found it tedious to build the same save and get functions over and over again. Simple Store was a great solution build on top of AsyncStorage that allowed us to access the device storage _simply_.

<!--  -->

```
Storeget'user'thenuser 
 // some code
}).catcherror 
 consolewarnerror
}).done
```

- **react-native-vector-icons** <https://www.npmjs.com/package/react-native-vector-icons> This is the best vector icon package we found, we used a combination of FontAwesome and EvilIcons and it worked pretty flawlessly. MaterialIcons, IonIcons, and others are also available via this package.

<!--  -->

```
<Icon name='trophy' />
<EvilIcon name='check' />

var Person struct
 name String // a required string
 surname maybeString // an optional string
 age Number // a required number
 rememberMe Boolean // a boolean
});

Actionsdashboard
```

## Weirdness

Coming from web development, we've encountered a few head-scratchers that were initially difficult to wrap our heads around. Here are some of them.

- **Styling** Not that it isn't possible to create a stylesheet for RN, it's that styles are not cascading. This makes defining styles just a big weird. We solved it with style definitions on several levels with inline over-rides.

Styles.js

```
moduleexports StyleSheetcreate
 title 
 fontSize 
 textAlign 'center'
 color blueText
 fontFamily 'Avenir'
 fontWeight '700'

 header 
 padding 
 paddingTop 
 backgroundColor '#fff'
 borderBottomWidth 
 borderBottomColor '#ccc'
```

Component.js

```
...
<Text style={[Styles.header, {color: 'white'}]}>
 Some text
</Text>
...
```

Standard style would look like this:

```
style={Styles.header}
```

Custom inline style looks like this:

```
style={{color: 'white'}}
```

This is how we merge the two:

```
style={[Styles.header, {color: 'white'}]}
```

It feels hacky and bad... There's probably a better way to do this, but it wasn't obvious to us when we were figuring this out.

- **Circular Requires/Navigation**

This is a big no no (most circular things are bad unless it's recursion, in which case it's not circular just recursive). This can happen by accident if using NavigatorIOS. Routing for web is single-stage. You know where you are and probably where you came from, that's about it. On iOS, views are pushed and popped onto a stack. Try to push a previously pushed component will result in an error. Attempting to simply require a component that might end up pushing the current component will cause a circular require and break. Using _react-native-router-flux_ solved this problem for us and helped us reason about our routes in a much more scalable way.

## Overall

React Native is a great way to build a mobile app. Not knowing Swift or Objective C makes it impossible to make a direct comparison but the experience is definitely a positive one. Even though RN is not without its nuances, it comes with the benefit of building a truly native app. We're glad that we used RN to build out our first mobile app. React Native continues to get better and the ability to quickly context switch from web to mobile for our small nimble dev team is an incredible thing.

In the next few posts, I will go more in depth regarding how we solved particular problems.

- Login and Authentication
- API and callbacks
- iOS SDKs using RNBridge

![](http://code.hireart.com/images/tom-profile-effe20d7.png)

Tom Tang leads mobile development at HireArt. Feel free to reach out: [tom@hireart.com](mailto:tom@hireart.com)
