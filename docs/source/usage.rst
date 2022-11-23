Using the Code Editor
====================

Make sure you have an active account, or make one to access the `Code Editor <https://discountingcashflows.com/valuation/>`__

.. _base-code:

Base Code for a Model
---------------------

Start a new model by going to the Code Editor where we will be writing our valuation model.

Copy the following base code into the editor:

.. code-block:: javascript

  var INPUT = Input({NUMBER: 5,
                     _RATE: ''});
  $.when(
    get_profile()).done(
    function(_profile){
      // We need to make sure we don't edit the _profile variable
      // Editing it could cause caching problems in the Watchlist page
      var profile = JSON.parse(JSON.stringify(_profile));
      profile = profile[0];

      // Use console.log() to inspect the data
      console.log(profile);

      // Print messages to the screen
      print("Hello World!", "First Message");
      print(profile.companyName, "Company's Name");
      print(profile.price, "Stock Price", "#", profile.currency);
      print(profile.website, "Company's Website");
  });

Running & Saving the Code
-------------------------

To run your code, you need to fill in a Ticker (Example: ``AAPL`` for Apple Inc.) and press Run. You can also name your model and save it.

This retrieves the company's profile data and prints out a ``Hello World`` message, the company's name, the last stock price and the company's website.

Press F12 or right-click and Inspect the page, and you will see an expandable object. This is the profile object that we have logged using ``console.log(profile);``.
If you expand it, you will see all the properties the object has. These properties are information about the company such as the company's full name. We printed it using ``profile.companyName``.

The following will be printed in the Terminal:

>>> First Message: Hello World! 
>>> Company's Name: Apple Inc. 
>>> Stock Price: 148.79 
>>> Company's Website: https://www.apple.com 


