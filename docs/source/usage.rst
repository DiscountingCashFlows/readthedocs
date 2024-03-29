Using the Code Editor
====================

Make sure you have an active account, or make one to access the `Code Editor <https://discountingcashflows.com/valuation/>`__

.. _base-code:

Example Code for a Model
------------------------

Start a new model by going to the Code Editor where we will be writing our valuation model.

Copy the following base code into the editor:

.. code-block:: javascript

  Input(
    {
      NUMBER: 5,
      _RATE: ''
    }
  );
  $.when(
    get_profile()).done(
    function(_profile){
    try{
      // We need to make sure we don't edit the _profile variable
      // Editing it could cause caching problems in the Watchlist page
      var response = new Response({
        profile: _profile,
      });
      
      // Use console.log() to inspect the data
      // Press F12 or right-click to inspect the Console
      // You will see an exapandable object (profile)
      console.log(response.profile);

      // Print messages to the screen
      print("Hello World!", "First Message");
      print(response.profile.companyName, "Company's Name");
      print(response.profile.price, "Stock Price", "#", response.profile.currency);
      print(response.profile.website, "Company's Website");
    }
    catch (error) {
      throwError(error);
    }
  });

Running & Saving the Code
-------------------------

To run your code, you need to fill in a Ticker (Example: ``AAPL`` for Apple Inc.) and press Run. You can also name your model and save it.

This retrieves the company's profile data and prints out a ``Hello World`` message, the company's name, the last stock price and the company's website.

Press F12 or right-click and Inspect the page, and you will see an expandable object. This is the profile object that we have logged using ``console.log(profile);``.
If you expand it, you will see all the properties the object has. These properties are information about the company such as the company's full name (``profile.companyName``).

The following will be printed in the Terminal:

>>> First Message: Hello World! 
>>> Company's Name: Apple Inc. 
>>> Stock Price: 148.79 
>>> Company's Website: https://www.apple.com 
