# Phase 1: Extracting Points from Image

## Tools:

- [Engauge Digitizer](https://akhuettel.github.io/engauge-digitizer/)

## Procedure:

1. Install the software using your package manager. On CachyOS (Arch): Install
   it from the AUR.

```fish
paru -S aur/engauge
```

2. Open the software
3. Import the Image
4. Mark at least three points for the 2D Coordinate System
5. Go to Settings -> Color Filter
6. Since the curve to digitize is just a different color from the grid, use hue.
7. Limit the range to blue.
8. Digitize the curve.
9. Export the points to a CSV file.
10. Repeat for the remaining images.
