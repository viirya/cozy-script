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


    






