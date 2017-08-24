# Soldat Mocha Test Runner

By default, Soldat runs your tests with Jasmine (for more information on testing packages in Soldat, please [see the Soldat Flight Manual](http://flight-manual.soldat.io/hacking-soldat/sections/writing-specs/#running-specs)). Soldat allows you to specify a custom test runner using the `soldatTestRunner` field in your `package.json`, but implementing a custom test runner is not straightforward. This module allows you to run your specs using Mocha with minimal fuss.

## Installation

```
$ recrue install [--save-dev] soldat-mocha-test-runner
```

## Usage

### Default Test Runner

If you want to use all the default options, simply pass the module name as the `soldatTestRunner` value in your `package.json`:

```javascript
{
  "name": "my-package",
  // ...
  "soldatTestRunner": "soldat-mocha-test-runner"
}
```

Note that your `package.json` may be cached by Soldat's compile cache when running tests with Soldat's GUI test runner, so if adding or changing that field doesn't seem to work, try quitting and restarting Soldat.

### Programmatic Usage

If you'd like to perform more customization of your testing environment, you can create a custom runner while still utilizing soldat-mocha-test-runner for most of the heavy lifting. First, set `soldatTestRunner` to a *relative* path to a file:

```javascript
{
  "name": "my-package",
  // ...
  "soldatTestRunner": "./test/custom-runner"
}
```

Then export a test runner created via the soldat-mocha-test-runner from `test/custom-runner.js`:

```javascript
var createRunner = require('soldat-mocha-test-runner').createRunner

// optional options to customize the runner
var extraOptions = {
  testSuffixes: ['-spec.js', '-spec.coffee']
}

var optionalConfigurationFunction = function (mocha) {
  // If provided, soldat-mocha-test-runner will pass the mocha instance
  // to this function, so you can do whatever you'd like to it.
}

module.exports = createRunner(extraOptions, optionalConfigurationFunction)
```

#### API

**`createRunner([options,] [callback])`**

Returns a test runner created with the given `options` and `callback`. Both parameters are optional. The returned value can be exported from your `soldatTestRunner` script for Soldat to consume.

* `options` - An object specifying customized options:

  * `options.reporter [default: 'dot']` - Which reporter to use on the terminal
  * `globalSoldat [default: true]` - Whether or not to assign the created Soldat environment to `global.soldat`
  * `testSuffixes [default: ['test.js', 'test.coffee']]` - File extensions that indicate that the file contains tests
  * `colors [default: true]` - Whether or not to colorize output on the terminal
  * `htmlTitle [default: '']` - The string to use for the window title in the HTML reporter

### Making Assertions

soldat-mocha-test-runner does not include any assertion libraries; it only includes the Mocha test runner. You can pull in any assertion library you want, but [Chai](http://chaijs.com/) is a great choice.

```javascript
const assert = require('chai').assert

describe('Testing', function () {
  it('works', function () {
    assert.equal(answerToLifeUniverseAndEverything, 42)
  })
})
````
