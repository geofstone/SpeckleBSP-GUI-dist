================================================================================
                         SpeckleBSP GUI v3.3.1
                   Graphical Interface for SpeckleBSP Processing
                              Author: Geoff Stone
================================================================================

OVERVIEW
--------
SpeckleBSP GUI is a Windows application that provides an easy-to-use graphical
interface for SpeckleBSP v3.0.1, a bispectrum processing tool for astronomical
imaging. It processes FITS files to create .bsp1 output files for speckle
interferometry analysis. The output .bsp1 files are designed for use with
Dave Rowe's Speckle Tool Box for image reconstruction.


SYSTEM REQUIREMENTS
-------------------
- Windows 10 or later
- Minimum 4GB RAM recommended
- Multi-core processor recommended for optimal performance
- Sufficient disk space for input FITS files and output .bsp1 files


INSTALLATION
------------
1. Download SpeckleBSP-GUI.exe from the releases page
2. Place the executable in any folder on your computer
3. Double-click to run - no installation required
4. On first run, config.json and logs folder will be created automatically


OPERATING MODES
---------------
The program offers two main tabs for different workflows:

1. MAIN TAB - SpeckleBSP Processing
   Single Target Mode:
   - Process FITS files from a single directory
   - Specify custom output file name or use automatic naming
   - Best for processing individual observations

   Batch Mode:
   - Automatically process multiple directories of FITS files
   - Scans subdirectories for FITS files (requires at least 10 files per directory)
   - Automatically names output files based on FITS header information
   - Real-time progress tracking with time estimates
   - Optional output directory for centralized file collection

2. FRINGE PATTERN TAB - Analysis and Visualization (NEW in v3.1+)
   - Compute and display averaged power spectrum from FITS images
   - Interactive autocorrelation display for binary star analysis
   - Automatic peak detection and binary star identification
   - 1D slice plot showing intensity along binary separation axis
   - Real-time display controls (brightness, contrast, colormaps)
   - Dark theme visualization matching main application


SINGLE TARGET MODE USAGE
------------------------
1. Input Directory:
   - Click "Browse" to select a directory containing your FITS files
   - All FITS files (.fit or .fits) in the directory will be processed
   - Images must be the same size (128, 256, 512, or 1024 pixels square)

2. Dark Frame Subtraction (Optional):
   - Check "Enable dark subtraction" to remove thermal noise
   - Select directory containing dark frames
   - Program will automatically create master dark if needed
   - Supports ROI extraction for science frames with regions of interest

3. RTN Correction (Optional - NEW in v3.0):
   - Check "Enable RTN Correction" to remove Random Telegraph Noise pixels
   - Select RTN map file (.npz format from Speckle-processor)
   - Choose correction method:
     * Median of neighbors: Replace RTN pixels with 3×3 median
     * Set to zero: Set RTN pixels to zero value

4. Output File (Optional):
   - Leave blank to use automatic naming based on first FITS file
   - Or specify a custom output filename
   - File will be saved with .bsp1 extension

5. Set Parameters (see Parameters section for details)

6. Click "Run SpeckleBSP" to start processing


BATCH MODE USAGE
----------------
1. Enable Batch Processing:
   - Check the "Enable Batch Processing" checkbox
   - Single Target Mode controls will be disabled

2. Root Directory:
   - Select the top-level directory containing your observation folders
   - Program will scan for leaf directories containing FITS files

3. Dark Frame Subtraction (Optional):
   - Check "Enable dark subtraction" to remove thermal noise
   - Select directory containing dark frames
   - Program will automatically create and cache master darks per directory
   - Intelligently handles ROI extraction for different science frame sizes

4. RTN Correction (Optional - NEW in v3.0):
   - Check "Enable RTN Correction" to remove Random Telegraph Noise pixels
   - Select single RTN map file to use for ALL directories
   - Choose correction method (median or zero)
   - Map dimensions must match science frame dimensions

5. Output Options:
   - "Copy .bsp1 files to:" - Enable to copy results to a central directory
   - "Write to directory only" - Save files ONLY to the specified directory
   - Output Directory - Browse to select where to save/copy .bsp1 files

6. Processing Options:
   - "Reprocess existing .bsp1 files" - Overwrite existing output files
   - "Test mode" - Preview what will be processed without actually running
     (Default: OFF - ready to process immediately)

7. Progress Tracking:
   - Real-time progress bar showing completion percentage
   - Dual progress bars for pre-processing (dark subtraction/RTN) and main processing
   - Current directory being processed (e.g., "Processing directory 3 of 10")
   - Elapsed time and estimated time remaining
   - Per-directory processing times
   - Comprehensive summary upon completion

8. Directory Structure Expected:
   Root/
   ├── Object1_folder/
   │   └── Filter1_folder/
   │       └── (FITS files here)
   └── Object2_folder/
       └── Filter2_folder/
           └── (FITS files here)


FILE NAMING CONVENTIONS
------------------------
Output files are automatically named based on FITS header information:

For Science Targets:
- Format: {target}_{filter}.bsp1
- Example: HD123456_V.bsp1

For Reference Stars (containing "_ref_" in OBJECT header):
- Format: {target}_ref_{filter}.bsp1
- Example: HD789_ref_V.bsp1

The program checks for:
- NAME1* header - used as target name if present
- OBJECT header - used if NAME1* is not found
- FILTER header - used for filter identification


PARAMETERS EXPLAINED
--------------------
The parameters section uses a compact two-column layout:

Left Column:
- Max Frequency (kmax):
  Upper frequency limit for bispectrum calculation (default: 60)
  Higher values = more detail but longer processing

- Max Delta Freq (dkmax):
  Maximum frequency difference in bispectrum (default: 9)
  Controls the range of frequency combinations analyzed

- Mask Radius (pixels):
  Radius in pixels for masking the central star (default: 100)
  Adjust based on your star size and seeing conditions

Right Column (NEW in v1.3.x):
- Mask Interval:
  How often to recalculate the random mask (default: 10)
  Set to 0 to recalculate every time

- Threads:
  Number of processing threads to use
  Automatically detects available CPU cores
  Default: (available cores - 1) for optimal performance
  Example: 8-core system defaults to 7 threads


DARK FRAME SUBTRACTION (NEW in v2.0)
-------------------------------------
The program now supports automatic dark frame subtraction to improve data quality:

Master Dark Creation:
- Automatically builds master dark from multiple dark frames
- Uses median combination to reduce noise
- Caches master darks for efficient reuse
- Multi-threaded processing with progress tracking

Intelligent ROI Handling:
- Automatically detects when science frames use Region of Interest (ROI)
- Extracts matching region from larger master dark frames
- Supports FITS standard 1-based ROIX/ROIY coordinates
- Per-directory caching of extracted ROIs for performance

Benefits:
- Removes thermal noise and hot pixels
- Improves signal-to-noise ratio
- Essential for high-quality speckle interferometry
- Fully automated with minimal user input

Usage:
1. Take dark frames with same exposure time as science frames
2. Place dark frames in a separate directory
3. Enable "Dark subtraction" checkbox
4. Select dark frames directory
5. Process as normal - dark subtraction happens automatically


RTN CORRECTION (NEW in v3.0)
-----------------------------
Random Telegraph Noise (RTN) correction removes pixels that exhibit discrete jumping behavior:

What is RTN:
- Pixels that randomly switch between discrete voltage states
- Can severely degrade speckle interferometry results
- Most common in CMOS sensors at high gain settings

RTN Map File:
- Generated by Speckle-processor RTN analysis tool
- Contains pixel coordinates of detected RTN pixels
- Stored in NumPy .npz format
- Must match dimensions of science frames

Correction Methods:
1. Median of Neighbors:
   - Replaces RTN pixel with median of 3×3 neighborhood
   - Excludes other RTN pixels from median calculation
   - Preserves local image structure
   - Best for most applications

2. Set to Zero:
   - Sets RTN pixels to zero value
   - More aggressive correction
   - Use for severe RTN contamination

Usage Notes:
- Single RTN map applies to all files in batch mode
- RTN correction applied after dark subtraction
- Warns if map dimensions don't match frames
- Typically corrects 3-5% of pixels per frame


FRINGE PATTERN ANALYSIS USAGE (NEW in v3.1+)
---------------------------------------------
The Fringe Pattern tab provides powerful tools for analyzing speckle interferometry
data and identifying binary stars:

1. Computing Fringe Patterns:
   - Select FITS directory containing speckle images (minimum 10 files)
   - Optional: Enable dark subtraction and/or RTN correction
   - Click "Compute Fringe Pattern" to process images
   - Program computes averaged power spectrum from all frames
   - Click "Show Fringe Pattern" to open visualization window

2. Display Modes:
   - Power Spectrum: Shows averaged Fourier transform (fringe pattern)
   - Autocorrelation: Reveals binary star separations as side lobes
   - Toggle between modes using radio buttons at top of window

3. Autocorrelation Features (for Binary Star Analysis):
   - Automatic Peak Detection: Red X markers show detected peaks
   - Binary Pair Identification: Green circles highlight symmetric pair
   - Separation Display: Title shows distance and position angle
   - 1D Slice Plot: Shows intensity profile along binary axis
     * Central peak suppressed (±6 pixels) to reveal side lobes
     * Green dashed lines mark binary separation distance
     * Log scale y-axis for better visibility

4. Display Controls:
   - Logarithmic Scale: Better dynamic range for faint features
   - Asinh Stretch: Alternative scaling for extreme dynamic range
   - Colormap: Choose visualization color scheme
   - Suppress Center Peak: Remove central autocorrelation peak
   - Show Contours: Overlay contour lines on image
   - Contrast/Brightness: Adjust image display
   - Percentile Clipping: Control min/max display values

5. Navigation:
   - Mouse wheel: Zoom in/out
   - Reset Zoom: Return to default view
   - Toolbar: Pan, zoom, save image options

6. Binary Star Measurements:
   - Separation: Displayed in pixels (title and slice plot)
   - Position Angle: Measured from center (0-360°)
   - Slice extends 50% beyond binary separation for context
   - Results visible even when 2D image appears dominated by central peak

Benefits:
   - Non-destructive analysis (does not modify FITS files)
   - Real-time interactive exploration
   - Automatic binary detection saves manual analysis time
   - 1D slice makes weak binaries clearly visible
   - Dark theme reduces eye strain during long analysis sessions


PROCESSING FEATURES
-------------------
- Real-time output display showing processing progress
- Abort button to stop processing at any time
- Automatic detection of reference stars vs science targets
- Skips already processed files (unless reprocess is enabled)
- Saves your directory preferences for next session
- Multi-threaded processing with intelligent CPU utilization
- Comprehensive logging of all processing activities
- Automatic dark frame subtraction with master dark creation
- RTN pixel correction with flexible methods


LOGGING (NEW in v1.3.1)
------------------------
All processing activities are automatically logged for troubleshooting:

- Log Location: logs/ folder in the application directory
- File Format: SpeckleBSP_GUI_YYYYMMDD.log (one file per day)
- View Logs: Click the "View Logs" button to open the log viewer
- Log Contents:
  * All commands executed
  * Processing success/failure for each directory
  * Processing times and statistics
  * Error messages and warnings
  * File copy operations

The log viewer provides:
- Display of recent log entries (last 1000 lines for large files)
- File size information
- "Open in Editor" button to view in external text editor
- "Open Log Folder" button for easy access to all logs


TROUBLESHOOTING
---------------
1. "No FITS files found":
   - Ensure files have .fit or .fits extension (case insensitive)
   - Check that files are in the selected directory

2. "Image size mismatch":
   - All FITS files must be the same dimensions
   - Supported sizes: 128x128, 256x256, 512x512, 1024x1024 pixels

3. Processing errors:
   - Check that FITS files are not corrupted
   - Ensure sufficient disk space for output files
   - Verify FITS headers contain required information
   - Review log files for detailed error messages

4. Batch mode not finding directories:
   - Requires at least 10 FITS files per directory
   - Check directory structure matches expected format

5. Performance issues:
   - Adjust thread count in parameters
   - Ensure input files are on a fast drive (SSD recommended)
   - Close other CPU-intensive applications


OUTPUT FILES
------------
The program creates binary .bsp1 files containing:
- Bispectrum data for speckle interferometry
- Processed information from all input FITS files
- Data optimized for reconstruction algorithms
- Compatible with Dave Rowe's Speckle Tool Box for image reconstruction


CONFIGURATION FILE
------------------
The program saves preferences in config.json:
- Last used directories for input/output
- SpeckleBSP.exe path (if not bundled)
- Batch output directory settings
- Window preferences

This file is created automatically and can be deleted to reset preferences.


TIPS FOR BEST RESULTS
---------------------
- Ensure all FITS files in a directory are from the same observation run
- Use consistent exposure times within each dataset
- Reference star observations should be clearly marked with "_ref_" in filename
- For batch processing, organize files by object and filter
- Use Test mode to verify correct file detection before processing
- Monitor the progress bar and time estimates during batch processing
- Review log files after processing to verify all directories were successful
- For large batch jobs, the time estimation becomes more accurate after a few directories


VERSION HISTORY
---------------
v3.3.1 - Current Version (Stable Release)
- Updated documentation with comprehensive Fringe Pattern Analysis guide
- Stable release with all v3.3.0-beta features

v3.3.0-beta - Dark Theme and UI Improvements
- Dark theme applied to fringe pattern display window
- White text on dark background for all plots and labels
- Improved slice plot visibility with cyan plot line
- Fixed intensity scale to display in white
- SpeckleBSP Executable section starts collapsed by default
- Batch Mode section starts collapsed by default
- Cleaner initial UI layout

v3.2.0-beta - Binary Star Slice Analysis
- Added 1D slice plot through autocorrelation along binary axis
- Automatic detection and marking of binary star peaks
- Central peak suppression (±6px) to reveal side lobes
- Dynamic layout (slice only shown when binary detected)
- Log scale display for better visibility
- Slice extends 50% beyond binary separation

v3.1.0-beta - Fringe Pattern Analysis with Collapsible UI
- Collapsible/accordion-style control sections
- Automatic peak detection in autocorrelation
- Binary star identification and marking
- Visual indicators for detected peaks
- Improved window layout with resizable separator

v3.0.0-beta - RTN Correction
- Added Random Telegraph Noise (RTN) pixel correction
- Support for RTN maps from Speckle-processor (.npz format)
- Two correction methods: median of neighbors or set to zero
- RTN correction integrated into pre-processing pipeline
- Works in both single target and batch modes
- Single RTN map applies to all directories in batch mode

v2.0.1
- Fixed GUI layout issues with window resizing
- Improved notebook widget height handling
- Optimized layout management for better UI stability

v2.0.0 - Dark Frame Subtraction
- Added automatic master dark creation from multiple frames
- Implemented dark frame subtraction for noise removal
- Intelligent ROI extraction for science frames
- Multi-threaded processing with progress tracking
- Per-directory caching for improved performance
- Support for FITS standard 1-based indexing

v1.3.1 - 9/20/2025
- Comprehensive logging system with viewer
- Fixed initialization order bug
- Improved error handling and reporting
- Log files created daily with automatic rotation
- "View Logs" button for easy access to processing history

v1.3.0 - 9/20/2025
- Major batch processing enhancements with progress tracking
- Support for SpeckleBSP v3.0.1 parameters (mask_interval, nprocs)
- Intelligent thread management with CPU core detection
- Compact two-column parameter layout
- Real-time progress bar with time estimates
- Per-directory processing time tracking
- Enhanced batch completion summary
- Test mode defaults to OFF

v1.2.0
- Improved reference star detection
- Batch output directory options
- Dark theme user interface
- Bug fixes and performance improvements


CREDITS
-------
SpeckleBSP GUI - Created by Geoff Stone
SpeckleBSP.exe - Created by Frank Suites
Speckle Tool Box - Created by Dave Rowe

The .bsp1 output files from SpeckleBSP are designed for use with
Dave Rowe's Speckle Tool Box for astronomical image reconstruction.


SUPPORT
-------
For issues or questions:
- Check the log files first for error details
- Visit the GitHub repository for updates and issue reporting
- Review this README for common solutions


================================================================================
                            End of README
================================================================================