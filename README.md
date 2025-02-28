# 💸 Expo + Next.js Monorepo 

Minimal monorepo with Expo/React Native Web + Next.js + TypeScript. Uses yarn workspaces.

# Folders

- `packages/components` (import as `@shared/components`)
- `packages/expo-app` (this is the app you run)

# Start app

- Clone the repo
- Run `yarn install`
- `yarn native` to run the expo app, `yarn web` to run next.js

# Edit code

Open `packages/components` and start editing. Watch changes happen instantly in your app.

# How do I do this? 🐻 🧐

Create a repo. Add a `package.json` with `workspaces`. Run `yarn add -D expo-yarn-workspaces` inside your root folder.

```json
// package.json
{
  "name": "root",
  "private": true,
  "devDependencies": {
    "expo-yarn-workspaces": "^1.2.1"
  },
  "workspaces": ["packages/*"]
}
```

Create `packages` folder. Then `expo init expo-app` if you don't have an app yet. Then follow the instructions for expo + next.js found [here](https://docs.expo.io/guides/using-nextjs/#add-nextjs-to-expo-projects). 

Add a `name` and `version` to your expo app's `package.json`.

Then follow these instructions: https://www.npmjs.com/package/expo-yarn-workspaces for expo yarn workspaces.

Add a new folder in your `packages` folder called `components` (or whatever you want.) Give it a `package.json` that looks like this:

```json
{
  "name": "@shared/components", // name this whatever you want
  "version": "1.0.0",
  "main": "index.tsx",
  "types": "index.tsx"
}
```

Now we need to add it to next's config.

`yarn add next-transpile-modules next-compose-plugins`

Add `@shared/` to `next.config.js` in your expo app's folder. This makes all folders that start with `@shared/` get transpiled, which is necessary for nextjs.

```js
// packages/expo-app/next.config.js
const { withExpo } = require("@expo/next-adapter");

// 🚨🚨🚨 if you rename @shared/, edit it here!
const withTM = require("next-transpile-modules")(["@shared/components"]);

const withPlugins = require("next-compose-plugins");

module.exports = withPlugins([withTM, [withExpo, { projectRoot: __dirname }]]);
```

Run `yarn install` from the root of your monorepo.

Enjoy your awesome monorepo.

Make sure to add any other folders you make to your `next.config.js` after `next-transpile-modules`.

# Possible Errors

1. Parsing

```sh
Module parse failed: Unexpected token (4:5)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
```

If you see that error, you haven't configured the `next.config.js` properly, see above.

2. Installing packages

Make sure you don't use different react-native/Expo versions in different packages. This will mess shit up.

# Practices

I tend to install 99% of my packages directly in the `expo-app` folder. It usually looks like this:

```sh
# open the app
cd package/expo-app
# add a package
yarn add moti
# go back to the root
cd ../..
# install again
yarn install
```

The nice thing about the monorepo is that you only need each package to be in **one `package.json` file**. You don't need to add a dependency in every `package.json`. So I use my main app as the entry point for basically every dependency.

I also run `yarn install` at the root every time I add a package, since I use a `patch-package` `postinstall` script at the root folder.

# EAS Build

If you're using EAS from Expo, you might need to add packages to your `package.json`'s `expo-yarn-workspaces.symlinks` array.

If you encounter a build error indicating you don't have these, you should add them. Apparently Expo is working on making this step simpler with a single symlink.
