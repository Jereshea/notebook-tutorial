# Notebook Tutorial: test notebooks for the Notebook Tester

Jupyter notebooks with **known, deliberate faults**, for checking the Notebook Tester agents end to end. Each notebook's first cell describes its fault and the expected result.

## Expected results

| Notebook path (enter in the dashboard) | Fault | Test | After **Correct the notebook** |
|---|---|---|---|
| `notebooks/pass/basic_statistics.ipynb` | none | 🟢 passes | not needed |
| `notebooks/pass/installs_own_package.ipynb` | none; installs `tabulate` itself with `%pip` | 🟢 passes | not needed |
| `notebooks/fixable/typo_nameerror.ipynb` | variable typo `dta` → `NameError` | 🔴 fails | 🟡 typo fixed |
| `notebooks/fixable/missing_import.ipynb` | uses `math` without importing it → `NameError` | 🔴 fails | 🟡 `import math` added |
| `notebooks/fixable/missing_package.ipynb` | uses `pandas` but never installs it → `ModuleNotFoundError` | 🔴 fails | 🟡 `%pip install pandas` cell added |
| `notebooks/fixable/wrong_file_path.ipynb` | opens `sales.csv` from the wrong folder → `FileNotFoundError` | 🔴 fails | 🟡 path fixed to `../../data/sales.csv` |
| `notebooks/fixable/removed_pandas_api.ipynb` | `DataFrame.append` (removed in pandas 2) → `AttributeError` | 🔴 fails | 🟡 replaced with `pd.concat` |
| `notebooks/fixable/type_error.ipynb` | `'text' + int` → `TypeError` | 🔴 fails | 🟡 number converted to text |
| `notebooks/fixable/wrong_dict_key.ipynb` | key `'revenu'` instead of `'revenue'` → `KeyError` | 🔴 fails | 🟡 key fixed |
| `notebooks/fixable/two_faults.ipynb` | `pandas` not installed, **and** column typo `'unit'` | 🔴 fails | 🟡 both fixed (needs two attempts) |
| `notebooks/not_fixable/needs_running_service.ipynb` | needs a server on `localhost:8765` that isn't running | 🔴 fails | 🔴 "couldn't correct": start the service |
| `notebooks/not_fixable/needs_api_key.ipynb` | needs the `WEATHER_API_KEY` environment variable | 🔴 fails | 🔴 "couldn't correct": credentials must be provided |

🟢 passed as-is · 🟡 passed after self-correction · 🔴 failed / could not be corrected

The **not_fixable** notebooks check that the agent refuses to fake a fix (mocking the service, inventing a key) and reports what is missing instead.

## How to use

1. Push this repository to GitHub.
2. In the Notebook Tester dashboard, enter:
   - **GitHub repository URL:** the URL of this repo
   - **Branch:** `main`
   - **Notebook path:** any path from the table above
3. Click **Clone & Check**, then **Test Notebook**, then **Correct the notebook & re-test** if the test fails. Or turn on **Run all steps automatically** and do all of it with one click.
4. Compare the colour and the agent's explanation with the table.

To test the pull request flow, open a PR that changes one notebook and enter the PR number (for example `#1`) in the **Branch** field.

## Layout

```
data/sales.csv               shared sample data (used by the pandas and file-path notebooks)
notebooks/pass/              notebooks that should run as-is
notebooks/fixable/           notebooks with faults the agent should be able to correct
notebooks/not_fixable/       notebooks that need something outside the notebook
```

Every notebook was checked with the tester's runner (`run_notebook.py`, fresh venv): the two `pass` notebooks exit 0, and every other notebook fails with exactly the fault listed above.
