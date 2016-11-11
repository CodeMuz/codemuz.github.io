---
layout: post
published: true
title: Additional Webpack Use Cases
---
## Using Webpack for CSS

If you've nto yet used Webpack for all your CSS (https://webpack.github.io/docs/stylesheets.html). Then maybe there are ways you can bring post processing into your build step without too much effort. For example finding vendor prefixes for all your css can be achieved using (https://github.com/postcss/postcss-loader), but it's not the easiest thing in the world to configure. Why not use the Web Shell plugin on build step to run the annotate after when your javascript application builds?

~~~
var debug = process.env.NODE_ENV !== "production";
var webpack = require('webpack');
var ngAnnotatePlugin = require('ng-annotate-webpack-plugin');
var WebpackShellPlugin = require('webpack-shell-plugin');

module.exports = {
    context: __dirname,
    devtool: debug ? "inline-sourcemap" : null,
    entry: "./main.js",
    output: {
        path: __dirname + "",
        filename: "js/bundle.min.js"
    },
    plugins: debug ? [] : [
        new ngAnnotatePlugin({
            add: true,
            // other ng-annotate options here
        }),
        new webpack.optimize.DedupePlugin(),
        new webpack.optimize.OccurenceOrderPlugin(),
        new webpack.optimize.UglifyJsPlugin({ mangle: true, sourcemap: false }),
        new WebpackShellPlugin({onBuildStart:['postcss --use autoprefixer stylesheets/css/styles.css -d stylesheets/css/'], onBuildEnd:['echo "Webpack End"']})
    ]
};
~~~

Also note here that we are running a ng annotate, which is important when webpack mangles the javascript in production as the angular dependencies assume undeterminable identifiers. See more here https://www.npmjs.com/package/ng-annotate.
