# Code Coverage with Karma

The Angular CLI takes care of Jasmine and Karma configuration. It creates full configuration in memory, based on the options specified in Angular CLI configuration file (`angular.json`).

## Code Coverage Reporting

If you want to create code coverage reports every time you test, set the following options in the Angular CLI configuration file:

```json
{
  ...
  "test": {
    "options": {
      ...
      "codeCoverage": true
    }
  }
}
```

## Code Coverage Enforcement

In order to enforce code coverage, you will have to fine tune the `karma` configuration. First, add a custom `karma.conf.js` file with custom configuration options for checking code coverage.

```js
module.exports = function (config) {
  config.set({
    basePath: "",
    frameworks: ["jasmine", "@angular-devkit/build-angular"],
    plugins: [
      require("karma-jasmine"),
      require("karma-chrome-launcher"),
      require("karma-jasmine-html-reporter"),
      require("karma-coverage"),
      require("@angular-devkit/build-angular/plugins/karma"),
    ],
    client: {
      jasmine: {
        // you can add configuration options for Jasmine here
        // the possible options are listed at https://jasmine.github.io/api/edge/Configuration.html
        // for example, you can disable the random execution with `random: false`
        // or set a specific seed with `seed: 4321`
      },
      clearContext: false, // leave Jasmine Spec Runner output visible in browser
    },
    jasmineHtmlReporter: {
      suppressAll: true, // removes the duplicated traces
    },
    coverageReporter: {
      coverageReporter: {
        dir: require("path").join(__dirname, "./coverage/<project-name>"),
        subdir: ".",
        reporters: [{ type: "html" }, { type: "text-summary" }],
      },
    },
    reporters: ["progress", "kjhtml"],
    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    browsers: ["Chrome"],
    singleRun: false,
    restartOnFileChange: true,
  });
};
```

Then, let's assume that you decided to have at least 80% code coverage for your tests. Add the `check` property to your `karma.conf.js` file under the `coverageReporter` key.

```js
module.exports = function (config) {
  ...
  coverageReporter: {
    ...
    check: {
      global: {
        statements: 80,
        branches: 80,
        functions: 80,
        lines: 80
      }
    }
  }
}
```

Finally, update Angular CLI configuration file to use the configuration file from `karma`.

```json
{
  ...
  "test": {
    "builder": "@angular-devkit/build-angular:karma",
    "options": {
      "karmaConfig": "karma.conf.js",
      "polyfills": ["zone.js", "zone.js/testing"],
      "tsConfig": "src/tsconfig.spec.json",
      "styles": ["src/styles.css"]
    }
  }
}
```

---

[Back to Angular Guides List](./README.md)
