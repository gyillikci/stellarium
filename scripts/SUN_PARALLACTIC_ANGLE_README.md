# Sun Parallactic Angle Camera Simulation

## Overview

These scripts simulate the detection of the Sun's parallactic angle changes using a camera with specific specifications. They help answer the question: **"What is the smallest angle change that can be detected in consecutive frames?"**

## Camera Specifications

- **Sensor**: 8 million pixels (approximately 2828 × 2828)
- **Field of View**: 1 degree
- **Angular Resolution**: ~1.27 arcseconds per pixel
- **Sun Coverage**: Sun's diameter (~0.5°) fills approximately 1414 pixels

## Rotational Resolution

The rotational resolution depends on the image processing technique:

| Method | Pixel Precision | Rotational Resolution | Typical Use Case |
|--------|----------------|---------------------|------------------|
| **Basic** | 1.0 pixel | ~291 arcsec (0.081°) | Simple edge detection |
| **Centroiding** | 0.1 pixel | ~29 arcsec (0.008°) | Feature tracking (sunspots) |
| **Advanced** | 0.01 pixel | ~2.9 arcsec (0.0008°) | Cross-correlation |

## Scripts

### 1. `sun_parallactic_angle_simulation.ssc`

**Full simulation** that tracks the Sun's parallactic angle over 6 hours.

**Features:**
- Samples parallactic angle at configurable frame rate (default 30 fps)
- Calculates frame-to-frame changes
- Determines how many frames needed to detect rotation
- Provides statistical analysis

**Usage:**
1. Open Stellarium
2. Press F12 to open Script Console
3. Load and run: `scripts/sun_parallactic_angle_simulation.ssc`
4. View results in the output log

**Adjustable Parameters** (edit the script):
```javascript
var FRAME_RATE = 30;          // Try: 1, 10, 30, 60 fps
var DURATION_HOURS = 6;       // Simulation duration
var CAMERA_FOV_DEG = 1.0;     // Field of view in degrees
var CAMERA_PIXELS = 8000000;  // Sensor resolution
```

**Output:**
- Camera specifications summary
- Time-series data: timestamp, altitude, azimuth, parallactic angle, change per frame
- Statistical analysis: min/max/average changes
- Detection capability for each processing method
- Recommendations based on results

### 2. `sun_parallactic_quick_test.ssc`

**Quick test** showing parallactic angle at key times of day.

**Features:**
- Tests specific times: sunrise, mid-morning, noon, mid-afternoon, sunset
- Shows when changes are fastest/slowest
- Visual demonstration with labeled views
- Faster runtime (~30 seconds vs several minutes)

**Usage:**
1. Press F12 for Script Console
2. Load and run: `scripts/sun_parallactic_quick_test.ssc`
3. Observe the visual demonstration

**Output:**
- Comparison table of parallactic angle at different times
- Rate of change analysis (arcsec/minute)
- Visual demonstration showing Sun position at 3 key times

## Understanding the Results

### Parallactic Angle

The **parallactic angle** is the angle between:
- The line from the object to the zenith (straight up)
- The line from the object to the celestial north pole

It represents how "tilted" the sky appears relative to the horizon-based coordinate system.

### When Changes Are Fastest

Parallactic angle changes most rapidly when:
1. **Sun is near the horizon** (sunrise/sunset)
2. **Observer is at mid-latitudes** (30-60° latitude)
3. **Sun is moving perpendicular to meridian**

Changes are slowest when:
1. **Sun crosses the meridian** (local noon)
2. **Observer is at equator** (parallactic angle ≈ 0)
3. **Object is near celestial pole**

### Frame-to-Frame Detection

Based on typical results:

- **At sunrise/sunset**: Changes of ~0.1-2 arcsec/sec
  - Detectable in **1-10 frames** with centroiding
  - Detectable in **1 frame** with advanced processing

- **Near noon**: Changes of ~0.01-0.1 arcsec/sec
  - Requires **10-100 frames** with centroiding
  - Requires **1-10 frames** with advanced processing

- **Recommended frame rate**: 1-30 fps
  - Higher rates provide redundancy and better averaging
  - Lower rates (1 fps) still capture changes over time

## Practical Recommendations

### For Camera Setup

1. **Timing**: Observe during morning (7-10 AM) or evening (4-7 PM) for maximum change rate
2. **Frame Rate**: 10-30 fps provides good balance between data volume and detection capability
3. **Processing**: Use sub-pixel techniques (centroiding on sunspots or limb features)
4. **Duration**: Even 5-10 minutes provides measurable rotation

### For Data Analysis

1. **Feature Tracking**: Track sunspots or limb features for best rotation detection
2. **Cross-Correlation**: Use image correlation between frames to detect sub-pixel shifts
3. **Time Averaging**: Average multiple frames to reduce noise
4. **Calibration**: Account for atmospheric refraction and camera rotation

## Mathematical Background

The parallactic angle is calculated using (Meeus, Astronomical Algorithms, p.98):

```
q = atan2(sin(H), tan(φ)·cos(δ) - sin(δ)·cos(H))
```

Where:
- **q** = parallactic angle (radians)
- **H** = hour angle (radians)
- **φ** = observer's latitude (radians)
- **δ** = declination (radians)

The rotational resolution at the Sun's limb:

```
θ = (Δp / r) × FOV
```

Where:
- **θ** = rotational resolution (degrees)
- **Δp** = pixel precision (pixels)
- **r** = Sun's radius in pixels
- **FOV** = camera field of view (degrees)

## Source Code Reference

Stellarium's parallactic angle calculation is in:
- `src/core/StelObject.cpp:106-119` - Main calculation
- `src/core/modules/Planet.cpp:3641-3651` - Sun corona orientation
- `src/scripting/StelMainScriptAPI.hpp:236` - Script API documentation

## Customization

### Change Observer Location

Edit the scripts to modify location:
```javascript
// Latitude, Longitude, Altitude (m), Time zone offset, Name, Planet
core.setObserverLocation(-122.4, 37.8, 10, -8, "San Francisco", "Earth");
```

### Change Date/Season

Different seasons affect the Sun's declination:
```javascript
core.setDate("2024-06-21T06:00:00", "utc", false);  // Summer solstice
core.setDate("2024-12-21T06:00:00", "utc", false);  // Winter solstice
core.setDate("2024-03-20T06:00:00", "utc", false);  // Spring equinox
```

### Different Camera Specs

Modify these variables for your specific camera:
```javascript
var CAMERA_PIXELS = 20000000;     // 20 MP camera
var CAMERA_FOV_DEG = 0.5;         // Narrower FOV (more zoom)
```

## Further Reading

- Meeus, J. (1998). *Astronomical Algorithms*, 2nd ed., Chapter 14
- Stellarium documentation: https://stellarium.org/doc/
- Parallactic angle applications in astronomy and navigation

## License

These scripts are released into the Public Domain. Use freely for educational, research, or commercial purposes.

---

**Created by**: Claude (AI Assistant)
**Date**: 2025
**Version**: 1.0
