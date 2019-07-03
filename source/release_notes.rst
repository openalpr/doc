
********************
Release Notes
********************

2.7.101
========

Release Date: July 1, 2019

  - Significantly faster CPU processing (2x faster on 64-bit Intel processors)
  - Performance improvement on Nvidia GPUs (roughly 40% faster processing)
  - Decreased memory usage on both CPU and Nvidia GPUs
  - Small accuracy improvement across all countries
  - Reduced false positives on road signs and billboards

Web Server:

  - New Dispatch View for real-time alerting (alerts displayed on a map)
  - Text Messaging for alerts
  - Improved user management
  - Additional management REST APIs
  - Usability enhancements for Analytic Reports
  - Improved efficiency

Bug fixes:

  - ONVIF discovery stability issue
  - AlprStream Python binding initialization error when using multiple threads
  - C# Vehicle Classifier binding bug

2.6.103
=========

Release Date: March 26, 2019

  - Windows GPU support for both Agent and SDK
  - Added Support for Singapore and Malaysia license plates
  - Extended Support for all countries in the Gulf Cooperation Council.  All emirates in UAE are now differentiated
  - Extended support for Mercosur format in Brazil
  - Improved accuracy for Japanese plates (smaller provinces, green plates, and low-numbered plates)
  - GPU support for multiple cards in one server
  - Encoding JPEG on GPU when available (reduces CPU load)
  - Small (~10KB) JPEG crop for the plate and vehicle are sent with plate group JSON result by default
  - Supporting custom branding/white-labeling for OpenALPR configuration utility
  - More efficient H264 decoding
  
Bug fixes:

  - Fixed performance issue related to retrieving recorded video in the agent
  - Added upload option for second beanstalkd tube
  - Writing heartbeat data to second tube
  - Added disk size stats to heartbeat data
  - Added command line option to toggle GPU on alpr command line program
  - Fixed rare race condition crash in stream processing
  - Faster GPU initialization

API Changes:

  - Alpr: Can initialize Alpr instance as GPU or CPU using the constructor (without modifying the configuration file)
  - Alpr: Added getters for GPU settings
  - Alpr: Deprecated prewarp function.  It no longer contributes to accuracy
  - Alpr: Added GPU batching functions to C API (can be used on Windows)
  - AlprStream: Added vehicle_jpeg crop value to AlprGroupResult
  - AlprStream: Added Python binding

Archived Windows Installers

  - https://deb.openalpr.com/windows-agent/openalpr-agent-2.6.103-signed.exe
  - https://deb.openalpr.com/windows-sdk/openalpr64-sdk-2.6.103.zip

Archived Linux Repositories

  - deb https://deb.openalpr.com/snapshot/bionic-2.6.103/ bionic main
  - deb https://deb.openalpr.com/snapshot/xenial-2.6.103/ xenial main
  - deb https://deb.openalpr.com/snapshot/jetson32-2.6.103/ jetson32 main
  - deb https://deb.openalpr.com/snapshot/jetson40-2.6.103/ jetson40 main



2.6.101
=========

Release Date: October 5, 2018

  - Accuracy improvement across all countries
  - H264 video recording in the agent (simplistic NVR capability)
  - Russian license plate recognition with province support
  - Improved plate tracking for stop-and-go traffic
  - Recognition for additional countries in the middle-east (Qatar, Egypt, Bahrain, Iraq, Jordan, Kuwait, Oman)
  - Extended European support to include two-line license plates
  - Extended Brazil and Argentina support to include two-line license plates
  - Direction of Travel tagging in the Web UI
  - Analytics reports in the Web UI
  - Added Nvidia support to Ubuntu 18.04 using CUDA 10.0

Bug fixes:

  - Video streams better maintain input framerate under high CPU load
  - Improved selection of best plate group images.  Images with a plate and a vehicle outside of the image frame are less likely.
  - Fixed race condition on initialization (thread safety issue) in ALPR API Python binding
  - Fixed .NET binding issue in the "recognize" function when using an encoded image in-memory.
  - Fixed .NET UTF-8 decoding issue (better support for plates with international characters)
  - Fixed video buffering issue in the Agent that, in some cases, caused significant video buffering and delays
  - Fixed issue causing direction of travel value to be set to 0 in certain some cases
  - Fixed bug in the agent where video files (used as cameras on automatic loop) with spaces in the filepath would not load
  - Faster default timer for sending plate groups
  - Fixed issue where results could back up on the agent when processing cameras with extremely busy roads (e.g., highways)
  - Agent buffers and retries POSTs when an HTTP 500 is received

API Changes:

  - AlprStream: Added JSON deserialize function
  - AlprStream: Added option to serialize plate crop image in JSON
  - AlprStream: Added set_location function for mobile applications (GPS lat/lng coordinates will be sent in JSON payload)
  - Alpr: Added JSON deserialize function

Archived Windows Installers

  - https://deb.openalpr.com/windows-agent/openalpr-agent-3.1.2-signed.exe
  - https://deb.openalpr.com/windows-sdk/openalpr64-sdk-2.6.101.zip

Archived Linux Repositories

  - deb https://deb.openalpr.com/snapshot/bionic-2.6.101/ bionic main
  - deb https://deb.openalpr.com/snapshot/xenial-2.6.101/ xenial main
  - deb https://deb.openalpr.com/snapshot/jetson32-2.6.101/ jetson32 main

2.5.103
=========

Release Date: March 5, 2018

  - Massive Nvidia GPU performance improvement 100%+ faster throughput (fps) on desktop GPU, even larger improvement on Jetson
  - Performance improvement for CPU-only processing (~10-15%)
  - Significantly improved efficiency and some usability improvements for live video preview
  - Small accuracy improvement for all supported countries
  - Small accuracy improvement for recognition on GPU
  - Vehicle: Improved make/color/body type detection.
  - Vehicle: Added vehicle orientation and year.
  - Vehicle: Added make/models for many countries (previously this was exclusively USA cars)
  - New countries: Japan and Indonesia
  - Added province detection for Australia, Japan, UAE, South Africa, Indonesia
  - Better accuracy in Great Britain for 1 and 2-line license plates
  - Web Server: Professional PDF report export for plate reads and alerts
  - Web Server: Search Auditing
  - Web Server: Search by State/Province
  - Web Server: Support both whitelists and blacklists for alerting
  - Web Server: Automatically add camera name/agent hostname to new agents

Bug Fixes:

  - Fixed rare race condition when video reconnects after losing connection
  - Fixed small memory leak when parsing metadata for certain video formats

API Changes:

  - AlprStream: Added queue_empty to API response object
  - Vehicle: Added "country" parameter to vehicle classifier
  - Vehicle: Added orientation/year to output
  - State Recognition: Prefixing USA states with "us-" to be consistent with other countries

Archived Windows Installers

  - https://deb.openalpr.com/windows-agent/openalpr-agent-3.1.1-signed.exe
  - https://deb.openalpr.com/windows-sdk/openalpr64-sdk-2.5.103.zip

Archived Linux Repositories

  - deb https://deb.openalpr.com/snapshot/bionic-2.5.103/ bionic main
  - deb https://deb.openalpr.com/snapshot/xenial-2.5.103/ xenial main
  - deb https://deb.openalpr.com/snapshot/jetson32-2.5.103/ jetson32 main

2.5.101
=========

Release Date: December 3, 2017

Features:

  - New ALPR configuration GUI for Windows/Linux agents.
  - Added South Korea and Great Britain training data
  - Better handling for plates extending beyond the edge of the image
  - Improved support for automatically detecting RTSP/MJPEG stream URL decoder types
  - Improved (more accurate/smoother) statistics generated from AlprStream
  - Removed unused GStreamer plugin DLLs from package
  - Agent now stores and uploads "plate groups" by default - 3-10x longer image storage retention

Bug Fixes:
  - Added missing ROIs from ALPR results
  - Fixed relatively small memory leak (~200KB / day of continuous usage) in AlprStream

API Changes
  - ALPR API: Added perspective corrected plate crop (JPEG bytes) to ALPR results object
  - ALPR API: Added license key parameter to constructor
  - AlprStream: Added "motion_stickiness" parameter to adjust how long motion detection lingers over areas
  - AlprStream: Added configurable GStreamer pipeline for more comprehensive input stream support
  - AlprStream: Added "candidates" to each plate group object which contains top N other possible plate numbers
  - AlprStream: Added "user_data" value that contains arbitrary user_data sent along with the group/plate results
  - AlprStream: Added image width/height to AlprGroup results



2.4.102
==========

Release Date: October 11, 2017

Major new features:
  - Nvidia GPU Support for Linux enables high-speed ALPR acceleration.  http://www.openalpr.com/nvidia.html
  - New country support: Argentina, Brazil, China, India, New Zealand, Saudi Arabia, South Africa, Thailand, and the UAE
  - Accuracy improvement for North America, Europe, and Australia
  - Accuracy improvement for US state recognition
  - 30-50% faster recognition performance on Windows (Windows and Linux performance are now roughly the same)
  - New AlprStream API officially released - http://doc.openalpr.com/sdk.html#alprstream-api
 
Minor new features
  - Parked car detection (when a plate is detected subsequent times and has not moved, it is flagged as parked)
  - Direction of travel indication (the direction that the plate has traveled is available with the results)
  - Open Sourced the OpenALPR rolling image data storage module (RollingDB) under LGPL license: https://github.com/openalpr/rollingdb
  - Reduced overall memory usage
  - Added Australian province detection
  - Small accuracy improvement for vehicle classifier
  - Added option to skip state/province recognition - reduces computation cost if state recognition is not needed
  - Added option to skip plate recognition (library returns candidate plate locations without character identification)
  - Added an agent option to upload only plate group data
  - Significantly better accuracy for US commercial tractor-trailer license plates
  - Added plate number overlay to the debug visualization
 
API Changes:
  - Deprecated setDetectRegion and moved the option to openalpr.conf
  - Added recognize_batch functions that accept and process multiple images simultaneously for optimal GPU processing efficiency
 
Bug fixes:
  - Fixed .NET binding in SDK to point to proper shared library
  - Increased default number of upload threads in the agent to better support high-latency connections
  - Fixed various user-interface bugs for Forensic Plate Finder
  - Fixed grouping bug that would, in certain cases, produce duplicate results
  - Better handling of disk storage when the agent PC is low on disk space
  - Including more detailed logging for agent video connections
  - Fixed agent bug that allowed incoming video frames to buffer without bound under certain configurations
  - Numerous minor bug fixes


2.4.101
========

Release Date: July 7, 2017

Major Features:
  - Upgraded recognition engine.  Significant accuracy improvement for US, Europe, and Australia.  Recognition frame rate is slightly improved.
  - Higher accuracy US state of issuance recognition.  Also includes Canada and Mexico
  - Refactored Stream processing into a library.  The "alprstream" API is released as a beta -- the API is subject to change before the official release.  The alprstream API allows application integrators to embed OpenALPR video processing 
  - New product: Video file processing utility (OpenALPR Forensic Plate Finder)
 
 Minor Features:
  - Higher accuracy vehicle recognition
  - More even frame recognition when video rate exceeds processing capacity
  - Faster ALPR group results
  - Remote opt-in crash reporting on Linux
  - Option to store a single plate image for a group
  - Pruned unnecessary gstreamer libraries from Windows installer package
  - Applying detector mask to motion detection (improves processing efficiency)
  - Config option to only upload plate groups
  - Improved websockets responsiveness (supports parallel requests)

 Bug fixes:
  - Fixed race condition that caused image write thread failure on startup
  - Fixed thread safety issue with plate grouper
  - Fixed invalid JSON output (duplicate img_width/img_height)


2.3.109
========

Release Date: August 29, 2016

Major Features:
  - Accuracy/performance improvements
  - Vehicle make/model/color recognition (previously available as a separate API) integrated with ALPR Agent
  - Refactored Windows Agent/SDK code to remove all Linux emulation.  Windows binaries run fully natively.

Bug fixes:
  - Resolve a small memory leak


E-mail Notifications
=====================

If you wish to be notified whenever OpenALPR updates are released, please `subscribe to our mailing list <https://list.openalpr.com/subscription?f=Q32MjluxHoVzoBh2N1cKA7634FJdV6vKesAsjym41nZle763Xpl4u23RFgzJ763rcvqrAwdp3IrOBH0eyQVGMajiT8Yg>`_.  


