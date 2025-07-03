The ``assumptions`` object
--------------------------

``assumptions.init()``
~~~~~~~~~~~~~~~~~~~~~~

Initializes assumptions for your model. You can set a hierarchy of
assumptions for structured relationships.

Example (Without Hierarchies)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   assumptions.init({
       "%growth_rate": "5%",  # Strings that denote percentages are allowed
       "historical_years": 10
   })

Example (With Hierarchies)
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   assumptions.init({
       "data": {
           "%discount_rate": None,
           "beta": data.get("profile:beta", default=1),
           "%risk_free_rate": data.get("treasury:year10"),
           "%market_premium": data.get("risk:totalEquityRiskPremium"),
       },
       "hierarchies": [{
           "parent": "%discount_rate",
           "children": ["beta", "%risk_free_rate", "%market_premium"]
       }]
   })

In this example, ``%discount_rate`` is the parent assumption, while
``beta``, ``%risk_free_rate`` and ``%market_premium`` are its children.

**Note:** Percentage assumptions, that start with ``%``, can be
specified either through a string like ``"5%"`` or the value directly
``0.05``

--------------

``assumptions.get()``
~~~~~~~~~~~~~~~~~~~~~

Fetches the value of a specified assumption. Raises an error if None.

Example of using ``assumptions.get()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   growth_rate = assumptions.get("%growth_rate")  # Get the growth rate

--------------

``assumptions.set()``
~~~~~~~~~~~~~~~~~~~~~

This function sets the value of a specific assumption.

Example of using ``assumptions.set()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   assumptions.set("%growth_rate", 0.07)  # Set growth rate to 7%

--------------

``assumptions.set_bounds()``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This function sets the bounds or limits of a specified assumption.

Example of using ``assumptions.set_bounds()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

    assumptions.set_bounds(
        "%growth_rate", low="5%", high="25%"  # Limits growth rate between 5% and 25%
    )
    assumptions.set_bounds(
        "eps", low=1, high=5
    )

--------------

``assumptions.set_description()``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sets a description for assumptions, providing context or explanations.

Example of using ``assumptions.set_description()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   assumptions.set_description({
       "%growth_rate": "The expected annual growth rate of revenues."
   })