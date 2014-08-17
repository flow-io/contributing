Contributing
============

Flow.io is a collection of stream utilities for statistical processing of numeric data streams. To contribute to Flow.io, please follow the style guide provided below.


## JavaScript

Flow.io uses the following JavaScript [style guide](https://github.com/kgryte/javascript-style-guide). Most importantly, modules should be written in the same style and idiom.


## Generator

You are encouraged to use the [Yeoman](http://yeoman.io) Flow.io [generator](https://github.com/flow-io/generator-flow-io). The generator creates a base scaffold from which to build your flow module.

Before using the generator, you should create a remote repository (of the same name as your module) on [Github](https://github.com/flow-io). The generator will use the module name to generate the remote URLs included in the `package.json`. Additionally, the generator takes care of setting the remote origin for the local Git repository, so you can begin pushing code immediately after generation.


## Modules

The main file for every module should export a function which instantiates a new stream factory.

### Factory

The rationale for using a factory pattern is to ease reuse of stream configurations. For instance, a stream which calculates a rolling-average over a stream of numeric data values requires a specified `window` size. The `window` defines the number of values over which to calculate an average. For multiple data sources, you may want to calculate a rolling-average for each source. Accordingly, you would need to configure and create a separate rolling-average stream for each data source. A configurable stream factory eases this burden, especially for streams requiring multiple configuration parameters (e.g., streams calculating the Pearson product-moment correlation coefficient).

For those streams not requiring configuration (e.g., streams which round numeric data values), the factory pattern is not necessary. You are, however, __strongly encouraged__ to use the factory pattern to ensure consistency across all flow modules.

One __requirement__ for every stream factory is that the factory prototype must have a `stream()` method. This method should return a configured stream.


### Setters/Getters

You are encouraged to use setters/getters with type checking over initial configuration objects. Doing so eases on-the-fly configuration adjustments when dynamically creating new streams.

``` javascript
// Do:
flow.stream()
	.window( 5 )
	.accessor( 'x', function( d ) {
		return d[ 0 ];
	})
	.accessor( 'y', function( d ) {
		return d[ 1 ];
	})
	.edges( edges )
	.type( 'normalized' );

// Don't:
flow.stream({
	'window': 5,
	'x': function( d ) {
		return d[ 0 ];
	},
	'y': function( d ) {
		return d[ 1 ];
	},
	'edges': edges,
	'type': 'normalized'
});
```

### Type Checking

Additionally, for setters, you are __strongly encouraged__ to type check input arguments. While including checks leads to longer modules and more required tests, doing so helps define user expectations and allows users to more easily debug their code.

``` javascript
// Do:
Stream.prototype.window = function( window ) {
	if ( !arguments.length ) {
		return this.window;
	}
	if ( typeof window !== 'number' || window !== window || window < 1 ) {
		throw new Error( 'window()::invalid input argument. Window must be a positive integer.')
	}
	this.window = parseInt( window, 10 );
	return this;
}

// Don't:
Stream.prototype.window = function( window ) {
	if ( !arguments.length ) {
		return this.window;
	}
	this.window = window;
	return this;
}
```


## Tests

All modules should instrument testing. Currently, [Mocha](http://visionmedia.github.io/mocha) is the preferred test framework with [Chai](http://chaijs.com) assertions. For code coverage, you are encouraged to use [Istanbul](https://github.com/gotwarlost/istanbul).

If you use the Flow.io [generator](https://github.com/flow-io/generator-flow-io), the above test modules are included. Strive for 100% code coverage.


---
## License

[MIT license](http://opensource.org/licenses/MIT). 


---
## Copyright

Copyright &copy; 2014. Athan Reines.