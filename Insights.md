## Objective
* Analyze the cause of road accidents in Million Plus Cities in India.
* Identify patterns in causes and outcomes and visualize the distribution of accidents based on different categories.

## library_Used
These are the python libraries that is used to Data Visualisation and Preparartion.
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

## Notebook_Setup
* Since dataset is large and wide, we need to inspect the entire DataFrame, therefore to display all columns and rows without truncation, we make these setups:
```python
pd.set_option("display.max_columns", None)
pd.set_option("display.max_rows", None)
```
* Also, to ignore all the warnings, we setup:
```python
import warnings
warnings.filterwarnings("ignore")
```