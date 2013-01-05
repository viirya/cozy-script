# CozyScript

CozyScript is a fork of CoffeeScript. It currently adds some features from the descendants of CoffeeScript
such as LiveScript. Those features include backcall function, implicit function call.

## Backcall examples

-- CozyScript --

    <- $ 1, 2, _
      console.log('test')

    (x) <- $ 1, _, 2
      console.log('test')

    <- $()
      console.log('test')

    x <- func 1, _, 2
      console.log('test')

    <- func_one()
      console.log('test')

      x <- func 1, _, 2
        console.log('test')



    phantom = require "phantom"
      (ph) <- phantom.create()
        (page) <- ph.createpage()
          (status) <- page.open google
            console.log "opened google? #{status}"
            page.evaluate (-> document.title), (it) ->
              console.log "page title is #{it}"
              ph.exit



-- Compile to JavaScript --

    $(1, 2, function() {
      return console.log('test');
    });

    $(1, function(x) {
      return console.log('test');
    }, 2);

    $(function() {
      return console.log('test');
    });

    func(1, function(x) {
      return console.log('test');
    }, 2);

    func_one(function() {
      console.log('test');
      return func(1, function(x) {
        return console.log('test');
      }, 2);
    });

    var phantom;
    phantom = require("phantom");

    phantom.create(function(ph) {
      return ph.createpage(function(page) {
        return page.open(google, function(status) {
          console.log("opened google? " + status);
          return page.evaluate((function() {
            return document.title;
          }), function(it) {
            console.log("page title is " + it);
            return ph.exit;
          });
        });
      });
    });

## Implicit function call

-- CozyScript --

    func = (one, two, three) -> one: '1', two: '2'

    func()
    func!

    eq func().two, '2'
    eq func!.two, '2'

    func_array = -> [1, 2, 3]

    eq func_array()[0], 1
    eq func_array![0], 1

-- Compile to JavaScript --

    func = function(one, two, three) {
        return {
            one: '1',
            two: '2'
        };
    };
    
    func();

    func();

    eq(func().two, '2');

    eq(func().two, '2');

    func_array = function() {
        return [1, 2, 3];
    };

    eq(func_array()[0], 1);

    eq(func_array()[0], 1);

### Currying function

-- CozyScript --

    times = (x, y) --> x * y
    
-- Compile to JavaScript --
    
    times = curry$(function(x, y) {
      return x * y;
    });

    function curry$(f, args){     # Currying javascript codes from LiveScript
      return f.length > 1 ? function(){
        var params = args ? args.concat() : [];
        return params.push.apply(params, arguments) < f.length && arguments.length ?
        curry$.call(this, f, params) : f.apply(this, params);
      } : f;
    };

### Partially applied function

-- CozyScript --

    filterNum = filter _, [1..5]
    
    filterNum (it) ->  # [4, 5]
      it > 3

-- Compile to JavaScript --
    
    filterNum = __partialize.apply(this, [filter, [void 0, [1, 2, 3, 4, 5]], [0]]);
    
    filterNum(function(it) {
        return it > 3;
    });

    __partialize = function partialize$(f, args, where){  # particalize$() javascript codes from LiveScript
      var context = this;
      return function(){
        var params = __slice.call(arguments), i,
            len = params.length, wlen = where.length,
            ta = args ? args.concat() : [], tw = where ? where.concat() : [];
        for(i = 0; i < len; ++i) { ta[tw[0]] = params[i]; tw.shift(); }
        return len < wlen && len ?
          partialize$.apply(context, [f, ta, tw]) : f.apply(context, ta);
      };
    }


