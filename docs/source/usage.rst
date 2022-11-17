Using the Code Editor
====================

Make sure you have an active account, or make one to access the `Code Editor <https://discountingcashflows.com/valuation/>`__

.. _base-code:

Base Code for a Model
---------------------

Start a new model by going to the Code Editor.
Here, we will be writing our valuation model.
Copy the following base code into the editor:

.. code-block:: console

var INPUT = Input({NUMBER: 5,
                  _RATE: ''}); 
$.when(
  get_profile()).done(
  function(_profile){
    // The context array is used to hold charts, tables and prints information.
    var context = [];
    // We need to make sure we don't edit the _profile variable
    // Editing it could cause caching problems in the Watchlist page
    var profile = JSON.parse(JSON.stringify(_profile));
    profile = profile[0];
    // Use console.log() to inspect the data
    // Example: console.log(profile);
    
    
    
    monitor(context);
});

Creating recipes
----------------

To retrieve a list of random ingredients,
you can use the ``lumache.get_random_ingredients()`` function:

.. autofunction:: lumache.get_random_ingredients

The ``kind`` parameter should be either ``"meat"``, ``"fish"``,
or ``"veggies"``. Otherwise, :py:func:`lumache.get_random_ingredients`
will raise an exception.

.. autoexception:: lumache.InvalidKindError

For example:

>>> import lumache
>>> lumache.get_random_ingredients()
['shells', 'gorgonzola', 'parsley']

