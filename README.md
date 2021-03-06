# Lithium for jQuery

Lithium adds the following useful APIs to be used along with jQuery:

1. Data type assertion: isDefined,isElement...
2. Things like bind,inherit,namespace...
3. Observable (Publisher-subscriber pattern) that can be used to communicate between loosly coupled modules/components.
3. IE8 JS 1.6 and JS 1.8 polyfills, like string trim, array forEach,lastIndexOf,filter,reduce...
4. Browser detection (which is still useful in rare cases..like for statistics).

Lithium depends on jQuery. Lithium is seperated into modules, so you use only what you need.

## Browser support

Latest Chrome,Firefox,Safari,Opera and IE8+.

## API

[Complete API documentation](http://munawwar.github.com/Lithium/doc/).

### Data types

Li.isDefined(val) - Returns true if val isn't undefined.

Li.isElement(o) - Returns true if o is an instance of HTMLElement

Li.isNaN(val) - Returns true if val is NaN.

Similarly Li.isObject, Li.isString, Li.isBoolean.

jQuery already has $.isFunction, $.isNumeric etc.

### Patterns

* Li.namespace(string) - Creates a global namespace.

  ``Li.namespace('app.utils');``

* Li.extend(base, obj) - Classical inheritence

    <pre><code>var myClass1 = Li.extend(Object, {
        constructor: function (cfg) {
            $.extend(this, cfg);
        },
        prop: "Lithium",
        method: function () { return 1;},
        statics: { //Special property to defined static methods/properties
            staticProp: "prop"
        }
    });
    
    //Create myClass2 using myClass1 as base class.
    var myClass2 = Li.extend(myClass1, {
        constructor: function (cfg) {
            this.super([cfg]); //call base class constructor
            
            //alternatively, this.super(arguments);
            //or this.superClass().constructor.call(this, cfg);
        },
        //Override 'method'
        method: function () {
            //Add 1 to the result of base class 'method'.
            return 1 + this.super(arugments);
        }
    });</code></pre>

* Observable

    <pre><code>var Restaurant = Li.extend(Object, {
        //Methods
        salesOffer: function () {
            this.fireEvent('freefood', '1.00 PM');
        }
    });
    Li.observable(Restaurant, ['freefood']); //Make class a publisher

    /*Subscriber/Listener*/
    var HungryMan = Li.extend(Object, {
        constructor: function (name, restaurant) {
            this.name = name;
            //Add listener
            restaurant.on('freefood', function (time) {
                console.log(name + ' says: Yay! free food!');
            }, this);
        }
    });
    /*----------------------------------*/

    /*Demonstration*/
    /*----------------------------------*/
    var someRestaurant = new Restaurant();
    var a = new HungryMan('man1', someRestaurant),
        b = new HungryMan('man2', someRestaurant);

    //Somewhere in a onclick event we execute...
    someRestaurant.salesOffer(); //...this would call all listeners. In this case it will display..
    //man1 says: Yay! free food!
    //man2 says: Yay! free food!</code></pre>

* Li.lbind(fn [, context, args...]) - Binds context and arguments to a function (like the [JS.1.8.1 Function.bind](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Function/bind)). Argument list is prepended to fn.

    <pre><code>element.onclick = Li.lbind(function (val, e) {
      console.log(this ===  element); //true
      console.log(val); //10
      console.log(e); //If IE9+, you'll get event.
    }, element, 10);</code></pre>

* Li.rbind - Same as lbind, except that arguments are appended to fn arugment list.

* Li.forEach(obj [, callback, context]) - forEach on any object. For arrays, Array.forEach is called internally.
* Li.uuid([len=10, hypenate=false]) - Returns a random UID with length 'len' and hyphenated if hypenate=true, as string.
* Li.object.value(obj) - Returns all values of an object. Object.keys(obj) would return keys of an object.
* Li.object.size(obj) - Returns the number of enumerable properties of the object.
* Li.string.htmlEncode and Li.string.htmlDecode - Encodes/Decodes >,<," and &.
* Li.format(formatString, ...) - A quick string format method
  
  <pre><code>Li.format('&lt;div class="{0}"&gt;&lt;/div&gt;, 'box');
  Li.format('&lt;div class="{cls}"&gt;&lt;/div&gt;, {cls: 'box'});
  //Both returns '&lt;div class="box"&gt;&lt;/div&gt;'</code></pre>

* Li.dom(htmlString, ...) - Converts htmlString to DOM, inserts them into a document fragment and returns the fragment.
  Internally this uses Li.format for string formatting.

  <pre><code>var df = Cu.dom('&lt;div class="{cls}" data-id="{id}"&gt;&lt;/div&gt;', {cls: 'box', id: Cu.uuid()}); //DocumentFragment
  document.body.appendChild(df);</code></pre>

### Browser Detection

<pre><code>Li.isIE - will be set when browser is MS IE.
Li.isIE9 - will be set when browser is MS IE 9.
Li.isChrome
Li.isWebKit
...similar for other browsers and versions
Li.isAndroid
Li.isIPhone
Li.isIPad
Li.isMobile - True if iPhone, Android, BlackBerry (Phone), Windows Phone or Symbian.

Additionally:
Li.browser.name - e.g. 'IE'
Li.browser.version - e.g. '9'
Li.browser.OS - e.g. 'Windows'
Li.browser.OSVersion (set if available) - e.g. '6.1'
</code></pre>
