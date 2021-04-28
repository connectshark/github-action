# github-action

vue2 project

## .yml內容

```
name: GitHub Actions Build and Deploy Demo
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v1

    - name: Build and Deploy
      uses: JamesIves/github-pages-deploy-action@releases/v2
      env:
        ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
        BRANCH: gh-pages
        FOLDER: dist
        BUILD_SCRIPT: npm install && npm run build
```