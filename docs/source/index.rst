Discounting Cash Flows Developer's Guide
========================================

This is the official guide for anyone interested in creating or editing valuation models within the Model Code Editor on `discountingcashflows.com <https://discountingcashflows.com/>`__.

.. note::

   The website is under active development. If you encounter outdated content or have any questions, please let us know `here <https://discountingcashflows.com/help/>`__.

Discounting Cash Flows (`discountingcashflows.com <https://discountingcashflows.com/>`__) is dedicated to fundamental stock analysis and valuation. We automate the process of retrieving financial data from various sources (such as SEC.gov, Yahoo Finance, etc.) and importing this data into financial valuation models, enabling investors to focus more on analyzing companies' fundamentals.

Our financial data is provided by `financialmodelingprep.com <https://site.financialmodelingprep.com/>`__. We use fully transparent and editable models written in JavaScript, processed directly in the user's web browser (Chrome, Firefox, Opera, etc.), to calculate the intrinsic value of companies.

Advantages
----------

- **Interactive valuation models**

  Users can adjust model assumptions with their own future estimations for companies. Changing these values automatically recalculates the models.

- **Shareable valuation models**

  Once users finalize their assumptions, they can easily copy and share the model's URL with others.

- **Editable valuation models**

  Users can fully customize the models using our built-in JavaScript code editor.

- **Price watches and alerts**

  Users can set up price alerts and receive notifications via email when their valuation models reach specific thresholds.

.. warning::

   Our website does **not** replace the **Due Diligence** required before making investment decisions, nor do we offer financial advice. We strongly recommend users thoroughly research each company, understand its business model, and develop their own projections before investing.

Model Code Editor
-----------------

This guide explains every function available within the Model Code Editor. Additionally, you can access the source code and detailed explanations for all valuation models, including how they are calculated, on our `GitHub documentation page <https://github.com/DiscountingCashFlows/Documentation/>`__.

Contents
--------

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   introduction
   data_map
   data_object
   assumptions_object
   model_object
