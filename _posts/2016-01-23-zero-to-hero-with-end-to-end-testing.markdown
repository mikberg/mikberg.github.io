---
layout: post
title:  "Zero to Hero with End-to-End testing using Nightwatch, SauceLabs and Travis"
date:   2016-01-23 13:59:43 +0000
categories: javascript ci testing
---
Developing super cool web apps has always been a walk on a thin line: You want to use the latest possible language features and APIs, but at the same time, it needs to work in loads of, for developers, obscure browsers like IE10 and Android Browser.

Deploying quickly grinds to a holt when you find out in final tests that your app doesn’t work in all of the required browsers and environments, and last minute hacks are sometimes needed. Using a continuous integration testing tool with real-life browsers was a real timesaver and stress-reliever for me.

This is a summary of the feature branch that took me there.

## Prerequisites

In order to get something tested at all, we need two ingredients: A *browser automation tool* and a *test runner*. For browser automation, [Selenium](http://www.seleniumhq.org/) is the obvious option, and the one cloud testing providers like BrowserStack and SauceLabs use.

If you’re on OSX, run `brew install selenium-server-standalone`, along with a webdriver for your favourite browser: `brew install chromedriver`. Run it with `selenium-server -p 4444`.

There are many test runners which integrate with Selenium. You can choose any test runner you want, so choose one that makes sense for you, based on programming language and needs. This article is based on [Nightwatch](http://nightwatchjs.org/), but should be easily translatable for other test runners.

Go ahead and install Nightwatch with `npm install -g nightwatch`.

## Configuring Nightwatch

Nightwatch needs some config, so create a new file called `nightwatch.js`. Nightwatch uses a `.json` config file as default, but using a JS one allows us to do some smart stuff later, like generating parts of the config based on environment variables.

```js
// nightwatch.js
module.exports = {
  src_folders: ['test/e2e/'],
  ...
};
```

Find the rest of the configuration you need in Nightwatch’ developer guide. In the `default` configuration, just use the `browserName` corresponding to the Selenium webdriver you installed, e.g. `chrome`.

## Creating your first test

Time to write some code! Create a file `test/e2e/simple/simpleTest.js`and paste in something like this:

```js
// test/e2e/simple/simpleTest.js
module.exports = {
  beforeEach: browser => {
    browser
      .url('http://localhost:8000')
      .waitForElementVisible(‘body’)
      .waitForElementVisible(‘#app > div’);
  },
  ‘Smoke test’: browser => {
    browser
      .assert.visible(‘#app > div’, ‘Check if app has rendered with   React’)
      .assert.title(‘MyTitle’);
  },
  after: browser => browser.end(),
};
```

Thanks to the Nightwatch API, the code is pretty self explanatory. Replace the assertions with something that makes sense for you.

Assuming you already have your app running, go ahead and run your test, with `nightwatch --config nightwatch.js`.

![Running Nightwatch](/images/nightwatch.gif)

## Running Tests in the Cloud

Useful as it is to test whether Chrome can parse HTML, we’re now ready to 10x the awesomeness, using SauceLabs. Feel free to create an account at this point.

First, make some changes to the Nightwatch config:

```js
default: {
  launch_url: 'http://ondemand.saucelabs.com:80',
  selenium_port: 80,
  selenium_host: 'ondemand.saucelabs.com',
  silent: true,
  username: process.env.SAUCE_USERNAME,
  access_key: process.env.SAUCE_ACCESS_KEY,
  screenshots: {
    enabled: false,
    path: ‘’,
  },
  globals: {
    waitForConditionTimeout: 10000,
  },
},
```

(You can move your previous default config to one called e.g. “local”.)

Nightwatch needs your username and access key to connect to SauceLabs. We also use the opportunity to add a global default time for Nightwatch’ timeouts.

At the same level as the default configuration, add one or two browsers you want to test against:

```js
chrome: {
  desiredCapabilities: {
    browserName: 'chrome',
    platform: 'OS X 10.11',
    version: '47',
  },
},
ie11: {
  desiredCapabilities: {
    browserName: 'internet explorer',
    platform: 'Windows 10',
    version: '11.0'
  }
}
```

Set your SauceLabs username and access key environment variables with `export SAUCE_USERNAME <username>`.

For SauceLabs to be able to contact your server, install SauceConnect with `brew install sauce-connect`, then run it with `sc -u <USERNAME> -k <ACCESS_KEY>`.

This should be all it takes to run tests on a machine far, far away, somewhere in a cloud, and have it do stuff the server running right under your fingertips. Run `nightwatch --config nightwatch.js --env chrome`, then sit back and relax.

![Nightwatch on Saucelabs](/images/nightwatch-saucelabs.png)

## Run Your Tests from Travis

This step requires us to make Travis spawn both a server to host our app, and a tunnel to SauceLabs with SauceConnect. Worry not, because it's not that hard.

For more documentation, read [Travis' article](For more documentation, read Travis’ article.).

```yml
addons:
  sauce_connect: true
install:
  - nvm install 4.0 // if you're not using Node as language
  - npm install -g nightwatch
before_script:
  // This is our server
  - npm run serve:dist &
script:
  - nightwatch --config nightwatch.js --env chrome,ie11
  // This one feels hacky, but does the job of killing the server
  - pkill node
```

**Note:** If your server takes a very long time to load, it can be a good idea to add a “sleep 30” command after you start the server.

**Also note:** You probably have some other stuff you want travis to do. Do them before Nightwatch, as the tests involving SauceLabs are probably the most expensive. You’ll also give your server some time to build JavaScript.

For SauceLabs to connect to Travis, we need to send them a tunnel identifier. We’d also like SauceLabs to take a note of the Travis build number it is testing, so we also pass that one. Luckily, they’re both based on the same value.

```js
// nightwatch.js
const TRAVIS_JOB_NUMBER = process.env.TRAVIS_JOB_NUMBER;
// in test_settings.default:
default: {
  launc_url: 'http://ondemand.saucelabs.com:80',
  ...
  desiredCapabilities: {
    build: `build-${TRAVIS_JOB_NUMBER}`,
    'tunnel-identifier': TRAVIS_JOB_NUMBER,
  },
}
```

Congratulations, the next time you push your code, servers around the world will collaborate to make sure everything works as intended.

Gather some friends to watch in awe as results tick into the SauceLabs dashboard, with live videos and things flashing all over the screen.

## Make SauceLabs know whether or not it worked

You might have noticed the nice question mark next to each of your tests in SauceLabs. It appears because while it provided Nightwatch with a nice API for it to run commands an queries, it's completely oblivious to whether or not it was expected. In order to get some nice check marks (or red exclamation marks), we need to report SauceLabs about the outcomes.

[Here's an example implementation](https://gist.github.com/mikberg/ce463e09d6adf46f987c) in the form of a Gist, inspired by [this code](https://github.com/18F/college-choice/blob/8029b46f1283ed94c7f13662689374c399ff6740/test/sauce.js).

Edit your test to run the sauce function when it tears down:

```js
const sauce = require('../sauce');
module.exports = {
  //...
  tearDown: sauce,
};
```

Result:

![Saucelabs results](/images/saucelabs-results.png)

Awesome. Add some better tests, add more obscure browsers, like Android 4.0. Now, only be a little bit nervous about browsers on each commit, rather than terrified when releasing.
