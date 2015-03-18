# ajaxMultipleRequest
Handling multiple AJAX request using $.when and $.apply

Usage(multiple request using array placeholders)
===================================
    var list = ['obj1', 'obj2', 'obj3', 'obj4', 'obj5']; //<---- List of object 
    var callback = function() { //<--- Another callback returned after success
      console.log("done");
    };
      var requests = []; //<--- placeholder to put ajax request code
    
      for(i = 0; i < list.length; i++) { //<--- iterate obj list length
        requests.push($.ajax({   //<--- Insert an Ajax request into array placeholder
				         url: 'url',
				         success: function() {
				           console.log('suc');
                         }
                      }));
      }
    
    $.when.apply(undefined, requests).then(function(results){callback()});  
    //<--- Execute those ajax request(array form), then return promise after success of each


Old way(very slow performance)
=====================================
Imagine to request three source using old way
**Example 1 :**

    // Get the HTML
    $.get("/feature/", function(html) {
    
      // Get the CSS
      $.get("/assets/feature.css", function(css) {
    
    	// Get the JavaScript
	    $.getScript("/assets/feature.js", function() {
	    
	       // All is ready now, so...
	    
	       // Add CSS to page
	       $("<style />").html(css).appendTo("head");
	    
	       // Add HTML to page
	       $("body").append(html);
	    
	    });
    
      });
    
    });

old way **Example 2 :**

    function getLocation() {
	    $.get("http://ipinfo.io", function (response1) {
		    if (response1) {
			    $.get("http://ipinfo.io", function (response2) {
				    if (response2) {
					    $.get("http://ipinfo.io", function (response3) {
						    if (response3) {
						    // handle the rest
						    }
						    }, 'jsonp');
					    }
				    }, 'jsonp');
			    }
	    }, 'jsonp');
    }

Suggested Way(fast performance)
================================
Instead of waiting one request to be done, what about request it simultaneous, and populate promise then. 
Solution for **example 1:** 

    $.when(
      // Get the HTML(first request)
      $.get("/feature/", function(html) {
    globalStore.html = html;
      }),
    
      // Get the CSS(second request)
      $.get("/assets/feature.css", function(css) {
    globalStore.css = css;
      }),
    
      // Get the JS(third request)
      $.getScript("/assets/feature.js")
    
    ).then(function(data1, data2, data3) {
    
      // All is ready now, so...
    
      // Add CSS to page
      $("<style />").html(globalStore.css).appendTo("head");
    
      // Add HTML to page
      $("body").append(globalStore.html);
    
    });
`then(function(data1, data2, data3)` Each returned Promise object is an array that contains, in the following order:
- data that was returned (if any)
- status of the promise object
- the jqXHR (jQuery XMLHttpRequest) object returned from the ajax call

Solution for **example 2:**

    var deferred = $.Deferred();

    function getLocation() {

	    var ajaxCall1 = $.get("http://ipinfo.io", {}, null, 'jsonp');
	    var ajaxCall2 = $.get("http://ipinfo.io", {}, null, 'jsonp');
	    var ajaxCall3 = $.get("http://ipinfo.io", {}, null, 'jsonp');

	    $.when(ajaxCall1, ajaxCall2, ajaxCall3)
	     .done(function (response1, response2, response3) {
	        if (response1[0].country === 'US') {
	            deferred.resolve();
	        } else {
	            deferred.reject(response1[0], response2[0], response3[0]);
	        }
	    });
	
	     return deferred.promise();
    }

    getLocation()
    .then(
    function () {
        alert('You are in US');
    }, 
    function (response1, response2, response3) {
        alert('You are in ' + response1.city
                     + ', ' + response2.region
                     + ', ' + response3.country);
    }
    );




Sources
============
1) http://geniuscarrier.com/using-jquery-deferred-with-multiple-ajax-calls/