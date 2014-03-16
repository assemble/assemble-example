# Introduction to Assemble

> This project will help you get started with Assemble. Just download, install the dependencies, and you're off and running!

Think Assemble seems complicated? You might be amazed at how litte you need to get started. [This gist](https://gist.github.com/jonschlinkert/9579914) has everything you need to create a 100% complete Assemble project!

## Getting started

Ready to learn how to do _more than_ what's covered in the gist? Of course you are! Let's get this ball rolling!

### Install

Download this project using one of the following options:

* Use git: `git clone https://github.com/jonschlinkert/assemble-example.git`
* Download directly [from GitHub](https://github.com/jonschlinkert/assemble-example/archive/master.zip), then unpack the zip file.

Next, in the root of the `assemble-example` directory, to install the necessary dependencies, run:

```bash
npm install
```
Done! You should now be able to run `grunt` to build the project.

_<sup>(Although this example project uses Grunt, unless otherwise noted the information here is build-chain agnostic. If you're a Gulp fan, stay on the lookout, we'll be publishing a [gulp-assemble](https://github.com/assemble/gulp-assemble) example soon!)</sup>_


### Usage

Let's start with the basics. This is everything Assemble requires to successfully build a project:

```js
assemble: {
  site: {
    files: {'dest/': ['templates/*.hbs']}
  }
}
```

Want more? What are you waiting for? Read on...

_(This example project uses a [Gruntfile](./Gruntfile.js) so the configuration examples use [Grunt](http://gruntjs.com/) conventions, but the options themselves are not specific to Grunt. You can use the same options in your [Gulpfile](https://github.com/gulpjs/gulp/blob/master/gulpfile.js) using [Gulp](http://gulpjs.com/) conventions, or with Assemble > v0.5 you can pass the options directly to Assemble if you prefer.)_

## Assemble core concepts

To get the most out of Assemble, it helps to be familiar with the following core concepts:

* [Templates](#templates)
  - [Layouts](#layouts)
  - [Pages](#pages)
  - [Partials](#partials)
* [Data](#data)
* [Content](#content)

By the end of this document, you will know what each of these concepts mean, as well as how to define options for them in your project configuration. Let's get started.

### Templates

> Templates keep your code as organized, modular, and reusable as it can be. Which means projects will be easier to maintain as a result.

A template is a document or document fragment that contains variables that will be replaced (by the template engine) with actual data, content or other documents. Assemble uses [Handlebars.js](handlebarsjs.com) as its default template engine <sup>[1](#extending-assemble)</sup>. So the syntax you see in the examples comes from that library.

Templates can be any shape and size you want them to be, but Assemble has built-in support and conventions for the following template structures:

* **Layouts**: used to "wrap" pages with common or site-wide elements, such as headers and footers, the `<head></head>` section, navigation and so on. Note that _layouts are also optional_.
* **Pages**: typically have a 1-to-1 relationship with the actual generated HTML pages in a project, e.g. `about.hbs` => `about.html` or `about/index.html`. But pages can also be dynamically generated from config data. It might also help to think of a page as something that would get inserted into the middle of a layout.
* **Partials**: referred to sometimes as _includes_, partials are like document fragments, snippets, or other small chunks of reusable code that will be included, inserted or embedded into other templates at build time. A partial can be a button, or a navbar, or even a Google Analytics script. For an example, see [how Zurb Foundation uses partials](https://github.com/zurb/foundation/tree/master/doc/includes) in their [documentation](http://foundation.zurb.com/docs/).

Here are some examples and additional explanation of each template type.

#### Layouts

As mentioned above, layouts are used to "wrap" other pages with common elements. So a basic layout might look something like this:

```handlebars
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <!-- variables like `title` are simply placeholders for
    real data -->
    <title>{{title}}</title>
  </head>
  <body>
    <!-- insertion point for any page using this layout -->
    {{> body }}
  </body>
</html>
```

**Layout configuration**

You can tell Assemble that you want to use a particular layout by defining it in the options:

```js
options: {
  layout: 'path/to/my-default-layout.hbs'
}
```

If you need more than one layout, you can optionally define a base directory for layouts using `layoutdir`:

```js
options: {
  layoutdir: 'path/to/layouts'
  layout: 'my-default-layout.hbs'
}
```

Remember, layouts aren't required. Sometimes you need one, sometimes you don't, and sometimes you need more than one. You might even need sub-layouts or nested layouts! No worries, [we have you covered there too](http://assemble.io/docs/Layouts.html)!


#### Pages

> Pages, generally **structural in nature**, contain more HTML than textual content, and can be (optionally) wrapped with layouts.

A basic page might look something like this:

```handlebars
<div class="page-header">
  <h2 id="about">About Us</h2>
</div>

<div class="docs-section">
  <div class="page-header">
    <h2 id="team">Team</h2>
  </div>
  <!-- This markdown helper will include the content from `team.md`
  and convert it to HTML -->
  {{md 'team'}}
  Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed
  do eiusmod tempor incididunt ut labore et dolore magna aliqua.
</div>

<div class="docs-section">
  <div class="page-header">
    <h2 id="history">History</h2>
  </div>
  Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed
  do eiusmod tempor incididunt ut labore et dolore magna aliqua.
</div>
```

**Pages configuration**

Pages are the "source files" in your configuration. Jump back up to the [usage section](#usage) for a basic example, or refer to [Grunt's documentation](http://gruntjs.com/configuring-tasks#files) to learn more about the vavious formats that can be used in the Gruntfile for defining source and dest files.

#### Partials

> Partials allow you to define a chunk of code one time and use it in multiple places.

Partials are often used for UI components such as buttons, navbars or modals. But they can also be used for any other snippets or sections of code that might be repeated across multiple pages, or for code that might otherwise be reusable in some way. Partials are easy to spot since they use a `>`, which is the special [Handlebars.js syntax](http://blog.teamtreehouse.com/handlebars-js-part-2-partials-and-helpers)) that is only used with partials: e.g. `{{> foo }}`.

Continuing with the `layout` example from above, to use a partial for the `head` section simply create a new file, such as `head.hbs` or whatever you prefer, then extract the code from the head section and add it to the new file:

```handlebars
<!-- `head.hbs` partial -->
<meta charset="UTF-8">
<title>{{title}}</title>
```

Before continuing on, ensure that the filepath to your newly created partial, `head.hbs`, is specified in your project's configuration so Assemble can take note of it, ensuring that the partial can be used in your templates.

Now, to actually use the partial, add the `{{> head }}` template to the `head` section of your layout where the code was removed. Assemble makes this simple by allowing you to use the name of the file you just created as the name of the partial:

```handlebars
<!DOCTYPE html>
<html lang="en">
  <head>
    <!-- The `>` means that this is a partial and its content
    will be inserted here. -->
    {{> head }}
  </head>
  <body>
    <!-- Remember that `{{> body }}` is used for inserting pages.
    this is the only `partial` name that is specially-reserved
    by Assemble -->
    {{> body }}
  </body>
</html>
```

**Partials configuration**

Before you can use partials in your templates, you need to tell Assemble where they are. You can do this by adding a `partials` property to the options.

Example:

```js
options: {
  // How you organize your project is your business. Assemble
  // just needs to know where your partials are and the file
  // extensions you'll be using.
  partials: ['templates/partials/*.hbs', 'templates/snippets/**/*.html']
}
```

### Content

> Content is usually written in an easy-to-read plain text format such as markdown

Converting markdown to HTML with Assemble is simple.

To include external markdown files and have them converted to HTML at build time, you can use the `md` helper:

```html
<h1>My Blog Post</h1>
{{md 'foo/bar.md'}}
```

Or you can wrap sections of markdown with the `markdown` block helper:

```handlebars
<!-- anything in this block will be converted to HTML -->
{{#markdown}}
# My Blog Post

> This is my first blog post!

Whoo hoo!
{{/markdown}}
```

Using helpers to process markdown allows users to write HTML or markdown, or both together. It also keeps things simple while giving you the freedom to convert your content to HTML according to your preferences:

* Convert 1-to-1 into HTML pages, e.g. `about.md` would convert to `about.html` (or `about/index.html` using [permalinks](https://github.com/assemble/assemble-contrib-permalinks))
* Insert into other pages (as includes)
* Concatenate several content files together before converting to pages or being inserted into pages. The [assemble.io/helpers/](http://assemble.io/helpers/) documentation page is a good example of this. The sections on this page were each created from an individual markdown file. In total, _Assemble seamlessly combines more than [100 individual markdown files][helpers] to construct this page!_.

### Data

> Data from JSON or YAML files is passed to your templates at build time.

This is best explained through examples, so given you have a partial for buttons, `button.hbs`:

```handlebars
<button type="button" class="btn {{modifier}}">{{text}}</button>
```

And given you have a corresponding data file, `button.json`, with the following data:

```json
[
  {
    "text": "Success!",
    "modifier": "btn-success"
  },
  {
    "text": "Error!",
    "modifier": "btn-error"
  },
  {
    "text": "Warning!",
    "modifier": "btn-warning"
  }
]
```

Used like this:

```handlebars
{{#each button}}
  {{> button }}
{{/each}}
```

Results in:

```html
<button type="button" class="btn btn-success">Success!</button>
<button type="button" class="btn btn-error">Error!</button>
<button type="button" class="btn btn-warning">Warning!</button>
```

Beyond being passed to templates as context, _data files can also be used for global project configuration and setting options_. See the [documentation for data](#TODO) to learn more.


## All together

By now your entire Gruntfile config would look something like this:

```js
grunt.initConfig({

  assemble: {
    options: {
      partials: ['templates/includes/*.hbs'],
      layout: 'templates/layouts/default.hbs'
    },
    site: {
      files: {'dest/': ['templates/*.hbs']}
    }
  }
});
```

And that's a wrap! At least for this example, which covers only a fraction of what Assemble has to offer. Please [visit Assemble's documentation](http://assemble.io) if you want to learn about using Assemble, the API, how to extend Assemble, or other topics such as:

* [Creating plugins](http://assemble.io/plugins/)
* [Registering helpers](http://assemble.io/helper/)
* [Adding template engines](http://assemble.io/engines/)

If you don't find what you need here or in the docs, we encourage you to visit Assemble's [GitHub Issues][issues] page to create an issue, we're always happy to help new users get started!

## Authors

This example and guide was written by [Jon Schlinkert](https://github.com/jonschlinkert), Assemble was created by:

**Jon Schlinkert**

+ [github/jonschlinkert](https://github.com/jonschlinkert)
+ [twitter/jonschlinkert](http://twitter.com/jonschlinkert)

**Brian Woodward**

+ [github/doowb](https://github.com/doowb)
+ [twitter/doowb](http://twitter.com/jonschlinkert)


## License

Copyright (c) 2014 [Jon Schlinkert](http://twitter.com/jonschlinkert), [Brian Woodward](http://twitter.com/doowb), contributors.
Released under the [MIT license](./LICENSE-MIT)


[permalinks]: https://github.com/assemble/assemble-contrib-permalinks
[helpers]: https://github.com/assemble/assemble-docs/tree/master/src/content/helpers
[issues]: https://github.com/assemble/assemble/issues?direction=desc&sort=updated&state=open

