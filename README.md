# Firebasin

A Python Library for the [Firebase](https://firebaseio.com/) API.

View this documentation  [here](https://gochomugo.github.io/firebasin/ "Documentation on firebasin") in a better and clean page.

## Quick Stats

[![Build Status](https://travis-ci.org/GochoMugo/firebasin.svg?branch=master)](https://travis-ci.org/GochoMugo/firebasin)

|Topic                        | Details                |
|----------------- |---------------:|
|Version                    | 0.1.2                   |
|Python                     | 2.6, 2.7, 3.2, 3.3, 3.4 |
|Development Status | Stable            |
|Last Updated            | 18th July, 2014    |

> The Development Status of this Library warrants me to say that this API will keep growing. That's a good thing, right?

## Prerequisites

**requests** Library

This library depends on the `requests` library. If you have **NOT** installed the [requests](docs.python-requests.org/ "Requests Home Page") library yet

`sudo pip install requests` 

## Installation

To Install this library into your machine:

`sudo pip install firebasin`

If you already have firebasin installed, you could **upgrade** by:

`sudo pip install --upgrade firebasin`

## Getting Started

1.  Import the `firebasin` library

`import firebasin`

2.  Create a Firebase instance

`my\_firebase = firebasin.Firebase("https://my-firebase\_name.firebaseio.com")`

While creating the Firebase instance, you might pass an access token to `auth` argument like shown below. The access token will persist across all transactions with the Firebase, unless you explicitly pass another access token to a method.

```python
my\_firebase = firebasin.Firebase("https://my-firebase\_name.firebaseio.com", auth="access\_token\_here")
\# Ensure that you have defined the function you pass to "error=".
```

Firebase is now ready to be used. It's time to conquer the World.

### Basic Methods

These are the basic methods available to any Firebase

`.root()`

Get a Firebase reference to the root of the Firebase. 

* Requires No arguments.
* Returns a String

Example:

```python
print(my\_firebase.root())
```
<hr>

`.name()`

Get the last token of this location's URL.

* Requires No arguments
* Returns a String

Example:

```python
print(my\_firebase.name())
```

<hr>

`.attr()`

Returns a tuple containing some details of the Firebase.

* Requires No arguments
* Returns a tuple e.g (time\_of\_creation, url\_of\_firebase, auth\_token)

```python
print(my\_firebase.attr())
```

<hr>

`.parent()`

Get a Firebase instance with the parent Location as its URL

* Requires No arguments
* Returns a new Firebase Instance

Example:

```python
new\_parent = my\_firebase.parent()
```

<hr>

`.child(**kwargs)`

Get a Firebase instance with a child location as its URL

* Requires:
    * point="relative\_URL\_to\_child"
* Returns a Firebase Instance

Example:

```python
new\_child = my\_firebase.child(point="/child")
```

### Asynchronous Methods

A Firebase instance has different methods that let you transact with it. The methods shown below are **Asynchronous**. This means that:
    
* Python won't wait for the response, it will continue executing following lines of code
* You should NOT assign return value to a variable. Doing this will always assign `None`
* You will have to use a callback to manipulate returned data from a request

**Note:** There are also **Synchronous** methods you can use. Read along to see how to use them.

`.get(**kwargs)`

Get data a particular location on your Firebase

* Requires:
    * point="location\_to\_read"
    * auth="access\_token" (Optional)
    * callback=name\_of\_a\_function (Optional)
    * callbacks=\[list\_of\_callbacks\] (Optional)
    * error=name\_of\_a\_function (Optional)
* Returns `None`. Data fetched is passed to the callback function, if specified.

Example:

```python
\# Callback function definition
def hello(data):
    print(data)
\# Another Callback
def world(data):
    with open('/home/user/.firebase-data', 'w') as data_file:
        data_file.write(data)
\# Making a 'GET' request
my\_firebase.get(point="/child", auth="Ja2f29f4Gsk2d3bhxW2d8vDlK", callback=hello)
\# A Request with multiple callbacks
my\_firebase,get(point="/child", auth="Ja2f29f4Gsk2d3bhxW2d8vDlK", callbacks=\[hello, world\])
```

<hr>

`.put(**kwargs)`

Write data into your Firebase

* Requires:
    * point="location\_to\_read"
    * data="data\_to\_put"
    * auth="access\_token" (Optional)
    * callback=name\_of\_a\_function (Optional)
    * callbacks=\[list\_of\_callbacks\] (Optional)
    * error=name\_of\_a\_function (Optional)
* Returns `None`. Data you just put is passed to the callback function, if specified.

<hr>

`.delete(**kwargs)`

Delete data from your Firebase

* Requires:
    * point="location\_to\_delete"
    * auth="access_token" (Optional)
    * callback=name\_of\_a\_function (Optional)
    * callbacks=\[list\_of\_callbacks\] (Optional)
    * error=name\_of\_a\_function (Optional)
* Returns `None`. A string saying `null` is passed to the callback function, if specified.

<hr>

`.export(**kwargs)`

Export data from a location on your Firebase

* Requires:
    * point="location\_to\_read"
    * auth="access\_token" (Optional)
    * path="UNIX\_path\_to\_file\_to\_write\_to" (Optional)
    * callback=name\_of\_a\_function (Optional)
    * callbacks=\[list\_of\_callbacks\] (Optional)
    * error=name\_of\_a\_function (Optional)
* Returns `None`. Data you just exported is passed to the callback function, if specified.

**Note:** if the `path` argument is not given, this will behave similar to `.get(**kwargs)`

<hr>

`onChange(**kwargs)`

Poll for changes at a Location on your Firebase.

* Requires:
    * point="location\_to\_read"
    * auth="access\_token" (Optional)
    * callback=name\_of\_a\_function (Optional)
    * callbacks=\[list\_of\_callbacks\] (Optional)
    * error=name\_of\_a\_function (Optional)
    * ignore\_error=True (Optional) - Whether to keep watching incase of an error or make it Stop
    * frequency=10 (Optional) - The number of seconds between checks on the Firebase
    * fetches=-1 (Optional) - The number of times to look at the Firebase before automatically stopping. It is a _positive_ integer. Giving a negative integer makes it be ignored. 
* Returns an Object representing the Watch. This object has one method:
    *   _.stop(number\_of\_seconds)_ - Passing an integer will cause the watch to be stopped after the specified number of seconds. If no number is passed, the watch will be stopped as soon as Possible.

```python
def keep\_printing(data):
    print(data)
\# Creates a watch Object
watch = my\_firebase.onChange(point="/watch_here", callback=keep\_printing) 
watch.stop(20)
\# The Watch will be stopped after 20 seconds
```

**Note:** 

* there's **No** synchronous flavor of this method. For obvious reasons.
* the stopping of a watch may take longer or shorter time for the action to kick in. This depends on your System.


### Synchronous Methods

The asynchronous methods are also available in synchronous flavor. To get a method work synchronously, append "_sync" to the name of the method.

Example:

```python
user = my\_firebase.get\_sync("/user")
\# Python will wait for the response before executing the next line of code
\# Callback arguments passed will be ignored
```

### Callbacks and Errors

In all the **asynchronous** methods, a **Callback** can be assigned. The callback will be executed once response is received. In the case of `.onChange()` method, the callback will be called every time data changes at the point specified.

Since version 0.1.2, multiple callbacks may be assigned using the **callbacks** parameter. The methods/functions, placed in a list and passed to the Firebase methods, will be executed sequentially as ordered in the particular list.

Functions assigned to **error** are executed when an error occurs while executing the action. The following errors may be caught:

1. `EnvironmentError`: This is caused by Trying to access a non-existant Firebase or Security Rules violation while trying to execute a query
2. `KeyError`: This is caused when you do **not** pass a required argument to any function
3. `ConnectionError`: This will occur mostly due to Network problems

If you wanted to catch errors according to this classes, you could do:

```python
def caught_an_error(err):
    if err.__class__.__name__ == "KeyError":
        print("KeyError: " + str(err))
    elif err.__class__.__name__ == "EnvironmentError":
        print("EnvironmentError: " + str(err))
    elif err.__class__.__name__ == "ConnectionError":
        print("ConnectionError: " + str(err))
    else:
        print(err.__class__.__name__ + str(err))
```

**Note:** All callbacks and error handling functions should be defined before assigning them to a method.

## Issues

Incase you encounter a bug, even if you could fix it yourself, please share with your fellow Pythonista :-) at the [Issues page](https://github.com/GochoMugo/firebasin/issues "Create an issue here")

## License

Source code for **firebasin** is issued and licensed under the [MIT License](http://opensource.org/licenses/MIT "OSI Page for MIT License").

<center>**Conquer the World with Python**</center>
