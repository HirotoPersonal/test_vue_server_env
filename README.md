## root
```
npx husky install
npx husky add .husky/pre-commit "npm test"
```
- edit precommit as follows:
```Shell
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

cd client
npm run lint-client

cd .. && cd server
npm run lint-server
```
- add .eslintignore and .prettierignore with following:
```
build
node_modules
.github
```

## client
- install with eslint using vue-cli
- install husky, lint-staged
- add following to package.json
```json
// scripts
"prepare": "cd .. && husky install .husky",
"lint-client": "lint-staged"

// after scripts
"lint-staged": {
  "*.{js,vue}": [
    "eslint --fix"
  ],
  "*.{json,html,js,vue}": [
    "prettier --write"
  ]
},
```
```
// eslintrc.js
rules: {
  ...
  "prettier/prettier": ["error", { endOfLine: "auto" }],
}
```

## server
- install eslint and prettier
- same as client
- https://blog.bitsrc.io/how-to-set-up-node-js-application-with-eslint-and-prettier-b1b7994db69f
