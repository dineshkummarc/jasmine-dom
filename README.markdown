# jasmine-dom

jasmine-dom was inspired by the [jasmine-jquery add-on](http://github.com/velesin/jasmine-jquery) for [Jasmine](http://pivotal.github.com/jasmine/) JavaScript Testing Framework. This add-on provides a set of custom matchers for working with DOM nodes and an API for handling HTML fixtures in your spec.

## Installation

This add-on has been separated into two parts: HTML fixtures in `jasmine-dom-fixtures.js` and the custom DOM matchers in `jasmine-dom-matchers.js`.

Simply download these files from the [downloads page](http://github.com/jeffwatkins/jasmine-dom/downloads) and include them in your Jasmine's test runner file (or add it to _jasmine.yml_ file if you're using Ruby with [jasmine-gem](http://github.com/pivotal/jasmine-gem)).

You can use one or both of these files, depending on your needs.

## Fixtures

Fixture module of jasmine-dom allows you to load HTML content to be used by your tests. The overall workflow is like follows:

In _myfixture.html_ file:
    <div id="my-fixture">some complex content here</div>
    
Inside your test:

    loadFixtures('myfixture.html');
    var node=document.getElementById('my-fixture');
    // ... call your code with node
    expect(node).to...;
    
Your fixture is being loaded into `<div id="jasmine-fixtures"></div>` container that is automatically added to the DOM by the Fixture module (If you _REALLY_ must change id of this container, try: `jasmine.getFixtures().containerId = 'my-new-id';` in your test runner). To make tests fully independent, fixtures container is automatically cleaned-up between tests, so you don't have to worry about left-overs from fixtures loaded in preceeding test. Also, fixtures are internally cached by the Fixture module, so you can load the same fixture file in several tests without penalty to your test suite's speed.

To invoke fixture related methods, obtain Fixtures singleton through a factory and invoke a method on it:

    jasmine.getFixtures().load(...);
    
There are also global short cut functions available for the most used methods, so the above example can be rewritten to just:

    loadFixtures(...);
    
Several methods for loading fixtures are provided:

- `load(fixtureUrl[, fixtureUrl, ...])`
    Loads fixture(s) from one or more files and automatically appends them to the DOM (to the fixtures container).
- `read(fixtureUrl[, fixtureUrl, ...])`
    Loads fixture(s) from one or more files but instead of appending them to the DOM returns them as a string (useful if you want to process fixture's content directly in your test).
- `set(html)`
    Doesn't load fixture from file, but instead gets it directly as a parameter. Automatically appends fixture to the DOM (to the fixtures container). It is useful if your fixture is too simple to keep it in an external file or is constructed procedurally, but you still want Fixture module to automatically handle DOM insertion and clean-up between tests for you.
  
All of above methods have matching global short cuts:

- `loadFixtures(fixtureUrl[, fixtureUrl, ...])`
- `readFixtures(fixtureUrl[, fixtureUrl, ...])`
- `setFixtures(html)`

Also, a helper method for creating HTML elements for your tests is provided:

- `sandbox([{attributeName: value[, attributeName: value, ...]}])`

It creates an empty DIV element with a default id="sandbox". If a hash of attributes is provided, they will be set for this DIV tag. If a hash of attributes contains id attribute it will override the default value. Custom attributes can also be set. So e.g.:

    sandbox();
    
Will return:

    <div id="sandbox"></div>    
    
And:

    sandbox({
      id: 'my-id',
      class: 'my-class',
      myattr: 'my-attr'
    });
    
Will return:

    <div id="my-id" class="my-class" myattr="my-attr"></div>

Sandbox method is useful if you want to quickly create simple fixtures in your tests without polluting them with HTML strings:

    setFixtures(sandbox({class: 'my-class'}));
    //  .. run your code
    expect(document.getElementById('sandbox')).not.toHaveClass('my-class');

This method also has a global short cut available:

- `sandbox([{attributeName: value[, attributeName: value, ...]}])`

Additionally, two clean up methods are provided:

- `clearCache()`
  purges Fixture module internal cache (you should need it only in very special cases; typically, if you need to use it, it may indicate a smell in your test code)
- `cleanUp()`
  cleans-up fixtures container (this is done automatically between tests by Fixtures module, so there is no need to ever invoke this manually, unless you're testing a really fancy special case and need to clean-up fixtures in the middle of your test)
  
These two methods do not have global short cut functions.


## DOM matchers

jasmine-dom provides following custom matchers (in alphabetical order):

- `toBeChecked()`: Tests whether the node has a checked attribute set to `true`.

        var node= sandbox('<input type="checkbox" checked="checked">');
        expect(node).toBeChecked()
        
- `toBeEmpty()`: Tests whether the node is empty. A node is only empty if it has no child nodes.
- `toBeHidden()`: Tests whether the node is hidden. A node is considered hidden if both its `offsetWidth` and `offsetHeight` are 0. This may be caused by a display value of `none` either on the node or an ancestor node, or because the size of this node has been explicitly set to 0x0.
- `toBeSelected()`: For nodes with a selected attribute, this matcher tests whether the attribute is set to `true`.

        var node= sandbox('<option selected="selected"></option>');
        expect(node).toBeSelected()
        
- `toBeVisible()`: This matcher is the inverse of `toBeHidden`. If a node has a dimensions other than 0, it is visible.

- `toContain(selector)`: Tests whether this node contains nodes that match the selector.

        var node= sandbox('<div>
  - e.g. `expect(node).toContain('span.some-class')`
- `toExist()`
- `toHaveAttr(attributeName, attributeValue)`
  - attribute value is optional, if omitted it will check only if attribute exists
- `toHaveClass(className)`
  - e.g. `expect($('<div class="some-class"></div>')).toHaveClass("some-class")`  
- `toHaveHtml(string)`
  - e.g. `expect($('<div><span></span></div>')).toHaveHtml('<span></span>')`
- `toHaveId(id)`
  - e.g. `expect($('<div id="some-id"></div>')).toHaveId("some-id")`
- `toHaveText(string)`
  - e.g. `expect($('<div>some text</div>')).toHaveText('some text')`
- `toHaveValue(value)`
  - only for tags that have value attribute
  - e.g. `expect($('<input type="text" value="some text"/>')).toHaveValue('some text')`
- `toMatchSelector(selector)`
  - e.g. `expect(node).toMatchSelector('div#some-id')`
 
The same as with standard Jasmine matchers, all of above custom matchers may be inverted by using `.not` prefix, e.g.:

    expect(node).not.toHaveText('other text')

## Supported browsers and jQuery versions

jasmine-jquery was tested for jQuery 1.4 on IE, FF, Chrome and Opera.
