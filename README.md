# grunt-iconizr [![NPM version][npm-image]][npm-url] [![Build Status][travis-image]][travis-url] [![Dependency Status][depstat-image]][depstat-url]

is a Grunt plugin wrapping around [svg-sprite](https://github.com/jkphl/svg-sprite) which **reads in a bunch of [SVG](http://www.w3.org/TR/SVG/) files**, optimizes them and creates **SVG sprites** in various flavours:

1. Traditional **CSS sprites** for use with background images ([configuration](#d-1-css-mode))
2. CSS sprites with **pre-defined SVG views**, suitable for foreground images as well ([configuration](#d-2-view-mode))
3. Inline sprites using the **`<defs>` element** ([configuration](#d-3-defs-mode))
4. Inline sprites using the **`<symbol>` element** ([configuration](#d-4-symbol-mode))
5. **SVG stacks** ([configuration](#d-5-stack-mode))

## Features & configuration? → [svg-sprite](https://github.com/jkphl/svg-sprite)

This manual covers only Grunt specific installation and configuration aspects. For a full list of features and options, please see the [svg-sprite manual](https://github.com/jkphl/svg-sprite).

## Getting Started

This plugin requires Grunt `~0.4.5`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-iconizr --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-iconizr');
```

## The «svg_sprite» task

### Overview

In your project's Gruntfile, add a section named `svg_sprite` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
	svg_sprite		: {
		options		: {
			// Task-specific options go here.
		},
		your_target	: {
			// Target-specific file lists and/or options go here.
		},
	},
});
```

The task-specific `options` are optional and affect all defined targets. You may define as many targets (`your_target`) as you want.

### Targets

In the simplest case an «svg_sprite» target looks like this:

```javascript
your_target: {
	src			: ['path/to/assets/**/*.svg'],
	dest		: 'path/to/css/dir',
	options		: {
		// Target-specific options
	}
},
```

However, as the `path/to/assets` would become part of the shape IDs, you will most likely want to add a working directory in most cases:

```javascript
your_target: {
	expand		: true,
	cwd			: 'path/to/assets',
	src			: ['**/*.svg'],
	dest		: 'path/to/css/dir',
	options		: {
		// Target-specific options
	}
},
```

### Options

As **target-specific options** you may provide a [main configuration object](https://github.com/jkphl/svg-sprite#main-configuration) as described in the *svg-sprite* manual. Configuration-wise, *svg-sprite* and *grunt-iconizr* differ only in one respect:

#### options.dest → dest

Type: `String`
Default value: `'.'`

Instead of being nested inside the `options` object, *svg-sprite*'s `dest` property gets **promoted one level up** and becomes part of the Grunt target configuration itself (see examples above). 

### Usage Examples

#### Basic example

In this very basic example, mostly default settings will be applied to create a traditional CSS sprite (bundle of SVG sprite and CSS stylesheet).

```javascript
grunt.initConfig({
	svg_sprite					: {
		basic					: {
		
			// Target basics
			expand				: true,
			cwd					: 'assets',
			src					: ['**/*.svg'],
			dest				: 'out',
			
			// Target options
			options				: {
				mode			: {
					css			: {		// Activate the «css» mode
						render	: {
							css	: true	// Activate CSS output (with default options)
						}
					}
				}
			}
		}
	}
});
```

The following files and directories are created:

```bash
out
`-- css
    |-- sprite.css
    `-- svg
        `-- sprite.css-495d2010.svg
```

> The cryptical looking part in the SVG's file name is the result of *svg-sprite*'s cache busting feature which is enabled by default for CSS sprites. We'll turn this off in the next example.

#### More complex example

The following example is a little more complex:

* We'll create a **«view» CSS sprite** and a **«symbol» sprite** in one go.
* Instead of CSS, we'll render a **Sass stylesheet** resource for the «view» sprite.
* We'll **turn off cache busting** for the «view» sprite and create **extra CSS rules specifying each shape's dimensions**.
* We'll **downscale the SVG shapes** to 32×32 pixels if necessary and **add 10 pixels padding** to all sides.
* We'll keep the intermediate SVG source files.

```javascript
grunt.initConfig({
	svg_sprite					: {
		complex: {
		
			// Target basics
			expand					: true,
			cwd						: 'assets',
			src						: ['**/*.svg'],
			dest					: 'out',
			
			// Target options
			options					: {
				shape				: {
					dimension		: {			// Set maximum dimensions
						maxWidth	: 32,
						maxHeight	: 32
					},
					spacing			: {			// Add padding
						padding		: 10
					},
					dest			: 'out/intermediate-svg'	// Keep the intermediate files
				},
				mode				: {
					view			: {			// Activate the «view» mode
						bust		: false,
						render		: {
							scss	: true		// Activate Sass output (with default options)
						}
					},
					symbol			: true		// Activate the «symbol» mode
				}
			}
		}
	}
});
```

The following files and directories are created:

```javascript
out
|-- intermediate-svg
|   |-- weather-clear.svg
|   |-- weather-snow.svg
|   `-- weather-storm.svg
|-- symbol
|   `-- svg
|       `-- sprite.symbol.svg
`-- view
    |-- sprite.scss
    `-- svg
        `-- sprite.view.svg
```

#### Advanced features

For more advanced features like

*	[custom transforms](https://github.com/jkphl/svg-sprite#b-2-custom-transformations-object-values),
*	[meta data injection](https://github.com/jkphl/svg-sprite#a-1-meta-data-injection),
*	customizing output templates or
*	introducing new output formats

please refer to the [svg-sprite manual](https://github.com/jkphl/svg-sprite).

Contributing
------------

In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using [Grunt](http://gruntjs.com/).

Release history
---------------

#### v1.0.0
* First release of the new plugin generation, compatible with [svg-sprite 1.0.x](https://github.com/jkphl/svg-sprite/tree/v1.0.0)
* Rewritten from scratch

For older release notes please [see here](https://github.com/jkphl/grunt-iconizr/tree/00f36c5a217798bfa22a9b80c8bd2a75dcbb32dd#release-history).

Legal
-----

Copyright © 2015 [Joschi Kuphal](https://jkphl.is) (<joschi@kuphal.net> / [@jkphl](https://twitter.com/jkphl))

*grunt-iconizr* is licensed under the terms of the [MIT license](LICENSE.txt).

The contained example SVG icons are part of the [Tango Icon Library](http://tango.freedesktop.org/Tango_Icon_Library) and belong to the Public Domain.


[npm-url]: https://npmjs.org/package/grunt-iconizr
[npm-image]: https://badge.fury.io/js/grunt-iconizr.png

[travis-url]: http://travis-ci.org/jkphl/grunt-iconizr
[travis-image]: https://secure.travis-ci.org/jkphl/grunt-iconizr.png

[depstat-url]: https://david-dm.org/jkphl/grunt-iconizr
[depstat-image]: https://david-dm.org/jkphl/grunt-iconizr.svg