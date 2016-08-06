# React Storybooks meets Create React App -- KADIRA VOICE

[Original URL](https://voice.kadira.io/react-storybooks-meets-create-react-app-ac8a3f32cc79?gi=d688fdb961da)

> npm i -g create-react-app getstorybookcreate-react-app hello-reactcd hello-reactgetstorybooknpm run storybookIf you have been following the React ecosystem recently, you've probably have seen...

![](https://cdn-images-1.medium.com/max/800/1*lR_y48TjywgF-gijEkfGPg.png)

```
npm i -g create-react-app getstorybook
create-react-app hello-react
cd hello-react
getstorybook
npm run storybook
```

If you have been following the React ecosystem recently, you've probably have seen the [Create React App](https://github.com/facebookincubator/create-react-app). It's the easiest way to get started with React. It comes with a default webpack setup, which works for most use cases.

It has support for:

- Importing CSS files

- Importing media files

- ES2016+ syntax

- Eslint support

and much more.

Most importantly, it has a standardized base React build system, which everyone can rally around. Now, using React is no longer fatiguing.

[React Storybook](https://github.com/kadirahq/react-storybook) always had a lot of ways it could be configured to suit your needs. It comes with a very basic webpack config, but you had to add a custom config to get CSS support and other features.

If you know how to configure webpack, that's not a big deal. But still, it makes it a bit hard to get started with React Storybook.

> **BTW: What's Storybook?**<br>
> It's a way to launch a [visual development environment](https://github.com/kadirahq/react-storybook) for your React component. Using it, you can build a component without running your app.

Recently, we've updated React Storybook's default configurations to match with the Create React App's (CRA) webpack setup. So, you can now work with any CRA-based app without worrying about any webpack stuff.

You can simply follow our [getting started guide](https://github.com/kadirahq/react-storybook#getting-started) to add storybook support to a CRA-based app.

Here are the [changes](https://github.com/kadira-samples/hello-react/commit/979201464fe97464690e8cd10db1802b18335d7b) needed to add storybook support to such an app:

With the updated default setup, adding a storybook won't take much time. But it used to take a **few minutes** to add a few files and some NPM scripts.

However, now it takes just a **few seconds**. Here's how:

First install the **getstorybook** npm utility globally:

```
npm i -g getstorybook
```

Then go to your React project and run:

```
getstorybook
```

That's all.<br>
It'll detect the type for your project and it'll add Storybook support.

Now, it's time for you to work on your component and you don't need to go about configuring stuff.
