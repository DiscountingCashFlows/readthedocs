The ``model`` Object
====================

Render and manage model outputs.

``model.render_results()``
--------------------------

Display calculated values clearly.

.. code-block:: python

   model.render_results([
       [data.get("income:revenue"), "Revenue", "$"],
       [data.get("ratio:dividendYield"), "Dividend Yield", "%"]
   ])

``model.render_chart()``
------------------------

Visualize data trends:

.. code-block:: python

   model.render_chart({
       "data": {"income:revenue": "Revenue"},
       "start": -5,
       "properties": {
           "title": "Revenue Growth",
           "number_format": "M"
       }
   })

``model.render_table()``
------------------------

Structured tabular display:

.. code-block:: python

   model.render_table({
       "data": {"income:revenue": "Revenue"},
       "start": -5,
       "end": 0,
       "properties": {"title": "Historical Revenue"}
   })

Other Methods
-------------

- ``model.set_final_value()``
- ``model.render_description()``
