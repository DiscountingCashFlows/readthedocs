The ``data`` Object
===================

The ``data`` object stores two types of data:

- **Reported data** from external providers (e.g., `financialmodelingprep.com`).
- **User-generated data** from calculations.

``data.compute()``
------------------

Calculates values using formulas referencing other keys or assumptions.

**Example:**

.. code-block:: python

   data.compute({
       "#bookValue": "balance:totalStockholdersEquity / income:weightedAverageShsOut",
       "#intermediaryVariable": f"#bookValue * income:eps * {assumptions.get('graham_multiplier')}",
       "#grahamNumber": "function:sqrt:#intermediaryVariable",
       "%revenueGrowthRate": "function:growth:income:revenue",
   })

Key Types
---------

- ``#``: Standalone units or per share items.
- ``%``: Percentages.
- Others: Formatted to millions or thousands in renderings.

Forecasting Values
------------------

.. code-block:: python

   assumptions.init({"projection_years": 5})

   data.compute({
       "income:revenue": "income:revenue:-1 * 1.1"
   }, forecast=assumptions.get("projection_years"))
