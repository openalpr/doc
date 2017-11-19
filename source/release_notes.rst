
********************
Release Notes
********************

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

