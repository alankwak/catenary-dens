# Version 1.1.0 - 8/22/2024
### New Features:
- can now use ```dens_from_eq()``` and ```dens_from_spline()``` to find loaded cable densities, using argument ```type="loaded"```
- mass densities are now standardized when returned from ```dens_from_eq()``` and ```dens_from_spline()``` so that their average value is 1, to make comparison easier

### Bug Fixes:
- fixed an issue where matplotlib was not installed and the program stopped due to error
- removed some unnecessary code


# Version 1.0.1 - 8/17/2024
### Bug Fixes:
- slight changes to fix pypi upload
