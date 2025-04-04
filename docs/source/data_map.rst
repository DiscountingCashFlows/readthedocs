Reported Data Map
=================

When using reported data, it's essential to understand the structure of the data map. Parent keys map to child keys, allowing you to access specific financial metrics.

Example:

- To access "EPS" (Earnings Per Share), you use: ``income:eps``.

Full Reported Data Map
----------------------

.. code-block:: json

   {
       "balance": ["cashAndCashEquivalents", "..."],
       "dividend": ["adjDividend"],
       "economic": ["cpi"],
       "flow": ["netIncome", "..."],
       "income": ["revenue", "..."],
       "profile": ["price", "..."],
       "quote": ["close"],
       "ratio": ["dividendYield", "..."],
       "risk": ["defaultSpread", "..."],
       "treasury": ["month1", "..."]
   }

*Abbreviated here for brevity. You can find the full JSON data map* `here <https://discountingcashflows-beta.com/documentation/model-editor-guide/#the-reported-data-map>`_
