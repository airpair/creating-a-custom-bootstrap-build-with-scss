## Who is this for?

Customizing [Bootstrap][] can be hard if done incorrectly.  Sometimes you can simply include more CSS rules after loading Bootstrap, but it's often not that easy.

If you have ever struggled to customize the overall look-and-feel of a Bootstrap-powered website, this tutorial is for you.

## Customizing Bootstrap font size and colors

Customizing fonts and colors is particularly difficult with Bootstrap.  If you change the base font size on your page, you'll also want to change header font sizes and margins for text elements like paragraph tags.

Fundamentally changing Bootstrap is not easy without recompiling Bootstrap's CSS.

What do I mean by recompiling?  Well, Bootstrap is built using a CSS preprocessor.  There are dozens of variables that control Bootstrap appearance.  A change in one of these variables can propagate throughout our website, affecting many different styles as appropriate.

The official version of Bootstrap is maintained using the [LESS][] CSS preprocessor, but we're going to use the [Sass][] version due to Sass's popularity.

## Build Bootstrap with Sass, Gulp, and Bower

We're going to use [Bower][] and [Gulp][] to compile Bootstrap, so we'll need [Node.js][].

### Create a Node.js project

First let's make a `package.json` file for Node to see.  Either use `npm init -y` or create a `package.json` file containing just an empty JSON object (`{}`).

Now let's install `gulp`, `gulp-bower`, and `gulp-sass`:

```bash
$ npm install --save-dev gulp gulp-bower gulp-sass
```

Our `package.json` file should now look something like this:

```json
{
  "devDependencies": {
    "gulp": "^3.8.11",
    "gulp-bower": "0.0.10",
    "gulp-sass": "^1.3.3"
  }
}
```

### Install Bootstrap for Sass with Bower

Now let's use Bower to install Bootstrap.  This will allow us to import Bootstrap into our SCSS code and compile it down to CSS manually.

Create a `bower.json` file using `bower init` or by manually creating one:

```json
{
  "name": "custom-bootstrap-example",
  "authors": [
    "Lillian Langston <lillian@example.com>"
  ],
  "private": true,
  "ignore": [
    "**/.*",
    "node_modules",
    "bower_components",
    "test",
    "tests"
  ],
  "dependencies": {
  }
}
```

Now let's install [`bootstrap-sass`][bootstrap sass] with Bower.

```bash
$ bower install --save bootstrap-sass
```

Our `bower.json` file should now have `bootstrap-sass` listed as a dependency:

```
"dependencies": {
  "bootstrap-sass": "~3.3.3"
}
```

Now we can make an SCSS file that includes bootstrap into our project.  Let's call our SCSS file `css/app.scss`:

```scss
@import "bootstrap";
@import "bootstrap/theme";
```

### Compile Bootstrap for Sass with Gulp

Now let's use gulp to compile our `app.scss` which includes Bootstrap SASS.  Create a `gulpfile.js` file which contains:

```js
var gulp = require('gulp');
var sass = require('gulp-sass');

var config = {
    bootstrapDir: './bower_components/bootstrap-sass',
    publicDir: './public',
};

gulp.task('css', function() {
    return gulp.src('./css/app.scss')
    .pipe(sass({
        includePaths: [config.bootstrapDir + '/assets/stylesheets'],
    }))
    .pipe(gulp.dest(config.publicDir + '/css'));
});

gulp.task('fonts', function() {
    return gulp.src(config.bootstrapDir + '/assets/fonts/**/*')
    .pipe(gulp.dest(config.publicDir + '/fonts'));
});

gulp.task('default', ['css', 'fonts']);
```

In the file above, we have made three gulp tasks.  The `css` task reads our `css/app.scss` Sass file, compiles it into CSS, and stores the CSS in `public/css`.  The `fonts` task copies the Bootstrap fonts to `public/fonts`.  The `default` task executes both the `css` and `fonts` tasks.

Now when we run `gulp`, the `default` task will execute and our compiled Bootstrap CSS should appear in the `public/css` directory:

```bash
$ gulp
$ ls public/css
app.css
```

## Customizing the font size

Now let's look at how we can go about customizing the font size in Bootstrap.

Notice that the value of the `$font-size-base` variable in the [`_variables.scss` file][variables.scss] is used for calculating a variety of other important variables.  For example 8 of the lines below rely on `$font-size-base`:

```scss
$font-size-base:          14px !default;
$font-size-large:         ceil(($font-size-base * 1.25)) !default; // ~18px
$font-size-small:         ceil(($font-size-base * 0.85)) !default; // ~12px

$font-size-h1:            floor(($font-size-base * 2.6)) !default; // ~36px
$font-size-h2:            floor(($font-size-base * 2.15)) !default; // ~30px
$font-size-h3:            ceil(($font-size-base * 1.7)) !default; // ~24px
$font-size-h4:            ceil(($font-size-base * 1.25)) !default; // ~18px
$font-size-h5:            $font-size-base !default;
$font-size-h6:            ceil(($font-size-base * 0.85)) !default; // ~12px
```

Notice those `!default` flags?  That `!default` flag means the variables will be set *only* if they don't have a value already.

All of the variables assigned in [Bootstrap SASS's][bootstrap sass] `_variables.scss` file have a `!default` flag.  That means we can override those variables by assigning our own values before we import Bootstrap.

Let's copy Bootstrap's `_variables.scss` file and make our own custom version:

```bash
$ cp bower_components/bootstrap-sass/assets/stylesheets/bootstrap/_variables.scss css/_variables.scss
```

Now we need to reference our custom variables module from our `app.scss` file.

```scss
@import "variables";
@import "bootstrap";
@import "bootstrap/theme";
```

Remember to import our `variables` module *before* we import Bootstrap!  If we import it afterward, our changes customizations won't be applied.

Now let's change `$font-size-base` to `16px` in `css/_variables.scss`:

```scss
$font-size-base:          16px;
```

Now if we recompile our CSS we should see our larger font size reflected throughout our application:

```bash
$ gulp
```

## Try it out!

I made a sample project to demonstrate how easy it is to customize Bootstrap variables before building [Bootstrap Sass][].

[Check out the sample project on Github](https://github.com/treyhunner/custom-bootstrap-example)

Know about a different way to customize Bootstrap?  Did I make a mistake in my explanation?  Leave a comment and let me know.

---

This article is also available on [Trey Hunner's blog](http://treyhunner.com/2015/02/creating-a-custom-bootstrap-build/).

[bootstrap]: http://getbootstrap.com/
[bower]: http://bower.io/
[bootstrap sass]: https://github.com/twbs/bootstrap-sass
[gulp]: http://gulpjs.com/
[variables.scss]: https://github.com/twbs/bootstrap-sass/blob/master/assets/stylesheets/bootstrap/_variables.scss#L52
[less]: http://lesscss.org/
[node.js]: http://nodejs.org/
[sass]: http://sass-lang.com/
