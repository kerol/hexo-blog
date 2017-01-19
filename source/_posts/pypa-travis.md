---
title: pypa-travis
date: 2017-01-18 15:06:49
tags: [pypa, travis]
---
### Preface
This article is about how to build your own pypa package and use auto testing tool travis.

### Pypa
[Reference](https://packaging.python.org/distributing/)
#### Step 1: Requirements
For (https://pypi.python.org/pypi): `twine<=1.6.5`
For (https://pypi.io): use latest twine
Here I use twine 1.6.5
```
pip install twine==1.6.5
```
#### Step 2: Configure your project
<!-- more -->
- setup.py
- setup.cfg
- your-package
##### setup.py
[Reference](https://github.com/kerol/kepython/blob/master/setup.py)
```
setup(
    name='kepython',
    version='0.1.2',
    description='Python utils by kerol',
    long_description=long_description,
    url='https://github.com/kerol/kepython',
    author='Kerol Gao',
    author_email='ikerol@163.com',
    license='Apache',
    packages=['kepython',],
    include_package_data=True,
    install_requires=[
        'flask>=0.12',
        'scrapy>=1.3.0',
    ],
    classifiers=[
       'Development Status :: 3 - Alpha',
        'Intended Audience :: Developers',
        'License :: OSI Approved :: Apache Software License',
       'Programming Language :: Python :: 3.6',
   ],
    entry_points={
        'console_scripts': [
            'kepython=kepython:main',
        ],
    },
)
```
##### setup.cfg
```
[bdist_wheel]
universal=1
```
#### Step 3: Packaging your project
```
python setup.py sdist
python setup.py bdist_wheel
# test
# important to do unit test
```
#### Step 4: Register and upload
Register yourself in pypa.
```
twine register dist/mypkg.whl
twine register dist/mypkg.tar.gz
twine upload dist/*
```
After serveral minutes, you will fine your packages.

### Travis
todo
