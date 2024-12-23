## Template for Python packaging and CI/CD

There are many tutorials online for `Python` packaging and dev-ops, but most of them only cover part of the picture. Discussion of the pros and cons of [different packaging tools](https://alpopkes.com/posts/python/packaging_tools/?utm_source=perplexity) and tech stack for python devops is not the concernt of this post, I only use this blog to remind myself one minimal functional template for my Python projects. 

[my example repo](https://github.com/mzgsxs/ci-cd-test)
---

### Components
Tech stacks:
1. [pdm](https://pdm-project.org/en/latest/)
2. keyring
3. [ruff](https://docs.astral.sh/ruff/) 
4. [pytest](https://docs.pytest.org/en/stable/index.html)
5. [github-actions](https://docs.github.com/en/actions)

#### Packaging and dependency and enviroment management (pdm)
Minimal structure required for packaging is as follows:
```
ROOT/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── PACKAGE_NAME/
│       ├── __init__.py
│       └── EXAMPLE.py
└── tests/
    ├── __init__.py
    └── test_EXAMPLE.py
```
Install (and use) or remove a specific python version
```
pdm python install 3.9.8
pdm python list
pdm python remove 3.9.8
```

[`pyproject.toml`](https://peps.python.org/pep-0621/) specifies the metadata/info for the project.
Start from inside empty `ROOT` directory, use pdm to automate this:
```bash
pdm init
```
[Add or remove dependencies/packages](https://pdm-project.org/en/latest/usage/dependency/) such as numpy by
```bash
pdm add DEPENDENCY_PACKAGE
pdm add DEV_DEPENDENCY_PACKAGE --group dev
pdm remove DEPENDENCY_PACKAGE
pdm remove DEV_DEPENDENCY_PACKAGE --group dev
```
[uv](https://docs.astral.sh/uv/) can be used as the package manager backend, but setup is [required](https://pdm-project.org/latest/usage/uv/). `pyproject.toml` will be automatically updated. To list packages install in `.venv` ( if not using [PEP 582](https://pdm-project.org/en/latest/usage/pep582/) )
```bash
pdm list
```
For version control, gitignore has also been generated automatically. 


#### Security
If there is any authetication/sensitive infomation required for this package and repo is opensourced, we need a way to manage those keys. 
If the code need to be deployed in multiple machines/servers, I will probably need to use AWS Secrets Manager. 



#### Lint & Formating
Ruff (Rust based, fast) can fix:
<ol>
  <li> import errors and sorting </li>
  <li> deprecated Syntax </li>
  <li> doc-strings formating </li>
  <li> Enforcing PEP8 Style </li>
  <li> ... </li>
</ol>

```
pdm add ruff --group dev
```
Manually add the following to `pyproject.toml`
```
[tool.ruff]
line-length = 88
exclude = ["build/", "docs/"]

[tool.ruff.lint]
extend-select = ["E501", "E", "F", "UP", "B", "SIM", "I"]
```
<ol>
  <li>`E`: Pycodestyle errors (e.g., whitespace issues, PEP8 violations).
  <li>`F`: Pyflakes errors (e.g., undefined variables, unused imports).
  <li>`UP`: Rules for upgrading Python syntax (e.g., replacing deprecated syntax).
  <li>`B`: flake8-bugbear rules (e.g., detecting likely bugs or design issues).
  <li>`SIM`: flake8-simplify rules (e.g., suggesting simpler constructs for clarity).
  <li>`I`: Import-related rules (e.g., ensuring imports are sorted).
  <li>`E501` is a Pycodestyle rule that enforces a maximum line length limit.
</ol>

And
```
pdm run ruff check --fix 
```
And
```
pdm run ruff format
```

#### Testing
Test 
```
pdm add pytest --group dev
```
And
```
pdm run pytest
```


#### Publishing
To publish to test pypi server
```bash
pdm build
pdm publish --no-build --repository testpypi --password PYPI_TOKEN
```
It's a twine wrapper and wheel file is really just a ZIP file


#### Documentation
Sphinx will be used, for it's cross-references feature & expandability
```
pdm add sphinx sphinx-autobuild --group dev
```
Init docs
```
pdm run sphinx-quickstart docs
```
Add this to `docs/conf.py` 
```
extensions = [
    'sphinx.ext.autodoc',        # Automatically document code from docstrings
    'sphinx.ext.napoleon',       # Support for Google-style and NumPy-style docstrings
    'sphinx.ext.viewcode',       # Add links to source code in documentation
]
```
Example docstring:
```python
"""
Bla Bla Bla
"""

def fn(a: int, b: int) -> int:
    """
    Bla bla

    Args:
        a (int): dika.
        b (int): laka.

    Returns:
        int: wowowo.
    """
    return a + b

```

Specify code to doc by 
```
pdm run sphinx-apidoc -o docs/source src/YOUR_PKG
```
Or add the following to `docs/conf.py` 
```
# Path to your Python code relative to the `docs/` directory
import os
import sys
sys.path.insert(0, os.path.abspath('../src/YOUR_PKG'))
```

After each code modification, run the following to update the documentation
```
cd src
pdm run make html
cd ..
```
Or to automatically rebuild and serve your docs localy while developing/editing
```
pdm run sphinx-autobuild docs docs/_build/html
```






### Notes