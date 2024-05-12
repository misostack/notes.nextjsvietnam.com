# Python Cheatsheet

## Setup python

- Create venv
- Install dependency packages

**Create venv**

```sh
pip install virtualenv
python -m venv venv
source venv/Scripts/activate
```

**Install dependencies**

```sh
pip install -r requirements.txt
```

**Package Manager**

- [setup poetry](https://www.jetbrains.com/help/pycharm/poetry.html#poetry-env)
- [poetry](https://python-poetry.org/)

```sh
# add new package
poetry add package-name
# build and publish your package
poetry build
poetry publish
```
