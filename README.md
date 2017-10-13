# studious-fantasia

> VueJs app to help research fantasy football players.

## Demo

https://studious-fantasia.herokuapp.com/

## Build Setup

You will need to publish one of your Google Sheets document tabs. [Here](https://support.google.com/docs/answer/37579?co=GENIE.Platform%3DDesktop&hl=en) are instructions on doing so.

Once you have the url, you must copy over `config.example.js` to `config.js` and add your url.

```javascript
export default {
    'sheets_url': '[Insert Google Sheets Published Url here]'
}
```

Now you are ready to start building away!

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).
