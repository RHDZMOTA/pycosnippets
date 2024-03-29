# Python Code Snippets

`PyCo Snippets` is a python library that allows you to execute python snippets directly on a python runtime
and catch the output by leveraging a jupyter-notebook runner.

The main idea is simple: 
1. Get a python code snippet as a string.
2. Programmatically add the snippet to a jupyter-notebook template.
3. Programmatically execute the notebook and inspect the output cells.

Example:

```python
from pycosnippets.templates import Template

# Python code snippet
code_snippet = """
print("Hello, World")
"""

# Create a notebook from a template
template = Template()
notebook = template.add_snippet(code=code_snippet)

# Execute the notebook and get the output as json
notebook.execute()
print(notebook.get_last_outputs())
```

## Relevant info

Since we attempt to provide a controlled environment, this package is currently only
available for a specific python version and only a few libraries are supported.

For this reason, and to reduce compatibility issues, the Python version and default libraries are defined
according to the [Databricks LTS runtime](https://docs.databricks.com/release-notes/runtime/releases.html). We install
the runtime via [rhdzmota/databricks-runtime](https://github.com/RHDZMOTA/databricks-runtime) library.
* Current runtime: `databricks-runtime==7.3.dev4`

## Installation

Install with pip:

```commandline
$ pip install pycosnippets
```

Test by running:

```commandline
$ pycosnippets hello-world
```

Output:

```json
[
    {
        "data": {
            "text/plain": [
                "'Hello, Pythonista!'"
            ]
        },
        "execution_count": 2,
        "metadata": {},
        "output_type": "execute_result"
    }
]
```


## Usage

You can use `pycosnippets` as a python library or a commandline application.

### CLI Application

Once you install `pycosnippets`, you can call it via the commandline:
* By running `pycosnippets` if you install it globally.
* By running `python -m pycosnippets` otherwise.

```commandline
$ pycosnippets run-snippet --code "print('Hello')" 
```

Expected output:

```text
{
    "timestamp_start": 1633303932.235129,
    "timestamp_final": 1633303942.675125,
    "output": [
        {
            "name": "stdout",
            "output_type": "stream",
            "text": [
                "Hello\n"
            ]
        }
    ],
    "metadata": null
}
```

We are using a jupyter-notebook runner to execute the python code. The submitted code gets appended to a
juypter-notebook template (`src/pycosnippets/template/default.ipynb`) and placed in a temporal directory defined by the
`PYCO_NOTEBOOK_TEMPLATE_OUTPUT_PATH` environment variable. You can provide your own templates by changing the
`PYCO_NOTEBOOK_TEMPLATE_PATH` and `PYCO_NOTEBOOK_TEMPLATE_NAME` environment variables.

You can also run github gists by provinding the following info:
* `user`: Github user
* `file`: Filename to execute
* `gist_id`: Gist ID

```commandline
$ pycosnippets run-gist \
    --user rhdzmota \
    --file hello.py \
    --gist-id 9aa444dfb15b5d8a96d1204bf173309a 
```

You can also load gists programmatically in your scripts.
Consider the following `hello.py` script:

```python

pyco.load_gist(
    u="rhdzmota",
    g="9aa444dfb15b5d8a96d1204bf173309a",
    f="hello.py",
)

hello(world="world")

```

Running the following command should load the gist code and execute the function call:

```commandline
$ pycosnippets run-script --filepath hello.py
```

Expected output:

```text
{
    "timestamp_start": 1633305459.1752648,
    "timestamp_final": 1633305463.0998662,
    "output": [
        {
            "data": {
                "text/plain": [
                    "'Hello, world!'"
                ]
            },
            "execution_count": 2,
            "metadata": {},
            "output_type": "execute_result"
        }
    ],
    "metadata": null
}
```

### Python library

There are two relevant classes:
* `Template`: This class allows you to register code in a jupyter notebook template, execute it, and get the output.
  * Import: `from pycosnippets.templates import Template` 
* (recommended) `PyCo`: This is a high-level singleton that attempts to provide all the relevant functionality for executing python snippets. 

Template usage example:

```python
from pycosnippets.templates import Template

# Python code snippet
code_snippet = """
print("Hello, World")
"""

# Create a notebook from a template
template = Template()
notebook = template.add_snippet(code=code_snippet)

# Execute the notebook and get the output as json
notebook.execute()
print(notebook.get_last_outputs())
```

PyCo usage example:

```python
from pycosnippets.pyco import PyCo

# Python code snippet
code_snippet = """
print("Hello, World")
"""

# Get PyCo singleton
pyco = PyCo.instance()

# Execute the code
output = pyco.run_code(
  code_snippet=code_snippet
)

print(output)
```


## Development

Clone this repo and install locally with `pip`:

```commandline
$ pip install -e .
```

Test by running:

```commandline
$ pycosnippets hello-world
```

Output:

```json
[
    {
        "data": {
            "text/plain": [
                "'Hello, Pythonista!'"
            ]
        },
        "execution_count": 2,
        "metadata": {},
        "output_type": "execute_result"
    }
]
```
