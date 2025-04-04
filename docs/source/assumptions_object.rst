The ``assumptions`` Object
==========================

Manage model assumptions easily.

``assumptions.init()``
----------------------

Initialize assumptions:

.. code-block:: python

   assumptions.init({
       "%growth_rate": "5%",
       "historical_years": 10
   })

With hierarchies:

.. code-block:: python

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

Other Functions
---------------

- ``assumptions.get()``
- ``assumptions.set()``
- ``assumptions.set_description()``
