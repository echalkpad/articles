# Jumpsuit -- React/Redux made simple

[Original URL](https://medium.com/@tannerlinsley/jumpsuit-react-redux-made-simple-e3186ba1b077)

> Everyone wants to use React and Redux these days. They are two amazing tools that come together like magic and make building websites and apps that scale a breeze. React is the best user-interface...

Everyone wants to use [React](https://facebook.github.io/react/) and [Redux](http://redux.js.org/) these days. They are two amazing tools that come together like magic and make building websites and apps that scale a breeze. React is the best user-interface creation tool in existence, and Redux is an awesome, no funny-business state-management library built to scale big or go home.

There are countless reasons that people love React + Redux, but we've found that learning how to put two and two together is not an easy task for most, and becoming productive with the combo is just as difficult. Every tutorial and starter-template that we could find seemed to be stuffed full of quick-start-boilerplate that often ends up being more confusing than helpful, and redux implementations that felt way too verbose for what they were accomplishing. So we set out to build a tool that would make all of this easier...

![](images_medium.com/1Ep6KzTpDGjy8t4MSpCZPXA.png)

We built **Jumpsuit!** A paper-thin API that allows us to start projects faster, be more efficient with our code, and help developers harness the power of React and Redux without understanding all of the overhead.

Simply put, Jumpsuit bundles a lot of great existing tools along with some new ideas into one awesome library:

- One dependency to get started. No more setting up and importing 5+ libraries to get going

- Zero boilerplate. You're writing your app from line 1

- A simplified API layer to React and Redux

- An optional, but integrated CLI to start coding even faster.

- HSR, or Hot "State" Reloading ([more details here](https://medium.com/@tannerlinsley/introducing-hsr-the-hot-state-reloader-behind-jumpsuit-js-42498712ac90#.mzbdczl5e))

Jumpsuit is easiest understood in a simple counter example:

It's that easy! And it extremely scalable. In fact, one of our favorite companies called [Nozzle.io](https://nozzle.io) has written their entire front-end application using Jumpsuit through and through. We even have a [Todo List example](https://github.com/jumpsuit/jumpsuit/tree/master/examples/todo), rewritten from the [Redux example](http://redux.js.org/docs/basics/ExampleTodoList.html) to use Jumpsuit.

- We write way less code that is extremely simple to read and understand! Even for new developers who aren't familiar with React or Redux, Jumpsuit has been easy for them to start using immediately.

- Action type constants, action creators, and the dispatcher are no longer needed. To us, they have always felt like unnecessary steps in what should be a very simple and succinct process.

- Jumpsuit covers about 90% of our use cases, but sometimes you still need to use advanced features like middleware, or the redux dispatcher. Just as easily, Jumpsuit can step aside and let's you dig deep into both React and Redux where needed.

- Jumpsuit's CLI is optional, but it packs a punch. It's completely replaced webpack and the accompanying config files for most of us who have tried it.

<!--  -->

```
$ npm install jumpsuit -g
$ jumpsuit new myProjectName && cd myProjectName
$ jumpsuit watch
$ open localhost:8000
```

We really think people will like what we've built because it is helping us build better apps much faster than we imagined, without sacrificing the best battle-tested tools in the community. Let us know what you think! Comment, [submit a PR](https://github.com/jumpsuit/jumpsuit/pull/new/master), or [file an issue](https://github.com/jumpsuit/jumpsuit/issues/new).

Thanks!
