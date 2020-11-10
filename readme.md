> This is fork of [stringify-object](https://github.com/yeoman/stringify-object/blob/master/package.json)

Stringify object with code chunks insertion


## Install

```
$ yarn add smart-stringify-object
```


## Usage

```js
var obj = {
	foo: 'bar',
	'arr': [1, 2, 3],
	nested: { hello: "world" },
	method: () => 'function () { return this.foo; }'
};

var pretty = stringifyObject(obj, {
	indent: '  ',
	singleQuotes: false,
	functions: 'exec'
});

console.log(pretty);
/*
{
	foo: "bar",
	arr: [
		1,
		2,
		3
	],
	nested: {
		hello: "world"
	},
	method: function () { return this.foo; }
}
*/
```

## Stringify functions

You can choose how exactly will be stringified object's properties of function type.

By default, the functions will be stringified as they were described in a code but you can use the functions as a tool to generate javascript code. There are two variants to use the functions in such a way:

### Exec

Option: `function: "exec"

Execute a function and insert the result as a code chunk.

```js
const obj = {
	name: path.basename(__filename),
	module: () => `require(${JSON.stringify(__filename)})`
}

stringify(obj, {
	functions: "exec"
})
/*
{
	name: 'example.js',
	module: require("path/to/example.js")
}
*/
```

### Extract

Option: `function: "extract"`

Get function's body and insert it as code chunk.

```js
var obj = {
	key: function() { Symbol() }
}

var pretty = stringifyObject(obj, {
	functions: "extract"
}
console.log(pretty);
/*
{
	key:  Symbol()
}
*/
```

## API

### stringifyObject(input, [options])

Circular references will be replaced with `"[Circular]"`.

#### input

*Required*  
Type: `object`, `array`

#### options

##### indent

Type: `string`  
Default: `'\t'`

Choose the indentation you prefer.

##### singleQuotes

Type: `boolean`  
Default: `true`

Set to false to get double-quoted strings.

##### filter(obj, prop)

Type: `function`

Expected to return a boolean of whether to keep the object.

##### inlineCharacterLimit

Type: `number`
Default: undefined

When set, will inline values up to `inlineCharacterLimit` length for the sake
of more terse output.

For example, given the example at the top of the README:

```js
var obj = {
	foo: 'bar',
	'arr': [1, 2, 3],
	nested: { hello: "world" }
};

var pretty = stringifyObject(obj, {
	indent: '  ',
	singleQuotes: false,
	inlineCharacterLimit: 12
});

console.log(pretty);
/*
{
	foo: "bar",
	arr: [1, 2, 3],
	nested: {
		hello: "world"
	}
}
*/
```

As you can see, `arr` was printed as a one-liner because its string was shorter
than 12 characters.

##### functions

Type: `"extract" | "exec" | false`   
Default: `false`

Choose method to stringify functions

- **extract** Extract function body as raw code
- **exec** Execute function and use its result as raw code

Exctract example:
```js
var obj = {
	key: function() { Symbol() }
}

var pretty = stringifyObject(obj, {
	functions: "extract"
}
console.log(pretty);
/*
{
	key:  Symbol()
}
*/
```

Exec example:
```js
var moduleName = "path/to/module.js"
var obj = {
	myModule: () => `require(${JSON.stringify(moduleName)})`
}

var pretty = stringifyObject(obj, {
	functions: "exec"
}
console.log(pretty);
/*
{
	myModule:  require("path/to/module.js")
}
*/
```

## License

[BSD license](http://opensource.org/licenses/bsd-license.php) © Vladimir Kalmykov, Yeoman Team
