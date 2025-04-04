# Model Editor Documentation

Our [editor](https://discountingcashflows-beta.com/valuation/) runs on **Python 3.10** and uses a custom financial modeling framework documented here.

This framework helps you analyze the true value of a stock by projecting future cash flows and calculating key financial metrics. You can work with historical data, set your own assumptions, and visualize results using intuitive charts and tables—all designed to make financial analysis easier and more insightful.

**Note:** If you come across any bugs or inconsistencies, feel free to reach out to us via our [Help Page](https://discountingcashflows.com/help/)

## Good to know

The model processes only annualized and LTM (Last Twelve Months) data, for now.
This means that requesting the EPS for last year as in `data.get("income:eps:-1")` will return the **annual**, and not the quarterly, EPS.


## The Reported Data Map
When using the function calls with reported data, it's essential to keep in mind the structure of the reported data map. Each parent key maps to child keys, allowing you to access specific financial metrics. 

For example:

- To access "EPS" (Earnings Per Share) from the reported data, you would refer to it as `income:eps`. 

Here's the reported data map:
```json
{
    "balance": [
        "cashAndCashEquivalents",
        "shortTermInvestments",
        "cashAndShortTermInvestments",
        "netReceivables",
        "inventory",
        "otherCurrentAssets",
        "totalCurrentAssets",
        "propertyPlantEquipmentNet",
        "goodwill",
        "intangibleAssets",
        "goodwillAndIntangibleAssets",
        "longTermInvestments",
        "taxAssets",
        "otherNonCurrentAssets",
        "totalNonCurrentAssets",
        "otherAssets",
        "totalAssets",
        "accountPayables",
        "shortTermDebt",
        "taxPayables",
        "deferredRevenue",
        "otherCurrentLiabilities",
        "totalCurrentLiabilities",
        "longTermDebt",
        "deferredRevenueNonCurrent",
        "deferredTaxLiabilitiesNonCurrent",
        "otherNonCurrentLiabilities",
        "totalNonCurrentLiabilities",
        "otherLiabilities",
        "capitalLeaseObligations",
        "totalLiabilities",
        "preferredStock",
        "commonStock",
        "retainedEarnings",
        "accumulatedOtherComprehensiveIncomeLoss",
        "othertotalStockholdersEquity",
        "totalStockholdersEquity",
        "totalEquity",
        "totalLiabilitiesAndStockholdersEquity",
        "minorityInterest",
        "totalLiabilitiesAndTotalEquity",
        "totalInvestments",
        "totalDebt",
        "netDebt",
        "calculatedOtherCurrentAssets",
        "calculatedOtherNonCurrentAssets",
        "calculatedOtherCurrentLiabilities",
        "calculatedOtherNonCurrentLiabilities"
    ],
    "dividend": [
        "adjDividend"
    ],
    "economic": [
        "cpi"
    ],
    "flow": [
        "netIncome",
        "depreciationAndAmortization",
        "deferredIncomeTax",
        "stockBasedCompensation",
        "changeInWorkingCapital",
        "accountsReceivables",
        "inventory",
        "accountsPayables",
        "otherWorkingCapital",
        "otherNonCashItems",
        "netCashProvidedByOperatingActivities",
        "investmentsInPropertyPlantAndEquipment",
        "acquisitionsNet",
        "purchasesOfInvestments",
        "salesMaturitiesOfInvestments",
        "otherInvestingActivites",
        "netCashUsedForInvestingActivites",
        "debtRepayment",
        "commonStockIssued",
        "commonStockRepurchased",
        "dividendsPaid",
        "otherFinancingActivites",
        "netCashUsedProvidedByFinancingActivities",
        "effectOfForexChangesOnCash",
        "netChangeInCash",
        "cashAtEndOfPeriod",
        "cashAtBeginningOfPeriod",
        "operatingCashFlow",
        "capitalExpenditure",
        "freeCashFlow",
        "calculatedOtherWorkingCapital"
    ],
    "income": [
        "revenue",
        "costOfRevenue",
        "grossProfit",
        "grossProfitRatio",
        "researchAndDevelopmentExpenses",
        "generalAndAdministrativeExpenses",
        "sellingAndMarketingExpenses",
        "sellingGeneralAndAdministrativeExpenses",
        "otherExpenses",
        "operatingExpenses",
        "costAndExpenses",
        "interestIncome",
        "interestExpense",
        "depreciationAndAmortization",
        "ebitda",
        "ebitdaratio",
        "operatingIncome",
        "operatingIncomeRatio",
        "totalOtherIncomeExpensesNet",
        "incomeBeforeTax",
        "incomeBeforeTaxRatio",
        "incomeTaxExpense",
        "netIncome",
        "netIncomeRatio",
        "eps",
        "epsdiluted",
        "weightedAverageShsOut",
        "weightedAverageShsOutDil",
        "calculatedOtherExpenses",
        "calculatedOperatingExpenses",
        "calculatedNetInterest",
        "calculatedOtherIncome",
        "calculatedIncomeNonControlling"
    ],
    "profile": [
        "price",
        "beta",
        "volAvg",
        "mktCap",
        "lastDiv",
        "changes",
        "rangeMin",
        "rangeMax"
    ],
    "quote": [
        "close"
    ],
    "ratio": [
        "dividendYield",
        "payoutRatio",
        "currentRatio",
        "quickRatio",
        "cashRatio",
        "daysOfSalesOutstanding",
        "daysOfInventoryOutstanding",
        "operatingCycle",
        "daysOfPayablesOutstanding",
        "cashConversionCycle",
        "grossProfitMargin",
        "operatingProfitMargin",
        "pretaxProfitMargin",
        "netProfitMargin",
        "effectiveTaxRate",
        "returnOnAssets",
        "returnOnEquity",
        "returnOnCapitalEmployed",
        "netIncomePerEBT",
        "ebtPerEbit",
        "ebitPerRevenue",
        "debtRatio",
        "debtEquityRatio",
        "longTermDebtToCapitalization",
        "totalDebtToCapitalization",
        "interestCoverage",
        "cashFlowToDebtRatio",
        "companyEquityMultiplier",
        "receivablesTurnover",
        "payablesTurnover",
        "inventoryTurnover",
        "fixedAssetTurnover",
        "assetTurnover",
        "operatingCashFlowPerShare",
        "freeCashFlowPerShare",
        "cashPerShare",
        "operatingCashFlowSalesRatio",
        "freeCashFlowOperatingCashFlowRatio",
        "shortTermCoverageRatios",
        "capitalExpenditureCoverageRatio",
        "dividendPaidAndCapexCoverageRatio",
        "priceToBookRatio",
        "priceEarningsRatio",
        "priceToFreeCashFlowsRatio",
        "priceToOperatingCashFlowsRatio",
        "priceEarningsToGrowthRatio",
        "priceSalesRatio",
        "enterpriseValueMultiple",
        "freeCashFlowMargin",
        "returnOnInvestedCapital",
        "cashConversionRatio",
        "freeCashFlowToEarnings",
        "pricePerShare",
        "bookValuePerShare",
        "revenuePerShare",
        "earningsPerShare",
        "dividendPerShare",
        "ebitPerShare",
        "evPerShare",
        "dividendPayoutRatio"
    ],
    "risk": [
        "defaultSpread",
        "totalEquityRiskPremium",
        "countryRiskPremium",
        "corporateTaxRate",
        "sovereignCDSSpread"
    ],
    "treasury": [
        "month1",
        "month2",
        "month3",
        "month6",
        "year1",
        "year2",
        "year3",
        "year5",
        "year7",
        "year10",
        "year20",
        "year30"
    ]
}
```

## The `data` object

Data is split between **reported data** and **user generated data**, both are stored in the `data` object.

 - Reported data is the data retrieved from our third party providers such as `financialmodelingprep.com`.
 - User data is data generated by using the `compute` or the `set` functions.

### `data.compute()`

Calculates values based on specified formulas and stores them in the `data` object. 
The formulas can reference other keys, either for reported data or user data, and can include mathematical operations and specialized functions. Let's take an example.

#### Example:
```python
# Computing Benjamin Graham's number
data.compute({  
    "#bookValue": "balance:totalStockholdersEquity / income:weightedAverageShsOut",
    "#intermediaryVariable": f"#bookValue * income:eps * {assumptions.get('graham_multiplier')}",
    "#grahamNumber": "function:sqrt:#intermediaryVariable",
    "%revenueGrowthRate": "function:growth:income:revenue",
})
```

### Breaking down the example:

Formulas are evaluated **from top to bottom** and starting from the earliest year available all the way until the end.
For the sake of this example, let's say the earliest year available is 2000.

#### 1. **`#bookValue`**

The first evaluated key is `#bookValue`. 

The framework starts by fetching the total stockholders' equity `totalStockholdersEquity` from the balance sheet in year 2000 and the number of shares outstanding `weightedAverageShsOut` from the income statement also in year 2000.

Calculates the `#bookValue` per share for year 2000 by dividing the total stockholders' equity by the weighted average shares outstanding.

#### 2. **`#intermediaryVariable`**

Since the value for `#bookValue` has just been calculated, the framework can now evaluate the second key `#intermediaryVariable`.

**Note:** Assumptions can be used within string formulas. One simple way is to use the following python string format:
```python
f"{assumptions.get('graham_multiplier')}"
```

#### 3. **`#grahamNumber`**

The third evaluated key is `#grahamNumber`, which uses the square root function `function:sqrt`. 

**Note:** The formula could also be written without a special function, using the power operator `**`. But it could result in complex numbers.
```python
"#grahamNumber": "#intermediaryVariable ** 0.5",
```

#### 4. **`%revenueGrowthRate`**

The fourth and last evaluated key is `%revenueGrowthRate`, which uses another function called `function:growth`

#### Key Types

Notice that there are multiple types of keys, this is to keep the model organized and the framework can format the values in millions or thousands depending on the key type.

 1. Keys that start with **"#"** are indicating that its values are either **standalone units** like ratios or "per share" items.
 2. Keys that start with **"%"** are indicating that its values are **percentages**.
 3. Keys that start with **anything else**, will be considered formattable to millions or thousands.

#### Examples:

 1. Price to Earnings ratio can be named something like **"#priceToEarnings"**
 2. The tax rate can be named **"%taxRate"**
 3. The key for forecasted revenue can be named just **"forecastedRevenue"**. The values will be then formatted to millions or thousands in the rendering table or chart, depending on your rendering preferences.


### Forecasting Values

Forecasting values allows you to project future financial metrics based on historical data and specified growth rates. This is particularly useful for estimating performance over a defined period.

#### Storing Projection Years as an Assumption

To make the model more interactive and allow control over the number of projected years, you can store the projection duration as an assumption. Use the `assumptions.init()` method to initialize the `"projection_years"` key before performing any forecasts:

```python
# Initialize assumptions for projection years
assumptions.init({
    "projection_years": 5  # Adjust this value to specify how many years to project
})
```

#### Using `data.compute` for Forecasting

You can now use the `data.compute()` function to calculate projected values. Below is an example of how to compute projected revenues based on a annual growth rate of 10%. 

```python
# Compute projected revenues using a growth rate
data.compute({
    "income:revenue": f"income:revenue:-1 * (1 + 0.1)",  # Projecting a 10% growth rate
}, forecast=assumptions.get("projection_years"))
```

**Note:** Feel free to make the revenue growth an assumption as well.

### Example of Forecasting

Here's a complete example that initializes assumptions, computes projected revenue, and displays the results in a table:

```python
# Initialize assumptions
assumptions.init({
    "projection_years": 5,  # Set the number of years to project
    "%revenue_growth_rate": "10%"
})

# Compute projected revenues at a 10% growth rate
data.compute({
    "income:revenue": f"income:revenue:-1 * (1 + {assumptions.get('%revenue_growth_rate')})",
}, forecast=assumptions.get("projection_years"))

# Render a table to display the projected revenues
model.render_table({
    "data": {
        "income:revenue": "Projected Revenue",
    },
    "start": 1,  # Start from the next year
    "end": assumptions.get("projection_years"),  # End at the projected years
    "properties": {
        "title": "Projected Revenues",
        "number_format": "M",  # Display figures in millions
        "column_order": "ascending",  # Show projected years in order
    },
})
```

## Available Functions

### `function:growth`

Calculates the year-over-year growth rate of the specified data key.  
Returns `(current - previous) / previous`.  

**Example:** `"function:growth:income:netIncome"`

**Note:** The `growth` function only accepts keys, not values.

### `function:discount`

Discounts a key or value using compound interest to adjust a future value to its present value.

**Example #1:** Discounting forecasted `flow:freeCashFlow` at 10%:

- `"function:discount:flow:freeCashFlow rate:0.1"`

**Example #2:** Discounting forecasted `flow:freeCashFlow` at 10% continuously:

- `"function:discount:flow:freeCashFlow rate:0.1 continuous:true"`

**Required parameters**

 - `rate:`
	 - The annual discount rate used to discount future cash flow or other figures to present value.

**Optional parameters**

- `offset:[..., -2, -1, ...]`
	- Shifts the time period used in discounting by a set number of years. The default value is `offset:0`
- `continuous:[true, false]`
	-  Can be configured for continuous time by setting `continuous:true`

**Note:** Setting `continuous:true` will discount the next year's `flow:freeCashFlow` to present value accounting for the days left until the fiscal year ends. 

`discount rate = ((1 + rate) ** days difference / 365)`

### `function:compound`

Compounds a key or value using compound interest. 

**Example:** `"function:compound:1 rate:0.1 offset:-1"`

**Required parameters**

 - `rate`
	 - The annual rate used to compound the given value.

**Optional parameters**

- `offset:[..., -2, -1, ...]`
	- Shifts the time period used in compounding by a set number of years. The default value is `offset:0`
- `continuous:[true, false]`
	-  Can be configured for continuous time by setting `continuous:true`


### `function:linear_regression`

Performs linear regression over historical values of the specified data key.  
Stores the predicted values across historical and forecast dates.  

**Example:** `"function:linear_regression:income:revenue start:-5 end:0"`

**Optional parameters**

- `start:[..., -2, -1, ...]`
	- Sets the regression start relative to LTM. The default starting period is the first available historical period.
- `end:[..., 0, 1, ...]`
	- Sets the regression end relative to LTM. The default ending period is the last available period.
- `except_ltm:[true, false]`
	-  To account for the Last Twelve Months (LTM) period in the linear regression, set `except_ltm:false`, by default it is `true`.

### Range Functions

The following functions support range selection and share the same optional parameters:

 - `function:average`
	 - Calculates the average of values over a specified range of periods.  
 - `function:sum` or `function:add`
	 - Returns the total sum of values over a specified period. Synonymous aliases: `sum`, `add`.
 - `function:max` or `function:maximum`
	 - Returns the maximum value in the specified range.  Synonymous aliases: `max`, `maximum`.  
 - `function:min` or `function:minimum`
	 - Returns the minimum value in the specified range.  Synonymous aliases: `min`, `minimum`.  
 - `function:multiply`
	 - Returns the product of values over the specified range.  Useful for chaining multipliers over time.  

**Example #1:** Averaging the last 3 years.
- `"function:average:exampleKey period:3"`

**Example #2:** Using range selection to select the last 3 years.
- `"function:average:exampleKey:-2->0"`

**Optional parameters - alternatives to range selection**

- `period:[1, 2, ...]`
	-  Selects the specified number of periods. This is just an alternative to `function:average:x->0`, where `x = (-1)*(periods - 1)`.
- `start:[..., -2, -1, ...]`
	- Sets the start relative to LTM. The default starting period is the first available historical period.
- `end:[..., 0, 1, ...]`
	- Sets the end relative to LTM. The default ending period is the last available period.

### `function:sqrt`

Returns the square root of the specified value.  
Only defined for non-negative values.  

**Example:** `"function:sqrt:16"` returns `4.0`

### `function:pow`

Raises the value to the power specified in `raised_to` parameter.  

**Example:** `"function:pow:2 raised_to:3"` returns `8.0`

**Required parameters**

 - `rate`
	 - The annual discount rate used to discount future cash flow to present value.

### `function:log`

Returns the logarithm of a number using a given base (default is natural log, base *e*).  
Base must be positive and not equal to 1.  

**Example:** `"function:log:10 base:10"` returns `1.0`

**Required parameters**

 - `rate`
	 - The annual discount rate used to discount future cash flow to present value.

### `function:exp`

Returns *e* raised to the power of the given value.  
Useful for reversing logarithmic values.  

**Example:** `"function:exp:1"` returns approximately `2.718`


### Available operations

Here are all the available operations allowed within `data.compute()`

#### Arithmetic Operations

- **Addition**: `+`  
  Adds two numbers.  
  Example: `3 + 2` results in `5`

- **Subtraction**: `-`  
  Subtracts the right number from the left.  
  Example: `5 - 2` results in `3`

- **Multiplication**: `*`  
  Multiplies two numbers.  
  Example: `4 * 3` results in `12`

- **Division**: `/`  
  Divides the left number by the right. Returns a float.  
  Example: `10 / 4` results in `2.5`

- **Floor Division**: `//`  
  Divides and rounds down to the nearest integer.  
  Example: `10 // 4` results in `2`

- **Exponentiation**: `**`  
  Raises the left number to the power of the right.  
  Example: `2 ** 3` results in `8`

- **Modulus**: `%`  
  Returns the remainder of the division.  
  Example: `10 % 3` results in `1`

#### Boolean Operations

Boolean operations evaluate to `1` if the condition is `True` and `0` if the condition is `False`.  
These results can be used in **Arithmetic Operations** just like numbers.

- **Equal to**: `==`  
  Checks if two values are equal.  
  Example #1: `5 == 5` results in `1`
  Example #2: `5 == 6` results in `0`

- **Not equal to**: `!=`  
  Checks if two values are not equal.  
  Example: `5 != 3` results in `1`

- **Less than**: `<`  
  Example: `3 < 5` results in `1`

- **Greater than**: `>`  
  Example: `7 > 4` results in `1`

- **Less than or equal to**: `<=`  
  Example: `4 <= 4` results in `1`

- **Greater than or equal to**: `>=`  
  Example: `6 >= 3` results in `1`

#### Grouping
- **Parentheses**: `(` `)`  
  Used to control the order of operations.  
  Example: `2 * (3 + 4)` results in `14`

---

### `data.set()`

The `data.set()` function allows you to set values in the stored data. You can set a single key-value pair or multiple pairs at once.

### Example:
```python
data.set("income:netIncome:1", 1000000)  # Set future net income, not overwriting
data.set({
    "income:revenue:1": 5000000,
    "income:costOfRevenue:1": 3000000
}, overwrite=True)  # Set multiple values overwriting any existing values
```

---

### `data.get()`

Retrieves a value from the stored data. You can specify a key and optionally define a default value if the key is not found.

### Example:
```python
ltm_eps = data.get("income:eps")  # Retrieves the last twelve months EPS from the income statement
previous_year_eps = data.get("income:eps:-1")  # Retrieves the previous year's EPS
```

#### Range Selection:

You can also select a range of values. For instance, to get the EPS values over the last 5 years plus LTM, you would use:

```python
historical_eps = data.get("income:eps:-5->0")
```

---

### `data.min()`

Calculates the minimum value for a given key, ignoring None values.

#### Example:
```python
min_eps = data.min("income:eps:-10->0")  # Minimum EPS over the last 10 years including LTM
```

---

### `data.max()`

Calculates the maximum value for a given key, similar to the `min()` function.

#### Example:
```python
max_revenue = data.max("income:revenue:-5->-1")  # Maximum revenue over the last 5 years, excluding LTM
```

---

### `data.average()`

Calculates the average of values for a given key, ignoring None.

#### Example:
```python
average_eps = data.average("income:eps:-10->0")  # Average EPS over the last 10 years, including LTM
```

---

### `data.sum()`

Calculates the sum of values for a specified key.

#### Example:
```python
total_revenue = data.sum("income:revenue:-5->-1")  # Total revenue over the last 5 years, excluding LTM
```

---

### `data.count()`

This function counts the number of entries for the specified key, excluding specified values if needed.

#### Example:
```python
count_dividends = data.count("dividend:adjDividend:*", properties={"except_values": [None, 0]})  # Count non-zero dividends
```

## The `assumptions` object

### `assumptions.init()`

Initializes assumptions for your model. You can set a hierarchy of assumptions for structured relationships.

#### Example (Without Hierarchies):
```python
assumptions.init({
	"%growth_rate": "5%",  # Strings that denote percentages are allowed
	"historical_years": 10
})
```

#### Example (With Hierarchies):
```python
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
```
In this example, `%discount_rate` is the parent assumption, while `beta`, `%risk_free_rate` and `%market_premium` are its children.

**Note:** Percentage assumptions, that start with `%`, can be specified either through a string like `"5%"` or the value directly `0.05`

---

### `assumptions.get()`

Fetches the value of a specified assumption. Raises an error if None.

#### Example:
```python
growth_rate = assumptions.get("%growth_rate")  # Get the growth rate
```

---

### `assumptions.set()`

This function sets the value of a specific assumption.

#### Example:
```python
assumptions.set("%growth_rate", 0.07)  # Set growth rate to 7%
```

---

### `assumptions.set_description()`

Sets a description for assumptions, providing context or explanations.

#### Example:
```python
assumptions.set_description({
    "%growth_rate": "The expected annual growth rate of revenues."
})
```


## The `model` object


### `model.render_results()`

The `model.render_results()` function is used to display results in a structured format. It shows the calculated values along with their corresponding labels, making it easy for users to interpret the outcomes of your financial model.

#### Parameters

The function takes a list of results, where each result is structured as follows:

- **Value**: The value to be displayed (e.g., a calculated metric).
- **Label**: A string that describes the value (e.g., "Net Income").
- **Data Type**: A string indicating the type of data, which affects how the value is formatted for display. Common types include:
  - **"$"**: Indicates that the value is a currency (e.g., dollars). This will format the number appropriately, often with commas and two decimal places.
  - **"%"**: Indicates that the value is a percentage. This will display the number as a percentage, typically multiplying the underlying value by 100 and appending a "%" sign.

##### Example 1: Currency Formatting

```python
model.render_results([
    [data.get("income:revenue"), "Total Revenue", "$"],
    [data.get("income:netIncome"), "Net Income", "$"],
    [data.get("income:eps"), "Earnings Per Share", "$"]
])
```
- **Total Revenue** and **Net Income** are formatted as currency, meaning they will display as, for example, **2.5 Bill. USD**, **1 Mil. CAD** and **250 Thou. EUR** and so on. 

##### Example 2: Percentage Formatting

```python
model.render_results([
    [data.get("ratio:dividendYield"), "Dividend Yield", "%"],
    [data.get("ratio:netProfitMargin"), "Profit Margin", "%"]
])
```
- In this case, **Dividend Yield** and **Profit Margin** are displayed as percentages. For instance, if **Dividend Yield** is 0.05, it will be shown as **5%**. Similarly, if the **Profit Margin** is 0.15, it will be displayed as **15%**.

#### Summary

The third parameter in `model.render_results()` allows you to control the formatting of the displayed values. By using **"$"** for currency and **"%"** for percentages, you ensure that the results are presented in a clear and understandable manner, appropriate for financial analysis. 

---

### `model.render_chart()`

The `model.render_chart()` function is used to create visual representations of financial metrics, helping to illustrate trends and comparisons over time. This function allows you to specify which data to visualize and configure various properties of the chart.

#### Parameters

The function accepts a dictionary containing the following keys:

- **data**: A dictionary mapping data keys to their respective labels. This defines what metrics will be included in the chart.
- **start**: An integer representing the starting point for the x-axis (the number of years from the current year). 
- **end**: An integer or * (* means all available years) representing the ending point for the x-axis (the number of years from the current year).
- **properties**: A dictionary of settings that customize the chart's appearance and behavior.

#### Example:

```python
model.render_chart({
    "data": {
        "income:revenue": "Revenue",
        "income:netIncome": "Net Income"
    },
    "start": -5,  # Last 5 years
    "properties": {
        "title": "Revenue and Net Income Over Time",
        "number_format": "M",  # Display figures in millions
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
```

#### Available Properties:

**title**: 

   - **Description**: A string that sets the title of the chart. This title appears at the top of the chart and provides context for what is being displayed.
   - **Example**: `"title": "Revenue and Net Income Over Time"`

**number_format**: 

   - **Description**: A string that specifies how the numbers should be formatted in the chart. Common formats include:
     - `"M"`: Displays numbers in millions.
     - `"K"`: Displays numbers in thousands.
     - `"1"`: Displays numbers as is, without any formatting.
   - **Example**: `"number_format": "M"`

**set_editable**: 

   - **Description**: A list of data keys that can be made editable within the chart. This allows users to modify the values directly from the chart interface, making it interactive.
   - **Example**: `"set_editable": ["income:revenue", "income:netIncome"]`

**hidden_keys**: 

   - **Description**: A list of data keys that should be hidden from the chart. This is useful for excluding certain metrics that may clutter the visualization.
   - **Example**: `"hidden_keys": ["income:costOfRevenue"]`

**width**: 

   - **Description**: A string that defines the width of the chart. Possible values include:
     - `"full"`: The chart will take the full width of the container.
     - `"responsive"`: The chart will adjust its width based on the screen size.
   - **Example**: `"width": "full"`


**include_ltm**: 

   - **Description**: A boolean that determines whether to include the Last Twelve Months (LTM) data point in the chart. Setting this to `True` includes it, while `False` excludes it.
   - **Example**: `"include_ltm": True`

**Coming Soon - chart_type**: 

**Description**: A string that defines the type of chart to render. 

- `"line"`: A line chart.
- `"bar"`: A bar chart.
- `"pie"`: A pie chart.

**Example**: `"chart_type": "line"`

---

### `model.render_table()`

The `model.render_table()` function is used to display data in a structured tabular format, allowing for easy reading and comparison of financial metrics. This function enables you to specify which data to include in the table and customize its appearance.

#### Parameters

The function accepts a dictionary containing the following keys:

- **data**: A dictionary mapping data keys to their respective labels. This defines what metrics will be included in the table.
- **start**: An integer representing the starting point for the table data (the number of periods back from the current period).
- **end**: An integer or `*` (where `*` means all available periods) representing the ending point for the table data.
- **properties**: A dictionary of settings that customize the table's appearance and behavior.

#### Example:

```python
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
        "column_order": "descending",  # Show the most recent figures first
        "display_averages": True  # Include averages in the table
    }
})
```

#### Available Properties:

**title**: 

   - **Description**: A string that sets the title of the table. This title appears at the top of the table and provides context for what is being displayed.
   - **Example**: `"title": "Financial Metrics Over Time"`

**number_format**: 

   - **Description**: A string that specifies how the numbers should be formatted in the table. Common formats include:
     - `"M"`: Displays numbers in millions.
     - `"K"`: Displays numbers in thousands.
     - `"1"`: Displays numbers as is, without any formatting.
   - **Example**: `"number_format": "M"`

**column_order**: 

   - **Description**: A string that defines the order of the columns in the table. Possible values include:
     - `"ascending"`: Columns will be ordered from the earliest to the latest.
     - `"descending"`: Columns will be ordered from the latest to the earliest.
   - **Example**: `"column_order": "descending"`

**display_averages**: 

   - **Description**: A boolean that determines whether to include the average values for the displayed metrics in the table. When set to `True`, the averages will be calculated and displayed as an additional row.
   - **Example**: `"display_averages": True`

**width**: 

   - **Description**: A string that defines the width of the table. Possible values include:
     - `"full"`: The table will take the full width of the container.
     - `"responsive"`: The table will adjust its width based on the screen size.
   - **Example**: `"width": "full"`

---

### `model.set_final_value()`

Sets the final calculated value for the model, often used to define the output.

Set `"units"` to:
 - `$`  for currency
 - `%` for percentages
 - `None` for standalone units

#### Example:
```python
model.set_final_value({
    "value": 100,  # Example stock value
    "units": "$"  # Currency
})
```

---

### `model.render_description()`

The `model.render_description()` function is used to add a descriptive text to the model, providing context or details about its purpose, assumptions, calculations, or any other relevant information that enhances understanding for users.

#### Parameters

The function accepts a single parameter:

- **description**: A string or raw string (using `r"""..."""` syntax) that contains the descriptive text. This text can include markdown formatting for better presentation.

#### Example:

Here’s a basic example of how to use `model.render_description()`:

```python
model.render_description(r"""
## Revenue Projection Model

This model calculates projected revenues based on historical trends and growth rates.
""")
```

#### Markdown Formatting

You can use markdown syntax within the description to enhance its readability and presentation. Here are some common formatting options:

- **Headings**: Use `#` for headings. For example, `## This is a Heading` creates a second-level heading.
- **Bold Text**: Use double asterisks `**` or double underscores `__` for bold text. For example, `**bold text**` will render as **bold text**.
- **Italic Text**: Use single asterisks `*` or single underscores `_` for italic text. For example, `*italic text*` will render as *italic text*.
- **Lists**: Use `-` or `*` for bullet points, and numbers for ordered lists. For example:
  ```
  - First item
  - Second item
  ```
- **Links**: Create hyperlinks using the format `[text](URL)`. For example, `[Learn more](https://example.com)`.

#### Additional Example with Formulas:

You can also include mathematical formulas in the description using LaTeX-style syntax. Here's how you might do that:

```python
model.render_description(r"""
## Discounted Cash Flow Model

This model calculates the present value of future cash flow using the Discounted Cash Flow (DCF) method.

The formula used for calculating the present value is:

$$
PV = \frac{CF}{(1 + r)^n}
$$

Where:
- \(PV\) = Present Value
- \(CF\) = Cash Flow
- \(r\) = Discount Rate
- \(n\) = Number of periods

This framework allows for robust financial projections.
""")
```
