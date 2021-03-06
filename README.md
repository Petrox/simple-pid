# simple-pid

[![Travis](https://travis-ci.com/m-lundberg/simple-pid.svg?branch=master)](https://travis-ci.com/m-lundberg/simple-pid)
[![PyPI](https://img.shields.io/pypi/v/simple-pid.svg)](https://pypi.org/project/simple-pid/)
[![Read the Docs](https://img.shields.io/readthedocs/simple-pid.svg)](https://simple-pid.readthedocs.io/)
[![License](https://img.shields.io/github/license/m-lundberg/simple-pid.svg)](https://github.com/m-lundberg/simple-pid/blob/master/LICENSE.md)

A simple and easy to use PID controller in Python. If you want a PID controller without external dependencies that just works, this is for you! The PID was designed to be robust with help from [Brett Beauregards guide](http://brettbeauregard.com/blog/2011/04/improving-the-beginners-pid-introduction/).

Usage is very simple:

```python
from simple_pid import PID
pid = PID(1, 0.1, 0.05, setpoint=1)

# assume we have a system we want to control in controlled_system
v = controlled_system.update(0)

while True:
    # compute new ouput from the PID according to the systems current value
    control = pid(v)
    
    # feed the PID output to the system and get its current value
    v = controlled_system.update(control)
```

Complete API documentation can be found [here](https://simple-pid.readthedocs.io/en/latest/simple_pid.html#module-simple_pid.PID).

## Installation
To install, run:
```
pip install simple-pid
```

## Usage
The `PID` class implements `__call__()`, which means that to compute a new output value, you simply call the object like this:
```python
output = pid(current_value)
```

The PID works best when it is updated at regular intervals. To achieve this, set `sample_time` to the amount of time there should be between each update and then call the PID every time in the program loop. A new output will only be calculated when `sample_time` seconds has passed:
```python
pid.sample_time = 0.01  # update every 0.01 seconds

while True:
    output = pid(current_value)
```

To set the setpoint, ie. the value that the PID is trying to achieve, simply set it like this:
```python
pid.setpoint = 10
```

The tunings can be changed any time when the PID is running. They can either be set individually or all at once:
```python
pid.Ki = 1.0
pid.tunings = (1.0, 0.2, 0.4)
```

To disable the PID so that no new values are computed, set auto mode to False:
```python
pid.auto_mode = False  # no new values will be computed when pid is called
pid.auto_mode = True   # pid is enabled again
```

In order to get output values in a certain range, and also to avoid [integral windup](https://en.wikipedia.org/wiki/Integral_windup) (since the integral term will never be allowed to grow outside of these limits), the output can be limited to a range:
```python
pid.output_limits = (0, 10)    # output value will be between 0 and 10
pid.output_limits = (0, None)  # output will always be above 0, but with no upper bound
```

To eliminate overshoot in certain types of systems, you can calculate the [proportional term directly on the measurement](http://brettbeauregard.com/blog/2017/06/introducing-proportional-on-measurement/) instead of the error. This can be enabled like this:
```python
pid.proportional_on_measurement = True
```

## Tests
Use the following to run tests:
```
tox
```

## License
Licensed under the [MIT License](https://github.com/m-lundberg/simple-pid/blob/master/LICENSE.md).
