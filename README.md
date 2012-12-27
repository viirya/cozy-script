
# CozyScript

CozyScript is a fork of CoffeeScript. It currently adds the syntax of backcall function.

## Backcall examples

-- CozyScript --

    <- $ 1, 2, _
      console.log('test')

    (x) <- $ 1, _, 2
      console.log('test')

    <- $()
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
