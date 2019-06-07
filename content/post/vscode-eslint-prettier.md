+++
categories = ["Javascript"]
description = "How to setup ESLint with Prettier in VSCode for React Project"
keywords = ["prettier", "ESLint", "VSCode", "JavaScript", "Create React App", "React"]
featured = "js.jpg"
featuredpath = "/img"
title = "How to Setup ESLint and Prettier in VSCode For React Project"
date = 2018-06-10T21:42:40+03:00
+++

1. Create a React App: `npx create-react-app my-app`

2. Install `ESLint` and `Prettier` in VSCode.

3. In the project directory, run `npm i prettier eslint-config-prettier eslint-plugin-prettier -D`

4. In User Settings (`ctrl + ,`), put in the following:

{{< highlight javascript>}}

{
    "editor.formatOnSave": true,
    "[javascript]": {
        "editor.formatOnSave": false
    },
    "eslint.autoFixOnSave": true,
    "eslint.alwaysShowStatus": true,
    "prettier.disableLanguages": [
        "js"
    ],
    "files.autoSave": "onFocusChange"
}

{{< /highlight >}}

5. Create an `.eslintrc` file:

{{< highlight javascript>}}

{
  "extends": ["react-app", "plugin:prettier/recommended"]
}

{{< /highlight >}}

6. If your project is not under git, run `git init`.

7. Run `npm i husky lint-staged -D` and `npm i pretty-quick -D`.

8. In `package.json`, add `"precommit": "pretty-quick --staged"` to `scripts`.
