+++
categories = ["JavaScript"]
description = "How to setup ESLint with Prettier in VSCode for React Project"
keywords = ["prettier", "ESLint", "VSCode", "JavaScript", "Create React App", "React"]
featured = "js.jpg"
featuredpath = "/img"
title = "How to Setup ESLint and Prettier in VSCode For React Project"
date = 2018-06-10T21:42:40+03:00
+++

1. Install ESLint and Prettier in VSCode.

2. In the project directory, run `npm i prettier eslint-config-prettier eslint-plugin-prettier -D`

3. In User Settings (`ctrl + ,`), put in the following:

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

4. Create an `.eslintrc` file:

{{< highlight javascript>}}

{
  "extends": ["react-app", "plugin:prettier/recommended"]
}

{{< /highlight >}}

5. If your project is not under git, run `git init`.

6. Run `npm i husky lint-staged -D` and `npm i pretty-quick -D`.

7. In `package.json`, add `"precommit": "pretty-quick --staged"` to `scripts`.
