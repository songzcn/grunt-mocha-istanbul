[![NPM](https://nodei.co/npm/grunt-mocha-istanbul.png?downloads=true&stars=true)](https://nodei.co/npm/grunt-mocha-istanbul/)
[![Dependency Status](https://david-dm.org/pocesar/grunt-mocha-istanbul.png?theme=shields.io)](https://david-dm.org/pocesar/grunt-mocha-istanbul)

grunt mocha istanbul task
==============

[Mocha](http://visionmedia.github.com/mocha/) reporter to generate coverage report of [istanbul](http://gotwarlost.github.com/istanbul/) instrumented code, for grunt
This doesn't force you to use PhantomJS, or instrument code for server or client-side.

Install
==============

1. Install it using `npm install grunt-mocha-istanbul --save-dev`
2. It needs `mocha` and `grunt` to be installed locally on your project (aka, having them in your devDependencies)
3. Call inside Gruntfile.js `grunt.loadNpmTasks('grunt-mocha-istanbul')`

Changes from 0.2.0
==============

* `mocha_istanbul_check` was removed and became part of the options under the `check` object


Options
==============

Most of the options that you pass to mocha is available in `options`:

```js

module.exports = function(grunt){
    grunt.initConfig({
        mocha_istanbul: {
            coverage: {
                src: 'test', // the folder, not the files,
                options: {
                    mask: '*.spec.js'
                }
            },
            coveralls: {
                src: 'test', // the folder, not the files
                options: {
                    coverage:true,
                    check: {
                        lines: 75,
                        statements: 75
                    }
                }
            }
        }
    });

    grunt.event.on('coverage', function(lcovFileContents, done){
        // Check below
        done();
    });

    grunt.loadNpmTasks('grunt-mocha-istanbul');

    grunt.registerTask('coveralls', ['mocha_istanbul:coveralls']);
    grunt.registerTask('coverage', ['mocha_istanbul:coverage']);
};
```

If there's a `mocha.opts` file inside the `src`, it will warn if you are overwritting any options.

Coverage is written to `coverage` folder by default, in the same level as the `Gruntfile.js`

The `check` will fail the build if the thresholds are not met. It's a great possibility for CI-builds.

Options
==============

##### _Array_ `options.require` (default: `[]`)
##### _Boolean_ `options.ui` (default: `false`)
##### _Array_ `options.globals` (default: `[]`)
##### _String_ `options.reporter` (default: `false`)
##### _Number_ `options.timeout` (default: `false`)
##### _Boolean_ `options.slow` (default: `false`)
##### _String_ `options.grep` (default: `false`)
##### _Boolean_ `options.recursive` (default: `false`)

Mochas parameters, check [http://visionmedia.github.io/mocha/#usage]

##### _Boolean_ `options.coverage` (default: `false`)

Emits a grunt event 'coverage', that will contain the lcov data from the file

##### _Boolean_ `options.dryRun` (default: `false`)

Spits out the command line that would be called, just to make sure everything is alright

##### _String_ `options.mask` (default: `false`)

The mask for the tests to be ran. By default, mocha will execute the `test` folder and all test files

##### _Boolean_ `options.quiet` (default: `false`)

Suppresses the output from Mocha and Istanbul

##### _String_ `options.coverageFolder` (default: `coverage`)

Name of the output of the coverage folder

##### _Number_ `options.check.statements` (default: `false`)

Number of statements threshold to consider the coverage valid

##### _Number_ `options.check.lines` (default: `false`)

Number of lines threshold to consider the coverage valid

##### _Number_ `options.check.branches` (default: `false`)

Number of branches threshold to consider the coverage valid

##### _Number_ `options.check.functions` (default: `false`)

Number of functions threshold to consider the coverage valid

Coverage event
==============

When you set the option `coverage` to `true`, you'll receive the `coverage/lcov.info` file contents:

```js
grunt.event.on('coverage', function(lcov, done){
    console.log(lcov);
    done(); // or done(false); in case of error
});
```

This is mainly useful so you can send it to, for example, coveralls (using [coveralls](https://github.com/cainus/node-coveralls)):

```js
grunt.event.on('coverage', function(lcov, done){
    require('coveralls').handleInput(lcov, function(err){
        if (err) {
            return done(err);
        }
        done();
    });
});
```

This way, Travis-CI can send the Istanbul generated LCOV directly to Coveralls.io website.
