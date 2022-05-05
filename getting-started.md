# Building Azure CI/CD Pipeline

## 1. CI: Setup Azure Cloud Shell

### 1.1 Create Cloud Based Development Environment

- Create GitHub Repo
Go to GitHub account and create a Repo. For this project, I have created `build-az-cicd`

- Login to cloud shell and create the SSH Keys

```shell
$ ssh-keygen -t rsa
```

- Copy SSH Key to add it to GitHub Account
```shell
$ cat ~/.ssh/id_rsa.pub
```

- In GitHub -> Go To Settings -> SSH & GPG Keys and then add the copied key

### 1.2 Create `Makefile` with shortcuts to build, test, and deploy a project

```
install:
    pip install --upgrade pip &&\
        pip install -r requirements.txt

test:
    python -m pytest -vv test_hello.py


lint:
    pylint --disable=R,C hello.py

all: install lint test
```

### 1.3 Create `requirements.txt`

add to it
```
pylint
pytest
```

### 1.4 Create the Python Virtual Environment

```
$ python3 -m venv ~/.myrepo
$ source ~/.myrepo/bin/activate
```

### 1.5 Create `hello.py`
```python
def toyou(x):
    return "hi %s" % x


def add(x):
    return x + 1


def subtract(x):
    return x - 1
```

### 1.6 Create `test_hello.py`
```python
from hello import toyou, add, subtract


def setup_function(function):
    print("Running Setup: %s" % function.__name__)
    function.x = 10


def teardown_function(function):
    print("Running Teardown: %s" % function.__name__)
    del function.x


### Run to see failed test
#def test_hello_add():
#    assert add(test_hello_add.x) == 12

def test_hello_subtract():
    assert subtract(test_hello_subtract.x) == 9
```

### 1.7 Test

```
$ make all
```

## 2.0 Configure GitHub Actions

### 2.1 Enable GitHub Actions

Go to Repo `build-az-cicd` -> Actions -> Setup your workflow

### 2.2 Replace yml code with the below

```yaml
name: Python application test with Github Actions

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.5
      uses: actions/setup-python@v1
      with:
        python-version: 3.5
    - name: Install dependencies
      run: |
        make install
    - name: Lint with pylint
      run: |
        make lint
    - name: Test with pytest
      run: |
        make test

```

## 3.0 Continuous Delivery on Azure

### 3.1 