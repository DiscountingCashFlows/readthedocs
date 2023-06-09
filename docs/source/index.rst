Discounting Cash Flows Developer's Guide
========================================

This is the official guide for anyone interested in creating or editing valuation models inside the website's (discountingcashflows.com) Model Code Editor.

.. note::

   Please note that the website is under active development. If you find any outdated content or have any questions, please let us know `here <https://discountingcashflows.com/help/>`__

Discounting Cash Flows (`discountingcashflows.com <https://discountingcashflows.com/>`__) is dedicated to fundamental stock analysis and valuation. We try to automate the process of retrieving data from financial sources(sec.gov, Yahoo Finance, etc.) and importing the data into a financial valuation model, allowing investors to focus more on studying fundamental aspects of companies.

Our data is provided by `financialmodelingprep.com <https://site.financialmodelingprep.com/>`__ and we use fully transparent and editable models written in the JavaScript programming language to process the data and output the intrinsic value of a company. The valuation models are evaluated by the client's own browser(Chrome, Firefox, Opera, etc.)

Check out the our Code Editor YouTube Video Tutorial

.. raw:: html

    <div>
        <iframe width="560" height="315" src="https://www.youtube.com/embed/jkWqcBCTzbA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
    </div>

Advantages
---------------------

The valuation models are interactive
   The user can change the model's assumptions with his own future estimations for the company.
   Changing values makes the model recalculate itself automatically.
The valuation models are shareable
   Once the user is done changing the model's assumptions, they can just copy the URL and give it to anyone.
The valuation models are editable
   The user can customize the models as they wish using our built-in JavaScript code editor.
The user can set watches and price alerts
   We will let the user know by email when his model reaches a value threshold.

.. warning::

   Please note that our website does not try to bypass the **Due Diligence** required before commiting to purchase a company and does not offer financial advice. We recommed users to get informed about the company and its business model and try to come up with their own projections for each company.

Model Code Editor
---------------------

We will go through every function available to the editor. You can also find the source code and description for all our valuation models and how they are calculated `here <https://github.com/DiscountingCashFlows/Documentation/>`__

Contents
--------

.. toctree::

   usage
   functions
   api
