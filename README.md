# PostCSS Custom Selectors [![Build Status](https://travis-ci.org/postcss/postcss-custom-selector.svg)](https://travis-ci.org/postcss/postcss-custom-selector)

> [PostCSS](https://github.com/postcss/postcss) plugin to transform [W3C CSS Extensions(Custom Selectors)](http://dev.w3.org/csswg/css-extensions/#custom-selectors) to more compatible CSS.

## Install

```console
$ npm install postcss-custom-selectors
```

## Usage

```js
// dependencies
var fs = require('fs')
var postcss = require('postcss')
var selector = require('postcss-custom-selectors')

// css to be processed
var css = fs.readFileSync('input.css', 'utf8')

// process css using postcss-custom-selectors
var output = postcss()
  .use(selector())
  .process(css)
  .css

console.log('\n\n ====>Output CSS:\n',output)  
```

Or just：

```js
var output = postcss(selector())
  .process(css)
  .css
```

input.css：

```css
@custom-selector --heading h1, h2, h3, h4, h5, h6;

article --heading + p{
  margin-top: 0;
}
```

output

```css
article h1 + p, article h2 + p, article h3 + p, article h4 + p, article h5 + p, article h6 + p{
  margin-top: 0;
}
```

## CSS spec

A custom selector is defined with the `@custom-selector` rule:

    @custom-selector = @custom-selector <extension-name> <selector>;

This defines a custom selector which is written as a pseudo-class with the given `<extension-name>`, and represents a `:matches()` selector using the provided `<selector>` as its argument.


For example, if an author wanted to easily refer to all heading elements in their HTML document, they could create an alias:

```css
@custom-selector :--heading h1, h2, h3, h4, h5, h6;

:--heading { /* styles for all headings */ }
:--heading + p { /* more styles */ }
/* etc */
```

## How to use it

### Pseudo elements/classes

You can use

* `:` to customise a class.
* `::` to customise a pseudo element.

For example to simulate `:any-link` you can do：

input.css:

```css
@custom-selector :--any-link :link, :visited;

a:--any-link {
  color: blue;
}
```

output:

```css
a:link, a:visited {
  color: blue;
}
```
### Multiples selectors

`@custom-selector` similar to CSS [`:matches()`](http://dev.w3.org/csswg/selectors-4/#matches) selector, but do not support multiple values:

```css
@custom-selector --heading h1, h2, h3, h4, h5, h6;
@custom-selector :--any-link :link, :visited;

.demo --heading, a:--any-link {
  font-size: 32px;
}
```

This will throw an error.

### Grunt

```js
module.exports = function(grunt) {
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    postcss: {
      options: {
        processors: [
          require('autoprefixer-core')({ browsers: ['> 0%'] }).postcss, //Other plugin
          require('postcss-custom-selector')(),
        ]
      },
      dist: {
        src: ['src/*.css'],
        dest: 'build/grunt.css'
      }
    }
  });

  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.loadNpmTasks('grunt-postcss');

  grunt.registerTask('default', ['postcss']);
}
```

### Gulp

```js
var gulp = require('gulp');
var rename = require('gulp-rename');
var postcss = require('gulp-postcss');
var selector = require('postcss-custom-selector')
var autoprefixer = require('autoprefixer-core')

gulp.task('default', function () {
    var processors = [
        autoprefixer({ browsers: ['> 0%'] }), //Other plugin
        selector()
    ];
    gulp.src('src/*.css')
        .pipe(postcss(processors))
        .pipe(rename('gulp.css'))
        .pipe(gulp.dest('build'))
});
gulp.watch('src/*.css', ['default']);
```

### Options

### `extensions` (default: `{}`)

This option allows you to customize an object to set `<extension-name>` (selector alias) and `<selector>`, these definitions will override the CSS in the same alias `@custom-selector`.

```js
var options = {
  extensions: {
    ':--any' : 'section, article, aside, nav'
  }
}

var output = postcss(selector(options))
  .process(css)
  .css;
```

input.css

```css
@custom-selector :--any .foo, .bar; /* overriden by JS definition */
:--any h1 {
  margin-top: 16px;
}
```

output:

```css
section h1, article h1, aside h1, nav h1 {
  margin-top: 16px;
}
```


## Contribute

Work on a branch, install dev-dependencies, respect coding style ([EditorConfig](http://editorconfig.org/)) & run tests before submitting a bug fix or a feature.

```console
$ git clone https://github.com/postcss/postcss-custom-selectors.git
$ git checkout -b patch
$ npm install
$ npm test
```

## [Changelog](CHANGELOG.md)

## [License](LICENSE)
