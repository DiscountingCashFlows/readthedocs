The ``model`` object
--------------------

``model.render_results()``
~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``model.render_results()`` function is used to display results in a
structured format. It shows the calculated values along with their
corresponding labels, making it easy for users to interpret the outcomes
of your financial model.

Parameters
^^^^^^^^^^

The function takes a list of results, where each result is structured as
follows:

-  **Value**: The value to be displayed (e.g., a calculated metric).
-  **Label**: A string that describes the value (e.g., “Net Income”).
-  **Data Type**: A string indicating the type of data, which affects
   how the value is formatted for display. Common types include:

   -  **“$”**: Indicates that the value is a currency (e.g., dollars).
      This will format the number appropriately, often with commas and
      two decimal places.
   -  **“%”**: Indicates that the value is a percentage. This will
      display the number as a percentage, typically multiplying the
      underlying value by 100 and appending a “%” sign.

Example 1: Currency Formatting
''''''''''''''''''''''''''''''

.. code:: python

   model.render_results([
       [data.get("income:revenue"), "Total Revenue", "$"],
       [data.get("income:netIncome"), "Net Income", "$"],
       [data.get("income:eps"), "Earnings Per Share", "$"]
   ])

-  **Total Revenue** and **Net Income** are formatted as currency,
   meaning they will display as, for example, **2.5 Bill. USD**, **1
   Mil. CAD** and **250 Thou. EUR** and so on.

Example 2: Percentage Formatting
''''''''''''''''''''''''''''''''

.. code:: python

   model.render_results([
       [data.get("ratio:dividendYield"), "Dividend Yield", "%"],
       [data.get("ratio:netProfitMargin"), "Profit Margin", "%"]
   ])

-  In this case, **Dividend Yield** and **Profit Margin** are displayed
   as percentages. For instance, if **Dividend Yield** is 0.05, it will
   be shown as **5%**. Similarly, if the **Profit Margin** is 0.15, it
   will be displayed as **15%**.

Summary of ``model.render_results``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The third parameter in ``model.render_results()`` allows you to control
the formatting of the displayed values. By using **“$”** for currency
and **“%”** for percentages, you ensure that the results are presented
in a clear and understandable manner, appropriate for financial
analysis.

--------------

``model.render_chart()``
~~~~~~~~~~~~~~~~~~~~~~~~

The ``model.render_chart()`` function is used to create visual
representations of financial metrics, helping to illustrate trends and
comparisons over time. This function allows you to specify which data to
visualize and configure various properties of the chart.

Parameters of ``model.render_chart()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The function accepts a dictionary containing the following keys:

-  **data**: A dictionary mapping data keys to their respective labels.
   This defines what metrics will be included in the chart.
-  **start**: An integer representing the starting point for the x-axis
   (the number of years from the current year).
-  **end**: An integer or \* (\* means all available years) representing
   the ending point for the x-axis (the number of years from the current
   year).
-  **properties**: A dictionary of settings that customize the chart’s
   appearance and behavior.

Example of using ``model.render_chart()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   model.render_chart({
       "data": {
           "income:revenue": "Revenue",
           "income:netIncome": "Net Income"
       },
       "start": -5,  # Last 5 years
       "properties": {
           "title": "Revenue and Net Income Over Time",
           "bar_keys": [
               "income:revenue"
           ]
           "set_editable": [
               "income:revenue",
               "income:netIncome"
           ],
           "hidden_keys": [
               "income:costOfRevenue"
           ],
           "width": "full"  # Full width for the chart
       }
   })

Available Properties:
^^^^^^^^^^^^^^^^^^^^^

**title**:

-  **Description**: A string that sets the title of the chart. This
   title appears at the top of the chart and provides context for what
   is being displayed.
-  **Example**: ``"title": "Revenue and Net Income Over Time"``

**bar_keys**:

-  **Description**: Choose which series should be displayed as bars instead of lines.

   -  ``"*"``: All keys are displayed as bars.
   -  ``["key1", "key2", ... ]``: Specify which keys are displayed as bars.

**set_editable**:

-  **Description**: A list of data keys that can be made editable within
   the chart. This allows users to modify the values directly from the
   chart interface, making it interactive.
-  **Example**:
   ``"set_editable": ["income:revenue", "income:netIncome"]``

**hidden_keys**:

-  **Description**: A list of data keys that should be hidden from the
   chart. This is useful for excluding certain metrics that may clutter
   the visualization.
-  **Example**: ``"hidden_keys": ["income:costOfRevenue"]``

**width**:

-  **Description**: A string that defines the width of the chart.
   Possible values include:

   -  ``"full"``: The chart will take the full width of the container.
   -  ``"responsive"``: The chart will adjust its width based on the
      screen size.

-  **Example**: ``"width": "full"``

**include_ltm**:

-  **Description**: A boolean that determines whether to include the
   Last Twelve Months (LTM) data point in the chart. Setting this to
   ``True`` includes it, while ``False`` excludes it.
-  **Example**: ``"include_ltm": True``


--------------

``model.render_table()``
~~~~~~~~~~~~~~~~~~~~~~~~

The ``model.render_table()`` function is used to display data in a
structured tabular format, allowing for easy reading and comparison of
financial metrics. This function enables you to specify which data to
include in the table and customize its appearance.

Parameters of ``model.render_table()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The function accepts a dictionary containing the following keys:

-  **data**: A dictionary mapping data keys to their respective labels.
   This defines what metrics will be included in the table.
-  **start**: An integer representing the starting point for the table
   data (the number of periods back from the current period).
-  **end**: An integer or ``*`` (where ``*`` means all available
   periods) representing the ending point for the table data.
-  **properties**: A dictionary of settings that customize the table’s
   appearance and behavior.

Example of using ``model.render_table()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   model.render_table({
       "data": {
           "income:revenue": "Revenue",
           "income:netIncome": "Net Income",
           "income:eps": "Earnings Per Share"
       },
       "start": -5,  # Last 5 years
       "end": 0,     # Up to the current year
       "properties": {
           "title": "Financial Metrics Over Time",
           "number_format": "M",  # Display figures in millions
           "order": "descending",  # Show the most recent figures first
           "display_averages": True  # Include averages in the table
       }
   })

Properties of ``model.render_table()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**title**:

-  **Description**: A string that sets the title of the table. This
   title appears at the top of the table and provides context for what
   is being displayed.
-  **Example**: ``"title": "Financial Metrics Over Time"``

**number_format**:

-  **Description**: A string that specifies how the numbers should be
   formatted in the table. Common formats include:

   -  ``"M"``: Displays numbers in millions.
   -  ``"K"``: Displays numbers in thousands.
   -  ``"1"``: Displays numbers as is, without any formatting.

-  **Example**: ``"number_format": "M"``

**order**:

-  **Description**: A string that defines the order of the columns in
   the table. Possible values include:

   -  ``"ascending"``: Columns will be ordered from the earliest to the
      latest.
   -  ``"descending"``: Columns will be ordered from the latest to the
      earliest.

-  **Example**: ``"order": "descending"``

**display_averages**:

-  **Description**: A boolean that determines whether to include the
   average values for the displayed metrics in the table. When set to
   ``True``, the averages will be calculated and displayed as an
   additional row.
-  **Example**: ``"display_averages": True``

**width**:

-  **Description**: A string that defines the width of the table.
   Possible values include:

   -  ``"full"``: The table will take the full width of the container.
   -  ``"responsive"``: The table will adjust its width based on the
      screen size.

-  **Example**: ``"width": "full"``

--------------

``model.set_final_value()``
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sets the final calculated value for the model, often used to define the
output.

Set ``"units"`` to: - ``$`` for currency - ``%`` for percentages -
``None`` for standalone units

Example of using ``model.set_final_value()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

   model.set_final_value({
       "value": 100,  # Example stock value
       "units": "$"  # Currency
   })

--------------

``model.render_description()``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``model.render_description()`` function is used to add a descriptive
text to the model, providing context or details about its purpose,
assumptions, calculations, or any other relevant information that
enhances understanding for users.

Parameters of ``model.render_description()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The function accepts a single parameter:

-  **description**: A string or raw string (using ``r"""..."""`` syntax)
   that contains the descriptive text. This text can include markdown
   formatting for better presentation.

Example of using ``model.render_description()``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Here’s a basic example of how to use ``model.render_description()``:

.. code:: python

   model.render_description(r"""
   ## Revenue Projection Model

   This model calculates projected revenues based on historical trends and growth rates.
   """)

Markdown Formatting
^^^^^^^^^^^^^^^^^^^

You can use markdown syntax within the description to enhance its
readability and presentation. Here are some common formatting options:

-  **Headings**: Use ``#`` for headings. For example,
   ``## This is a Heading`` creates a second-level heading.

-  **Bold Text**: Use double asterisks ``**`` or double underscores
   ``__`` for bold text. For example, ``**bold text**`` will render as
   **bold text**.

-  **Italic Text**: Use single asterisks ``*`` or single underscores
   ``_`` for italic text. For example, ``*italic text*`` will render as
   *italic text*.

-  **Lists**: Use ``-`` or ``*`` for bullet points, and numbers for
   ordered lists. For example:

   ::

      - First item
      - Second item

-  **Links**: Create hyperlinks using the format ``[text](URL)``. For
   example, ``[Learn more](https://example.com)``.

