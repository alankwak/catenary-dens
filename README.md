# catenary-dens v1.0.0
Python program for finding catenary shapes and mass densities

This program was created as a part of the Summer Undergraduate Reasearch Experience at Carthage College. The goal of the project was to answer the question "Is it possible to find the mass density of a hanging cable given its shape?", which is the inverse of the normal "forward" problem that asks if it is possible to find the shape of a hanging cable given its mass density. The program contains functions that compute the answers to both the forward problem and the inverse problem.

dependencies:
- numpy
- scipy
- sympy, if using dens_from_eq()

The full source code is provided with annotations on how to use each function, and what is happening as the program runs. For convenience, here are the main functions within the program and how to use them.

These functions find the shape a cable would make if hung from its ends given horizontal distance between the ends, the vertical distance between the ends, the length of the cable, and the mass density of the cable with respect to arc length. There are two more parameters that are brute-forced to create the desired curve. These are the initial slope of the curve, as well as the horizonal tension in the curve.

## For the Forward Problem

### find_parameters() 
-- Main function to brute force h (horizontal tension / 9.8) and the initial slope of a desired cable, given characteristics of the curve
| required/optional           |parameter, type       | description                                                                                                        |
|-----------------------------|----------------------|--------------------------------------------------------------------------------------------------------------------|  
|  required                   | dens, function:      | user-created function dens() that takes one argument and returns the mass density of the user's cable at specified arc length (if using free-hanging model) or horizontal distance (if using loaded model) |
|  required                   | xdist, float:        | desired horizontal distance between the two endpoints of the cable |
|  required                   | ydist, float:        | desired vertical distance between the two endpoints of the cable |
|  required                   | length, float:       | desired length of cable |
|  optional, default=None     | guess_h, float:      | allows the user to set a starting value when searching for h, may decrease search times |
|  optional, default=None     | guess_dydx, float:   | allows the user to set a starting value when searching for the initial slope of the curve, not recommended |
|  optional, default=.01      | thresh, float:       | represents the maximum x/y-distance a generated curve's endpoint can be compared to the desired endpoint in order for the program to count a curve as successful, lower values take longer/more loops but give generally more accurate results |
|  optional, default=500      | max_attempts, int:   | the maximum number of loops/curves to generate in an attempt to find the desired curve |
|  optional, default=False    | debug, boolean:      | if set to True, prints information about each curve generated while searching for the correct curve |
|  optional, default=hanging  | type, function:      | specifies if the free-hanging or loaded cable function should be used to solve the differential equation |

### find_catenary(dens, xdist, ydist, length, thresh=.01, max_attempts=500, debug=False) 
-- Essentially calls find_parameters() with specific arguments to make compute time faster using scaling. Used to find the shape of a free-hanging cable.
| required/optional           |parameter, type       | description                                                                                                        |
|-----------------------------|----------------------|--------------------------------------------------------------------------------------------------------------------|  
|  required                   | dens, function:      |user-created function dens() that takes one argument and returns the mass density of the user's cable at specified arc length (if using free-hanging model) or horizontal distance (if using loaded model) |
|  required                   | xdist, float:        |desired horizontal distance between the two endpoints of the cable |
|  required                   | ydist, float:        |desired vertical distance between the two endpoints of the cable |
|  required                   | length, float:       |desired length of cable |
|  optional, default=.01      | thresh, float:       |represents the maximum x/y-distance a generated curve's endpoint can be compared to the desired endpoint in order for
the program to count a curve as successful, lower values take longer/more loops but give generally more accurate results |
| optional, default=500       | max_attempts, int:   | the maximum number of loops/curves to generate in an attempt to find the desired curve |
|  optional, default=False    |  debug, boolean:     |  if set to True, prints information about each curve generated while searching for the correct curve |

### find_loaded_catenary(dens, xdist, ydist, length, thresh=.01, max_attempts=500, debug=False)  
-- the exact same as find_catenary(), but uses type=loaded in the find_parameters() function call to solve the loaded cable diff eq (e.g. for a cable supporting a road directly beneath it)

### CatSolution Object
All three of these functions return a ```CatSolution``` object, which has the following members:
| member         |description   |
|-----------------------------|----------------------|
|             status, int:     | -1 = failed, desired cable length is not long enough to reach desired cable endpoint   
||0 = success, curve was found with endpoint within desired threshold   
||1 = maximum number of loops occurred or variable increments are too small, returns the curve with the closest distance from desired endpoint to simulated endpoint |
|             message, string:  |    describes result of attempt to find curve |
|             type, string:     |    "Free-hanging" or "Loaded", depending on whether find_catenary() or find_loaded_catenary() was called |
|             h, float:         |    h found as a result of attempt to find curve |
|             idydx, float:     |    initial slope found as a result of attempt to find curve |
|             x, array of floats:  | list of x-coordinates that make up curve |
|             y, array of floats:  | list of y-coordinates that make up curve |

It is recommended to use find_catenary() or find_loaded_catenary() and not find_parameters(). To create the density function, define a function that inputs an arc length and outputs the density at that arc length. For example, for a catenary of constant mass density, one might define:
```{python}
def density1(s):
  return 2
```
For a catenary with a linearly increasing mass density:
```{python}
def density2(s):
  return s
```
The resulting shape could then be obtained and plotted through
```{python}
cat = find_catenary(density2, 3, 1, 5)
```
You can print the ```CatSolution``` object to output relevant information about the curve as well as plot what the resulting curve looks like with any plotting package. For example,
```{python}
import matplotlib.pyplot as plt

print(cat)
plt.plot(cat.x, cat.y)
```
![image](https://github.com/user-attachments/assets/6b519b9a-6063-4527-8b60-b71ec1d5953e)

## For the Inverse Problem
