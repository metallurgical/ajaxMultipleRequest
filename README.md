# ajaxMultipleRequest
Handling multiple AJAX request using $.when and $.apply

Usage
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