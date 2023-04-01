# CI/CD Firebase

## Step
- 1: Open terminal

```bash
curl -sL https://firebase.tools | bash
```

- 2: Create project with doc or cd to your project
- Link: [React Document](https://create-react-app.dev/docs/getting-started)
```bash
cd project_name
```

- 3: innit Firebase to project
```bash
firebase innit
```
- `Choose the option` : Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys
- What do you want to use as your public directory? --> build
- Configure as a single-page app (rewrite all urls to /index.html)? -> yes
- Set up automatic builds and deploys with GitHub? --> No

- 3: Create folder
```bash
mkdir .github && cd .github && mkdir workflows && touch firebase-hosting.yml
```
- Add this script to file `yml`:

```bash
name: Firebase CI

on:
  push:
    # workflow_dispatch:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x]

    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js version ${{ matrix.node-version }}
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install Yarn
        run: npm install -g yarn

      - run: yarn
      - run: npm install -g firebase-tools
      - run: yarn test
      - run: yarn build
      - name: Archive build
        uses: actions/upload-artifact@v2
        with:
          name: build
          path: build

  deploy:
    name: Deploy
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Download build
        uses: actions/download-artifact@v2
        with:
          name: build
          path: build
      - name: Deploy to Firebase
        uses: w9jds/firebase-action@master
        with:
          args: deploy --only hosting
        env:
          FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}

```

- 4: Get firebase token
```bash
firebase login:ci
 ```
 - then copy string behind the test `Success! Use this token to login on a CI server:`

# Thanks you & have a nice day ^^~