# AngularTailwind

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 11.0.3. This project contains code from the tailwindUI team for test purpose only.

## How to use tailwind in an angular project with Angular CLI 10 to 11 (December 2020)

It was inpired by [this article](https://notiz.dev/blog/angular-10-with-tailwindcss#css-instead-of-scss).
First, Generate your Angular project and go to your favorite code editor:

	ng new app-name
	cd app-name
	code .
**Choose Scss** for your angular project.

Then install the dependencies for Tailwind, Postcss and ngx-build-plus (bug on the angular animations when compiling...) : 

	npm i -D tailwindcss autoprefixer postcss postcss-import postcss-loader postcss-scss
	ng add ngx-build-plus
**ngx-build-plus** is a custom builder for angular compatible with tailwind. The default builder is not. It will automatically update the *build* and *serve* options in *"./angular.json"*.

Now, in the root folder create a **webpack.config.json** file. Inside, copy the following :
```js
module.exports = {
	  module: {
	    rules: [
	      {
	        test: /\.scss$/,
	        loader: 'postcss-loader',
	        options: {
	          postcssOptions: {
	            ident: 'postcss',
	            syntax: 'postcss-scss',
	            plugins: [
	              require('postcss-import'),
	              require('tailwindcss'),
	              require('autoprefixer'),
	            ],
	          },
	        },
	      },
	    ],
	  },
	};
```
Then go to your **./angular.json** config file and modify as follows (the builder in both case should already be correct, if not, modify) :

```diff
"architect": {
  "build": {
-   "builder": "@angular-devkit/build-angular:browser",
+   "builder": "ngx-build-plus:browser",
    "options": {
+     "extraWebpackConfig": "webpack.config.js",
      ...
    }
    ...
  },
  "serve": {
-   "builder": "@angular-devkit/build-angular:dev-server",
+   "builder": "ngx-build-plus:dev-server",
    "options": {
+     "extraWebpackConfig": "webpack.config.js",
      ...
    }
    ...
  },
  "test": {
-   "builder": "@angular-devkit/build-angular:karma",
+   "builder": "ngx-build-plus:karma",
    "options": {
+     "extraWebpackConfig": "webpack.config.js",
      ...
    }
    ...
  },
```
You're almost done, just type this in the console `npx tailwind init` and head to the global styling file **./src/app/style.scss** and import the tailwind dependencies :
```scss
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';
```
## Tailwind Purge with Angular
For purge, like with other node frameworks, specify your differents file where you use tailwind in a vector :
```js
module.exports = {
  purge: ['./src/**/*.html', './src/**/*.ts'],
  theme: {
    extend: {},
  },
  variants: {},
  plugins: [],
};
``` 
For development, you don't wan't tailwind to purge to every hot reload. The tailwind team thought about it and tailwind will only purge when the environment variable **NODE_ENV** is set to **production** so make sure to specify it.
```bash
# set to prod on linux / WSL
export NODE_ENV=production
# reset to development
export NODE_ENV=development

# on windows powershell
$env:NODE_ENV="production"
# reset to development
$env:NODE_ENV="development"
```
Then when running `ng build --prod`you will get your production-ready webapp with a purged version of tailwind.

## Test on your local machine
Go to your console and type :
```bash
git clone https://github.com/maxencerb/angular11_tailwindcss.git
cd angular11_tailwindcss
npm i
ng serve --open
```
The development serve is on `http://localhost:4200/` .

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.
