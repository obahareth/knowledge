# Webpack

### Tips

#### Be very careful with importing large libraries

Trying to compile large libraries (like react-plotly or PDF libraries) can take your Webpack compile from seconds to 10 minutes+. If a package is slowing down your compile, consider using a CDN version. We simply used script tags, but there are Webpack plugins that can help with that too:

* [webpack-cdn-plugin](https://www.npmjs.com/package/webpack-cdn-plugin).
* [dynamic-cdn-webpack-plugin](https://www.npmjs.com/package/dynamic-cdn-webpack-plugin).

#### Try to find a webpack plugin for your dependencies

Just importing packages like [moment.js](https://momentjs.com/) or [lodash](https://lodash.com/) bring in a lot of bloat that you probably don’t need. Try to import what you need only, or better yet find a webpack plugin that removes the unused things from your bundle, because [selective imports don’t always work](https://github.com/react-bootstrap/react-bootstrap/issues/2683). As one example, there’s [a webpack plugin](https://github.com/iamakulov/moment-locales-webpack-plugin) that removes a lot of the unnecessary bloat added by Moment.js.

Google actually has a [nice repository](https://github.com/GoogleChromeLabs/webpack-libs-optimizations) listing some common problematic dependencies.

#### Inspect your bundle with Webpack bundle analyzer

![](https://cdn-images-1.medium.com/max/800/0\*TewqripGyXujWGJs.png)

[Webpack Bundle Analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer) is extremely helpful to see what exactly is going into your bundle. In the screenshot above, you’ll notice that moment.js has lots of localization files that your app probably doesn’t need. Webpack Bundle Analyzer can help you easily spot these issues.

#### Add es-check to your CI pipeline early on

[es-check](https://github.com/dollarshaveclub/es-check) will help you find out which ES version your bundle is using, it’s super useful to find out if you’re somehow suddenly not producing ES5 anymore. Even if you’re using Babel and browserslist, you might be importing a node module that’s not even meant to be used in browsers, or even a package that’s not being distributed as ES5. Add es-check to your continuous integration pipeline early on and it should help you find out if your bundle ever stops working with ES5, and that’ll help you find which package is the culprit so you can then transpile it.

#### Transpiling a node\_module

We had imported a very simple package called [hex-rgb](https://github.com/sindresorhus/hex-rgb) that’s not even meant for browsers and this tiny package made our bundle not ES5-compatible anymore. Such packages should go through Babel and be transpiled.

In your webpack config, your babel loader’s exclude field probably looks like this: `/node_modules/` . We need to make a regex that excludes node\_modules except the specific ones that should be transpiled:

```
// Exclude all node modules except hex-rgb and another-package
/node_modules\/(?![hex\-rgb|another\-package])/
```

And once again, this might not be a good solution for large packages as it can drastically slow your build time and you might want to switch to a CDN version instead.

Follow [this issue](https://github.com/babel/babel-loader/issues/171) from the babel-loader repo to stay up to date on how to handle cases like this.

#### Use Browserslist to specify your target browsers

[Browserslist](https://github.com/browserslist/browserslist) lets you specify which browsers to transpile for.

```
> 1%
ie >= 8
```

This simple configuration targets browsers with usage more than 1% global usage, and IE versions 8 and above.

#### Use babel.config.js over .babelrc (for Babel ≥ 7.0)

Favor using `babel.config.js` to [configure Babel](https://babeljs.io/docs/en/configuration) over `.babelrc` . If you want to transpile `node_modules` (which is now becoming a very common case with webapps), then you should use `babel.config.js` .

`.babelrc` can be overridden by another `.babelrc` belonging to a node\_module that you’re transpiling and that can lead to all sorts of weird issues.

#### Make your webpack-dev-server logging output friendlier

1. Change your [webpack-dev-server config](https://webpack.js.org/configuration/dev-server/) to this

```
devServer: {
  noInfo: true,
  stats: 'minimal'
}
```

2\. Add [WebpackBar](https://github.com/nuxt/webpackbar) to get much less-verbose, friendlier, and more concise output.

![](https://cdn-images-1.medium.com/max/800/1\*3CqzwcXgMpT-42e22OMu7Q.png)

Note: The first configuration is meant to be combined with Webpack Bundle Analyzer, as it suppresses console output for things related to your bundle that Webpack Bundle Analyzer already shows. If you’re not using Webpack Bundle Analyzer, don’t apply the first step.

#### &#x20;
