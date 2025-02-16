# ember-backstop

![Scooch and Tomster](https://github.com/garris/BackstopJS/blob/master/assets/scoochAndTomster.png?raw=true)

**BackstopJS visual regression testing addon for Ember.**

- Simple to add, Simple to use.
- Works in Local or CI pipeline.
- Manages your test and reference files.
- Works in your existing Acceptance and Integration tests.

TLDR;
------------------------------------------------------------------------------
QUICK TUTORIAL 👉 If you are new and just want a quick tutorial, try this step-by-step -- you'll be automatically diffing screenshots in about 5 mins!
https://github.com/garris/ember-backstop-tutorial/



Installation
------------------------------------------------------------------------------

### First do this...
```
ember install ember-backstop
```

### Next, for Git...
Add these lines to your `<project>/gitignore`...
```
  #backstopjs
  html_report
  bitmaps_test
```

### If you use Mirage 
Add this line to `<project>/mirage/config.json`...
```
  this.passthrough('/backstop/**');
```

### If you test with `ember test`
Add this to your `<project>/testem.js`...
```
  proxies: {
    '/backstop': {
      target: 'http://localhost:3000',
      secure: false,
    },
  },
```

Usage
------------------------------------------------------------------------------
### Backstop-remote service
You will need the backstop-remote service running for visual tests.

In a seperate window run...

```
ember backstop-remote
```

Leave that running while you're testing. When you don't need it anymore you can either close the window or run `ember backstop-stop` to stop the service.

### Running Ember Server
Ember-Backstop works with `ember test` and `ember serve` e.g.

```
ember test --server --filter="your_test_name_partial"
```

or

```
ember serve
```

### Adding a visual regression test is simple...
First import the backstop helper into your test...

```
import backstop from 'ember-backstop/test-support/backstop';
```

Then add an `await backstop(assert);` assertion to any UI state you want to test...

```
  test('it renders the thing', async function(assert) {
    await visit('/sales/company/11102');
    await backstop(assert);
    assert.dom('#myFancyElement').exists();
  });
```

Now, whenever you run a test, BackstopJS will take a screen shot and compare it against the last known good. 

### The first run
The first time you run backstop-helper tests BackstopJS will fail because there aren't any reference files yet. So, here's what you do...

1. run tests normally
2. When tests complete, in another window run `ember backstop-approve`

Your next test run should now pass.

### View the most recent BackstopJS test report
You can always view the last test run report by running the following in another window...

```
ember backstop-report
```


### Approving changes
Anytime you want to promote a changed test to the new reference file, run...

```
ember backstop-approve
```  

This command also takes an optional `filter` parameter e.g. 

```
ember backstop-approve --filter=testFilenameAsRegExString
```

### Configuration Options
The backstop helper takes an optional options object.

#### Scenario Option
You can configure BackstopJS scenario options for your tests dynamically by passing a `scenario` object in the options.

```
  test('it renders the thing - selects the .jumbo selector - and compares with a custom mismatch threshold', async function(assert) {
    await visit('/sales/company/11102');
    await backstop(assert, {scenario: {selectors: '.jumbo', misMatchThreshold: 0.05}});
    assert.dom('#myFancyElement').exists();
  });
```

See [Scenario Documention](https://github.com/garris/BackstopJS#advanced-scenarios) for what you can configure.

#### Name Option
You can add a custom name segment to your backstop test artifacts by passing a `name` property in the options.
```
  test('shows specific rental details', async function(assert) {
    await visit('/rentals');
    await click(".grand-old-mansion");
    await backstop(assert);
    await backstop(assert, {name: 'WITH A CUSTOM NAME'});
  });
```
The above produces two identical backstop tests with the following titles...
```
1) Acceptance__list_rentals__shows_specific_rental_details__assert0_0_document_0_webview
2) Acceptance__list_rentals__shows_specific_rental_details__WITH_A_CUSTOM_NAME__assert1_0_document_0_webview
```




### More Info
See [http://backstopjs.org](http://backstopjs.org) for documentation on BackstopJS -- but keep in mind -- for this implementation all DOM interactions should probably be done in your Ember test -- and not the BackstopJS config.

### Issues

#### If your screenshots are blank
Take a look at your test network calls.  Are you seeing `UnrecognizedURLError` errors?  If so, there may be an issue with a middleware addon dependency loading too early. Try ensuring any server dependencies don't block proxy requests to `/backstop/`. Post the issue [here](https://github.com/garris/ember-backstop/issues) and let us know! 

#### Questions/comments? 
Post an issue, propose a feature or just say Hi!  https://github.com/garris/ember-backstop/issues


Contributing
------------------------------------------------------------------------------

Yes. Please pitch in to make this addon awesome for everyone.


Compatibility
------------------------------------------------------------------------------

* Ember.js v2.18 or above
* Ember CLI v2.13 or above
* Node.js v8 or above


License
------------------------------------------------------------------------------

This project is licensed under the [MIT License](LICENSE.md).
