
The ``assumptions`` object
--------------------------

``assumptions.init()``
~~~~~~~~~~~~~~~~~~~~~~

Initializes assumptions for your model. You can set a hierarchy of
assumptions for structured relationships.

Example (Without Hierarchies):
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   assumptions.init({
       "%growth_rate": "5%",  # Strings that denote percentages are allowed
       "historical_years": 10
   })

Example (With Hierarchies):
^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

.. _example-8:

Example:
^^^^^^^^

.. code:: python

   growth_rate = assumptions.get("%growth_rate")  # Get the growth rate

--------------

``assumptions.set()``
~~~~~~~~~~~~~~~~~~~~~

This function sets the value of a specific assumption.

.. _example-9:

Example:
^^^^^^^^

.. code:: python

   assumptions.set("%growth_rate", 0.07)  # Set growth rate to 7%

--------------

``assumptions.set_description()``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sets a description for assumptions, providing context or explanations.

.. _example-10:

Example:
^^^^^^^^

.. code:: python

   assumptions.set_description({
       "%growth_rate": "The expected annual growth rate of revenues."
   })
