
.. _on_premises:

********************
On-Premises ALPR
********************

OpenALPR On-Premises Software runs on your hardware/application without any connection to the internet.  The software can be used for general surveillance purposes, or it can be used as an engine to add LPR capabilities to other applications.

Using OpenALPR On-Premises requires a commercial license.  Please contact info@openalpr.com to discuss licensing options for your project.

OpenALPR Agent
========================

System Requirements
---------------------

The OpenALPR agent scales to make use of as much CPU resources that you can provide.  For example, OpenALPR can utilize up to 100% of the processing power on low-power devices (such as the Raspberry Pi) as well as high-end devices (such as an Intel 32 CPU-core Xeon server).  The latter device is capable of processing sufficiently more video frames per second than the low-power device.

The minimum system requirements for OpenALPR agents are a function of:

  - The speed/angle of the vehicles with respect to the camera (i.e., how long is a legible license plate in-frame)
  - The resolution of the input video
  - The number of cameras streams being analyzed by the agent
  - Other CPU load on the system
  - The volume of vehicles (i.e., are vehicles seen constantly or sproadically)

As a rough rule-of-thumb, an OpenALPR agent running on a latest-generation Intel i5 desktop-class CPU can process roughly 20 frames of 720p video per second.  That translates to being able handle 3 cameras for low-speed vehicles, 2 cameras for medium-speed vehicles, and 1 camera at high-speed.  Of course, your mileage may vary, and it is always best to benchmark performance on your own hardware analyzing your video.

Memory and disk requirements are relatively constant.  Once OpenALPR initializes, the process uses roughly 500MB per analysis thread (CPU core), and that memory usage does not increase.  The agent records JPEG images to disk as plates are recognized, however Disk IO is usually not a bottleneck.

The OpenALPR Agent performance can also be drastically accelerated by Nvidia GPU hardware.  This requires a custom binary compilation for the target Linux hardware system.  We offer this as a service for a consulting fee.  Please contact info@openalpr.com to discuss.


Installation
---------------

Windows
.........

  - Download the `OpenALPR Windows Installer <http://deb.openalpr.com/windows/openalpr-latest.exe>`_
  - Install the program onto your PC
  - Start the "Configure OpenALPR" program after the install completes
  - Depending on how you wish to use the OpenALPR agent, you may choose one of four options for Data Destination:

  .. image:: images/agent-windows-config.png
      :scale: 100%
      :alt: Windows Agent Configuration


  1. **OpenALPR Cloud** - Sends data to https://cloud.openalpr.com -- requires an active OpenALPR Cloud Account.
  2. **OpenALPR On-Premises Web Server** - You have installed the OpenALPR Web Server on a Linux machine and wish to point the agent data to that server
  3. **Generic HTTP Url** - You wish to integrate OpenALPR with your own application.  You can point the data to your own HTTP endpoint
  4. **Local Queue** - You want the OpenALPR data to be stored on a local beanstalkd queue.  Your application will programmatically drain the queue to process results.

If you are intending to use the agent for surveillance purposes, you will most likely select the "OpenALPR On-Premises Web Server".  Options 3 and 4 are useful if you are an application developer 
and are integrating OpenALPR data into your own application.


Linux
.........

  1. Download the Ubuntu 16.04 64-bit install DVD image and burn to a DVD.
  2. Follow this `installation guide <http://www.ubuntu.com/download/desktop/install-ubuntu-desktop>`_ to install Ubuntu 16.04 64-bit.
  3. Run the following command from the terminal:

  ```
  bash <(curl http://deb.openalpr.com/install)
  ```

  4. To connect the agent to the OpenALPR Cloud, choose “install_agent”.
  5. When prompted if you wish to register the agent, type "n" to choose no.
  6. If you wish to register the agent with an On-Premises web server, type the following command:
     - alprlink-register -w http://[ip_address_of_web_server]
     - Type in your e-mail address and password that you used to install the web server


Docker
............

  1. Run the command **docker pull openalpr/commercial-agent** to pull the latest OpenALPR Docker Commercial Agent.
  2. Run the following command to register the agent with the service:
     - docker run -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/commercial-agent alprlink-register

  3. Start the docker container with the following command:
     - docker run -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/commercial-agent


.. _commercial_config_options:

Configuration
---------------

Agent Properties
..................


Each OpenALPR agent can either be configured manually (via editing the agent configuration files) or via the OpenALPR Web Server.  This section discusses manually configuring the OpenALPR agent.  Configuring the agent through the OpenALPR Web Server is described in the Web Server section of the documentation.

The primary configuration file for the OpenALPR agent is located in the **/etc/openalpr/alprd.conf** file.  There is also a default configuration file in **/usr/share/openalpr/config/alprd.defaults.conf** that contains all of the default settings for the agent for your reference.  Any value you change in alprd.conf takes precedence over the default configuration.

The following configuration options are available to be modified by editing the alprd.conf file.  After adding or changing values in the configuration file, you must restart the OpenALPR agent process.


Image Storage Configuration
#############################

The commercial daemon maintains a maximum disk quota and automatically cleans up after itself.  Images stored to disk are removed on a FIFO-basis.  These parameters control the location, image quality, and maximum size of the local iamge storage. 

.. code-block:: ini

    store_plates = 1
    store_plates_jpeg_quality = 85
    store_plates_location = /var/lib/openalpr/plateimages/
    store_plates_maxsize_mb = 8000


Upload Configuration
#############################

You may enable or disable uploading.  When the agent is running, all plate results will first be placed onto a local beanstalkd queue.  If the upload_data parameter is set to "1" for enabled, then the agent will pull results off the queue and send them to the configured "upload_address" endpoint as an HTTP POST.  The upload_threads parameter specifies the number of simultaneous data uploads.  Generally, the number of threads would only need to be increased if the web server is slow to respond or if there is exceptionally high network latency.

.. code-block:: ini

    upload_data = 1
    upload_address = https://cloud.openalpr.com/push/
    upload_threads = 2


Company ID (required)
#############################

The company_id value is sent in the JSON payload for all results.  When connecting to an OpenALPR web server, this value is configured for you automatically.  However, when configuring the agent manually, the value is not used, but it is still required.  Any alphanumeric string will suffice.

.. code-block:: ini

    company_id = abc-123


Motion Detection
#############################

Motion detection is an effective way to improve efficiency and get more plate reads from the available processors.  The ALPR processing will only analyze frames with movement, and will ignore areas of the image that have not changed.  This parameter controls whether motion detection is enabled (1) or disabled (0).

.. code-block:: ini

    motion_detection = 1


Parallel Processing
#############################

The ALPR processing occurs in parallel, each thread will consume an entire CPU core.  Allocating more CPUs for ALPR processing will linearly increase the number of plate reads per second.  If you wish to maximize the ALPR Processing on your hardware, this number should map the number of CPU cores available on the system.

.. code-block:: ini

    analysis_threads = 2


Back-buffer Processing
#############################

With motion detection enabled, it's possible to buffer moving images and analyze them as CPU becomes available.  For example, if a car moves past the camera over 10 frames, and the CPU can only analyze 3 of those frames during that period, the buffer will allow you to analyze all 10 frames.  After the movement is complete and there is no other activity, the ALPR process will drain the buffer looking for license plates during the period of movement.  Increasing the buffer_size will increase the duration of this period, but will also consume more memory.  The camera_buffer_size unit is number of image frames.

.. code-block:: ini

    camera_buffer_size = 60    

Plate Grouping
#############################

The agent groups similar plate numbers together and sends a JSON post that contains the UUIDs of all of the plate images in the group.  These configuration files control the behavior of the plate grouping algorithm:

  - **plate_groups_span_cameras** -- A single agent may have multiple cameras.  Depending on how the cameras are positioned, they may be viewing the same vehicles or different ones.  If plate_groups_span_cameras is set to enabled, then the grouping algorithm combines plate events that are seen by different cameras.  Otherwise, they are never combined.
  - **plate_groups_min_plates_to_group** -- A plate group must have at least this number of individual plate reads to form a group.
  - **plate_groups_min_confidence** -- The plate read must have at least this confidence in order to be considered for grouping.
  - **plate_groups_max_plates_to_group** -- At some point, if a plate is seen more than this many times, it's wrapped up into a group and sent even if the plate is still in the frame.
  - **plate_groups_time_delta_ms** -- The plate group waits this long for another plate to be identified with a similar license plate.  If a matching plate is seen, this timer is reset.
  - **plate_groups_max_delay_to_send** - Wait at least this long before sending a plate group event.  This is primarily used to ensure that any back-log of processing is complete before finishing the group event.  Depending on the value for camera_buffer_size, you may want to increase or decrease this value.

.. code-block:: ini

    plate_groups_enabled = 1
    plate_groups_span_cameras = 0
    plate_groups_time_delta_ms = 4500
    plate_groups_min_plates_to_group = 2
    plate_groups_min_confidence = 80.0
    plate_groups_max_plates_to_group = 500
    plate_groups_max_delay_to_send = 7500


Image Retrieval
#############################

The Agent may also host a local web server for retreiving images via HTTP GET requests.  By default this web server is turned off, but you may enable it by changing the web_server_enabled parameter to 1.

.. code-block:: ini

    web_server_enabled = 0
    web_server_port = 8355


Vehicle Make/Model Classification
####################################

OpenALPR automatically classifies vehicle make/model, color, and body type for each license plate group that it detects.  If you are not using or do not need this feature, you can disable it to save CPU processing time.  

.. code-block:: ini

    classify_vehicles = 1

Camera Streams
................

The Agent must be configured to connect to one or more camera streams to process license plates.  OpenALPR expects to receive a URL specifying the HTTP or RTSP stream location on the IP camera.  You should be able to find this URL by consulting your camera's user manual, or using a ONVIF-capable discovery tool.  Once you have found the URL, you can test the stream by using a video player such as `VLC <http://www.videolan.org/>`_

To add a camera stream:

1. Create a new unique file in /etc/openalpr/stream.d/ with the extension ".conf".  For example, create a file named "my_new_camera.conf"
2. Add a line to the file with the following format:
   - stream = [Camera HTTP/RTSP stream URL]
3. Restart the OpenALPR agent:


Once restarted, you should see the agent successfully connecting to the camera in the log file (/var/log/alpr.log)

Each camera configuration file may **optionally** be configured with the following settings:
  
  - **prewarp** - A series of numbers that adjusts the rotation/angle of the camera image before processing.  The exact value can be obtained using the OpenALPR configuration utility
  - **detection_mask_image** - Path to a black and white image file that creates a mask over the video stream before processing.  Areas that are black will be ignored, white areas will be scanned for plates.


Restarting the Agent 
......................

After making any configuration changes, the OpenALPR agent must be restarted for those configuration changes to take effect.  

To Restart the OpenALPR Agent:
  - On **Windows**, use the OpenALPR Configuration utility or manually restart the "OpenALPR Agent" Windows Service
  - On **Linux**, run: **sudo systemctl restart openalpr-agent**
  - On **Docker**, restart the container.


.. _web_server:

Web Server
========================

.. include:: web_server.include


Application Integration
========================

.. include:: app_integration.include

.. _video_processing:


Video Processing
=================

The video processing utility processes a video file (e.g., MPG, AVI, MP4, etc.) to find all of the license plates in the video.  The output is a CSV file that lists the license plate information

Installation
--------------

In order to use this utility, you must have a commercial license key.  Contact info@openalpr.com to request an evaluation key.  The license key must be saved in /etc/openalpr/license.conf.

Windows
.........

The Alpr Video utility is included in the latest `OpenALPR Windows Installer <http://deb.openalpr.com/windows/openalpr-latest.exe>`_.

After installing the agent, the video utility is located in the [install_directory]\\bin directory

Linux
.........

First, install the OpenALPR agent following the install procedure in the OpenALPR Agent section.

Run the following command to install the OpenALPR Video utility:

  ```
  sudo apt update && sudo apt install openalpr-video
  ```

After the install is complete, you can execute the alpr_video utility directly on the command line.


Command-Line Usage
---------------------

Launch the alpr_video program with a path to a video file.  Also include an output directory, this is where the csv and SQLite files are saved.  The threads option provides more threads to process the video simmultaneously (this should not exceed the number of CPU cores on the machine).

.. code-block:: bash

    USAGE: 

       ./alpr_video  --output_dir <output_dir> [-c <country_code>] [--config
                     <config_file>] [-n <topN>] [--threads <num_threads>] [-p
                     <pattern code>] [--save_frames] [--debug] [--motion] [--]
                     [--version] [-h] <video_file_path>



    Where: 

       --output_dir <output_dir>
         (required)  Path to the output directory

       -c <country_code>,  --country <country_code>
         Country code to identify (either us for USA or eu for Europe). 
         Default=us

       --config <config_file>
         Path to the openalpr.conf file

       -n <topN>,  --topn <topN>
         Max number of possible plate numbers to return.  Default=10

       --threads <num_threads>
         Number of simmultaneous processing threads. Default=1

       -p <pattern code>,  --pattern <pattern code>
         Attempt to match the plate number against a plate pattern (e.g., md
         for Maryland, ca for California)

       --save_frames
         Save the image frames for recognized license plates.  Default=off

       --debug
         Print diagnostic information to the console.  Default=off

       --motion
         Use motion detection on video file or stream.  Default=on

       --,  --ignore_rest
         Ignores the rest of the labeled arguments following this flag.

       --version
         Displays version information and exits.

       -h,  --help
         Displays usage information and exits.

       <video_file_path>
         (required)  Video file containing license plates


       OpenAlpr Video Processing Utility




Results
---------

The results are made available in CSV files as well as a SQLite database for querying.  The image frames where license plates were found are also optionally saved to a folder on the disk.

The CSV data is exported in two files.  One file shows the individual plate reads, and the other shows the plate groups.  For example:

Plate Results:

.. csv-table:: Plate Results

    id,group_id,country,plate_number,confidence,frame_num,video_time_s,matches_pattern,tracking_hash,x1,y1,x2,y2,x3,y3,x4,y4,img_name,region,region_confidence
    1,2,gb,CV60UWK,78.4882,70,2.84,1,,36,235,176,280,169,314,29,267,,gb,0
    2,2,gb,CV60UWK,75.4025,73,2.96,1,,134,180,269,220,260,251,127,209,,gb,0
    3,2,gb,CV60UWK,83.4606,74,3,1,,167,159,310,199,300,238,159,196,,gb,0
    4,2,gb,CV60UWK,82.3763,75,3.04,1,,198,141,332,180,322,215,189,177,,gb,0

Plate Groups:

.. csv-table:: Plate Groups

    id,country,plate_number,matches_pattern,plate_count,frame_start,frame_end,video_time_start_s,video_time_end_s,best_plate_id,confidence,region,region_confidence
    2,gb,CV60UK,0,6,70,77,2.84,3.12,6,82.6058,gb,0
    1,gb,GP0VZC,0,9,199,211,8,8.48,18,84.4312,gb,0
    4,gb,GR15RYT,1,2,981,994,39.28,39.8,39,82.4912,gb,0