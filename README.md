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


Suggested Way(fast performance)
================================
Instead of waiting one request to be done, what about request it simultaneous, and populate promise then.

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
    
    ).then(function() {
    
      // All is ready now, so...
    
      // Add CSS to page
      $("<style />").html(globalStore.css).appendTo("head");
    
      // Add HTML to page
      $("body").append(globalStore.html);
    
    });
