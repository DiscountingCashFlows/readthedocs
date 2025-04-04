Introduction
============

Our `editor <https://discountingcashflows-beta.com/valuation/>`_ runs on **Python 3.10** and uses a custom financial modeling framework documented here.

This framework helps you analyze the true value of a stock by projecting future cash flows and calculating key financial metrics. You can work with historical data, set your own assumptions, and visualize results using intuitive charts and tables—all designed to make financial analysis easier and more insightful.

.. note::
   If you come across any bugs or inconsistencies, feel free to reach out to us via our `Help Page <https://discountingcashflows.com/help/>`_.

Good to know
------------

The model processes only annualized and LTM (Last Twelve Months) data for now. This means requesting EPS for last year, such as:

.. code-block:: python

   data.get("income:eps:-1")

returns the **annual**, not quarterly, EPS.
