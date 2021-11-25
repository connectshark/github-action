# 使用github action來實現純前端網頁自動部署至gh-page

[medium好讀版](https://medium.com/nosegates/14c36e5c917c?source=friends_link&sk=e03d4a461fc5b52afcf4e7eaed3490b6)

對於前端工程師來說github上的gh-page是一個很適合放置自己作品的地方
尤其適合想要練練手感的時候可以方便地展示成果的地方

在過去使用手動commit至gh-page的分支上，或是使用deploy.sh檔來部署，對於我這種懶人來說都是多一個步驟，時常會覺得麻煩

現在能夠透過github action的功能實現推上github時自動部署至專案gh-page的分支上，設定完成以後非常適合懶人使用

遵循此篇文章設定即可輕鬆完成設定

## 設定自動部署

### 設定專案部署時的base url

使用此github action會預設部署至`https://你的帳號.github.io/專案名稱/`這個url底下
所以在部署時需要設定專案的base url

以vite部署為例需要在`vite.config.js`中設定
```javascript=
base: process.env.NODE_ENV === 'production'
    ? '/該專案名稱/'
    : '/',
```

### 設定專案`.yaml`檔

前往專案的Action頁面
![Action頁面](https://i.imgur.com/aQS6w3j.png)

你的專案沒有設定過github action所以頁面應該是長得跟我的圖差不多
隨意選一個set up this wordflow的按鈕點下去，別擔心預設的內容我們都不需要
點擊按鈕後github會提供你一個預設的模板內容
![我的圖差不多](https://i.imgur.com/g5Kk1mk.png)


將裡面預設的內容全部清除
將下面的內容貼上

```yaml=
name: Build and Deploy
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v2.3.1

      - name: Install and Build 🔧
        run: |
          npm install
          npm run build
      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@4.1.4
        with:
          branch: gh-pages
          folder: dist

```
檔名看自己要不要修改一下
如果不需要修改的話直接點選右上角的commit按鈕commit上去

### 開啟github page功能

由github action自動推上gh-page分之後github會預設開啟該專案的github page

如果遇到沒有開啟的話可以從專案的setting頁面中選擇左邊的page頁籤進入設定github page的地方
![github page](https://i.imgur.com/O5Bey9P.png)
在這裡選擇gh-page分支後點選save即可手動開啟github page功能

## 在部署時使用環境變數

如果要在專案中使用環境變數需要先設定專案的Actions secrets資料並且稍微修改一下`.yaml`檔的內容

### 設定secrets
前往專案的setting -> secrets頁籤
直接點選New repository secret新增專案的環境變數

![Actions secrets](https://i.imgur.com/I89sZR0.png)

![Actions secrets](https://i.imgur.com/6n7472D.png)
新增的方式很簡單
Name的欄位對應環境變數的key
value的欄位對應value
直接複製貼上即可

到這裡就設定完部署時的環境變數
接著我們要設定`.yaml`的地方引用變數

### 修改`.yaml`檔的內容

將`.yaml`檔的內容修改成以下內容
```yaml=
name: Build and Deploy
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v2.3.1

      - name: Install and Build 🔧
        run: |
          npm install
          npm run build
        env:
          環境變數key : ${{ secrets.環境變數key }}
      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@4.1.4
        with:
          branch: gh-pages
          folder: dist
```
主要是在run底下設定env
設定方式是專案部署的環境變數key對應先前在secrets裡的環境變數key
