# fix-protobuf-imports

This script will fix relative imports (from and to nested sub-directories) within compiled `*pb2.py` and `*pb2.pyi` Protobuf files/modules generated from `protoc --python_out --mypy_out`:

```bash
fix-protobuf-imports /path/to/python_out/dir
```

## When do I need to fix my imports?

E.g. you might have the following file/module structure:

- `./`
  - `a_pb2.py`
  - `b_pb2.py`
  - `./sub/`
    - `c_pb2.py`
    - `./nested/`
      - `d_pb2.py`
      - `__init__.py`
    - `__init__.py`
  - `__init__.py`

Now assume, `c.proto` is importing `a.proto`, `b.proto` and `d.proto`.

`protoc` will generate the following import statements for `c_pb2.py`:

```python
# c_pb2.py

from google.protobuf import descriptor as _descriptor

import a_pb2 as a__pb2
import b_pb2 as b__pb2

from sub.nested import d_pb2 as sub_dot_nested__d__pb2

# ...
```

Using these modules will not work under Python 3, as the imports are not relative. As it can get quite cumbersome to fix these issues, this script will convert the imports automatically:

```bash
fix-protobuf-imports /path/to/python_out/dir
```

This will result in the following working imports:

```python
# c_pb2.py

from google.protobuf import descriptor as _descriptor

from .. import a_pb2 as a__pb2
from .. import b_pb2 as b__pb2

from ..sub.nested import d_pb2 as sub_dot_nested__d__pb2

# ...
```

## Development
This project uses the Python project managment tool `poetry`.

1. Install Poetry
  ```sh
    curl -sSL https://install.python-poetry.org | python3 - --preview
  ```
2. Install dependencies
  ```sh
    poetry install
  ```
3. Test script
  ```sh
    poetry run fix-protobuf-imports --help
  ```
