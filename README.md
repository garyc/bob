Bob
---

A minimalistic build tool for Node.js projects.

Overview
--------

Bob provides common build targets (clean, checkstyle, lint, test, coverage, package, deploy, stop, start, status, restart) for Node.js libs/apps. It essentially allows multiple projects to use the same Makefile stored in a global node_modules.

Installation
------------

    npm install -g bob

Config
------

Bob reads package.json file, please note that each property under "bob" is *optional*.

    {
        "name": "myproject",
        "version": "0.0.1",
        "bob": {
            "src": {
                "dir": "mysrc/"
            },
            "checkstyle": {
                "files": "foo.js bar/",
                "opts": "--checkstyle"
            },
            "hint": {
                "files": "foo.js bar/",
                "opts": "--jslint-reporter --config path/to/hintconfig.js"
            },
            "lint": {
                "files": "foo.js bar/",
                "opts": "--reporter path/to/lintreporter.js --config path/to/lintconfig.js"
            },
            "test": {
                "files": "bar/*.js",
                "opts": "--dot-matrix"
            },
            "coverage": {
                "files": "bar/*.js",
                "opts": "--cover-html"
            },
            "deploy": {
                "host": "myremotehost",
                "port": 22,
                "dir": "/remote/path/to/myproject"
            }
        }
    }

Project convention:

* package.json - project descriptor
* {name}.js - main app file, must support `node {name}.js start|stop|restart|status` (e.g. using learnboost/cluster)
* lib/ - .js library files
* test/ - .js test files
* build/ - generated by Bob to store buildtime reports/artifacts
* run/ - generated by Bob to store runtime logs/pids
* Uses `jscheckstyle`, `jshint`, `nodelint`, and `vows` as choices of tools

Usage
-----

Install required tools. (per Bob installation)

    bob tools

Install project dependencies. (per Node.js project)

    bob dep
    
Run Bob from project directory.

    bob target1 target2 target3 ...

Run Bob with specific environment. (by default it uses NODE_ENV=development)

    NODE_ENV=production bob start
    
Targets
-------

* clean - Delete build/ and run/ directory
* checkstyle - Run `jscheckstyle` against TODO
* lint - Run `nodelint` against all .js files under lib/ and test/ directories plus additional files configured in {app.build.lint}
* hint - Run `jshint` against all .js files under lib/ and test/ directories plus additional files configured in {app.build.hint}
* test - Run `vows` against all .js files under test/ directory
* coverage - Run `vows` against all .js files under test/ directory with coverage flag
* package - Create a source .tar.gz package at build/package/ directory
* stop - Stop the app
* start - Start the app
* restart - Restart the app
* status - Display app status
* nuke - Kill all processes with command containing the word 'node'
* deploy - Deploy the package to {app.deploy.host}:{app.deploy.port} at {app.deploy.dir}
* deploy-r - Deploy the package and then remotely restart the app
