# Angular Template loader for webpack

Puts HTML partials in the Angular's $templateCache so directives can use templates without initial downloading.

## Webpack and loaders

Webpack is the [webpack](http://webpack.github.io/) and it's module bundler. [Loaders](http://webpack.github.io/docs/using-loaders.html) wrap content in the javascript code that executes in the browser. 

# Install

`npm install ng-cache-loader`

# Usage

You can require html partials via `ng-cache-loader`:

``` javascript
require('ng-cache!./demo/template/myPartial.html');
```

Partial will be available as `ng-include="'myPartial.html'"`
or `templateUrl: 'myPartial.html'`.

## Named templates

You can wrap template in the `script` tag:

``` html
<!-- ./demo/template/myPartial.html -->

<script type ="text/ng-template" id="myFirstTemplate">
  <!-- then use ng-include="'myFirstTemplate'" -->
</script>
```

You can have multiple templates in one file:

``` html
<!-- ./demo/template/myPartial.html -->

<script type ="text/ng-template" id="myFirstTemplate">
  <!-- then use ng-include="'myFirstTemplate'" -->
</script>

<script type ="text/ng-template" id="mySecondTemplate">
  <!-- then use ng-include="'mySecondTemplate'" -->
</script>
```

You can mix named templates and simple markup:

``` html
<!-- ./demo/template/myPartial.html -->

<script type ="text/ng-template" id="myFirstTemplate">
  <!-- then use ng-include="'myFirstTemplate'" -->
</script>

<!-- markup outside script tags available as ng-include="'myPartial.html'" -->
<div ng-include="'myFirstTemplate'">...</div>

<script type ="text/ng-template" id="mySecondTemplate">
  <!-- then use ng-include="'mySecondTemplate'" -->
</script>
```

## Prefix

Prefix adds path left of template name:

``` javascript
require('ng-cache?prefix=public/templates!./path/to/myPartial.html')
// => ng-include="'public/templates/myPartial.html'"
```

Prefix can mask the real directory with the explicit value
or retrieve the real directory name (use `*` or `[dir]`):

``` javascript
require('ng-cache?prefix=public/*/templates!./path/to/myPartial.html')
// => ng-include="'public/path/templates/myPartial.html'" 
```

Prefix can strip the real directory name (use `//`):

``` javascript
require('ng-cache?prefix=public/*//*/templates!./far/far/away/path/to/myPartial.html')
// => ng-include="'public/far/path/templates/myPartial.html'" 
```

Prefix can be extended through a directory tree (use `**` or `[dirs]`). See the next section.

## Root

You can specify root directory in prefix. It is enough to specify a single directory name.

``` javascript
require('ng-cache?prefix=packman:**!/User/myself/Projects/packman/path/to/myPartial.html')
// => ng-include="'path/to/myPartial.html'"

require('ng-cache?prefix=packman:**!/User/myself/Projects/packman/far/path/to/myPartial.html')
// => ng-include="'far/path/to/myPartial.html'"
```

You can specify two or three directories to uniquely associate root with place in the file system.

It is also possible to combine wildcards in prefix. For instance `prefix=packman:**/tmpls//*`.


## Module

By default, templates will be added to the default AngularJS 'ng' module run() method. 
Use this parameter to use a different module name:

``` javascript
require('ng-cache?module=moduleName!./path/to/myPartial.html')
```

## webpack config

Match `.html` extension with loader:

``` javascript
module: {
    loaders: [
        {
            test: /\.html$/,
            loader: "ng-cache?prefix=[dir]/[dir]"
        }
    ]
},
```

## URL resolve

Relative links to the local images are resolved by default. 

``` html
<!-- Source -->
<img src="../img/logo.png"></img>

<!-- becomes -->
<img src="data:image/png;base64,..."></img>
```

Use this in conjunction with [url-loader](https://github.com/webpack/url-loader). For instance:

``` javascript
require('url?name=img/[name].[ext]!ng-cache!./templates/myPartial.html')
```

Using webpack config is more convenience:

``` javascript
module: {
    loaders: [
        { test: /\.html$/, loader: "ng-cache?prefix=[dir]/[dir]" },
        { test: /\.png$/, loader: 'url?name=img/[name].[ext]&mimetype=image/png' },
        { test: /\.gif$/, loader: 'url?name=img/[name].[ext]&mimetype=image/gif' }
    ]
},
```

To switch off url resolving use `-url` query param:

``` javascript
require('ng-cache?-url!./myPartial.html')
```

# License

MIT (http://www.opensource.org/licenses/mit-license.php)
