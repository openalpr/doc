
.. _on_premises:

********************
OpenALPR Watchman
********************

Watchman software runs on your hardware/application and enables automatic license plate recognition from any IP camera.
You don't need to spend $10,000 on a proprietary "LPR camera," nor do you need to use complicated and costly video management software.
The software can be used for general surveillance purposes or as an engine to add LPR capabilities to other applications.

The Watchman Agent recognizes license plates and the Watchman webserver (either cloud or on-premise) displays searchable results.
Other options for retreiving results from the Agent are discussed in the Application Integration section.

Sign-up for Watchman today on https://cloud.openalpr.com/


Watchman Agent
========================

System Requirements
---------------------

The Watchman Agent scales to make use of as many CPU resources as you can provide. For example, OpenALPR can utilize up to 100 percent of the processing power on both low-power devices (such as a Raspberry Pi) and high-end devices (such as an Intel 32 CPU-core Xeon server). High-end devices are capable of processing significantly more video frames per second.

Memory and disk requirements are relatively constant. Once OpenALPR initializes, the process uses roughly 500MB per analysis thread (CPU core), and that memory usage will not increase. The agent records JPEG images to a disk as plates are recognized. However, Disk IO is usually not a bottleneck.

The minimum system requirements for Watchman Agents are a function of:

  - The speed/angle of vehicles with respect to the camera (i.e., how long a legible license plate is in-frame)
  - The resolution of the input video
  - The number of camera streams analyzed by the agent
  - Other CPU load
  - The volume of vehicles (i.e., whether vehicles are seen constantly or sporadically)

OpenALPR maintains a `list of benchmarked processors <https://docs.google.com/spreadsheets/d/1FNwEuJAgZ1LyM2GGd7VRJo85x99IFbecveO8rmFH1to/edit?usp=sharing>`_ (both cloud and physical) at VGA, 720p, 1080p, and 4k resolution.
Each Watchman release version has its own tab in the spreadsheet.
If your CPU or GPU model is on the list, write down the "System Total FPS" numbers for the resolution(s) you are interested in.
If you cannot find your CPU/GPU model in our list, the source code and instructions for running the speed benchmarks are available on `Github <https://github.com/openalpr/speed_benchmark>`_.

Please note that all benchmarks in our spreadsheet were conducted with Ubuntu as the operating system. If you plan to install on Windows, you should decrease the FPS values by 16-22%.

To estimate the number of cameras for a given system total FPS value, use the following per-camera rules of thumb

  - **Low Speed** (under 25 mph): 5-10 fps
  - **Medium Speed** (25-45 mph): 10-15 fps
  - **High Speed** (over 45 mph): 15-30 fps

Installation
---------------

Follow the appropriate directions for your operating system below.
After completing installation, you can can choose between four data destination options Depending on how you wish to use the Watchman Agent

  .. image:: images/agent-windows-config.png
      :scale: 100%
      :alt: Windows Agent Configuration


  1. **OpenALPR Cloud** — Data is sent to https://cloud.openalpr.com (requires an active OpenALPR Cloud Account).
  2. **Watchman Webserver** — You have installed an on-premise webserver on a Linux machine and wish to point the Agent data to that server.
  3. **Generic HTTP Url** — You wish to integrate OpenALPR with your own application. You can point the data to your own HTTP endpoint.
  4. **Local Queue** — You want the OpenALPR data to be stored on a local beanstalkd queue. Your application will programmatically drain the queue to process results.

If you intend to use the agent for surveillance purposes, you will most likely select "OpenALPR Cloud" or "Watchman Webserver."
Options 3 and 4 are useful if you are an developer integrating OpenALPR data into your own application.

.. _openalpr_windows_agent_install:

Windows
.........

  - Download the `OpenALPR Windows Installer <https://deb.openalpr.com/windows-agent/openalpr-agent-latest.exe>`_
  - Right click the .exe file, run as administrator, and follow the install wizard
  - Start the Configure OpenALPR program and go to Configure > Data Destination

Linux
.........

  1. Download the `Ubuntu 18.04 64-bit install DVD image <http://releases.ubuntu.com/18.04/>`_ and burn it to a DVD.
  2. Follow this `installation guide <http://www.ubuntu.com/download/desktop/install-ubuntu-desktop>`_ to install Ubuntu 18.04 64-bit.
  3. Ensure you have curl installed by running the following command from the terminal:
  
  .. code-block:: bash

    sudo apt-get update && sudo apt-get install -y curl

  4. Run the following command from the terminal:

  .. code-block:: bash

    bash <(curl -s https://deb.openalpr.com/install)


  5. Choose "install_agent" and select yes/no when prompted to connect the Agent to your cloud account.
  6. If you are using a GUI, you can configure the agent using the alprdconfig utility by running the following command:

  .. code-block:: bash

    sudo alprdconfig

  7. If your server does not have a GUI and you wish to connect your software to a Watchman webserver, run the following commands:
     - Type the command alprlink-register -w https://[ip_address_of_web_server] .
     - Type in the e-mail address and password for your OpenALPR Cloud account, or the one you used when installing the on-premise webserver.

Docker
............

  1. Open a new terminal
  2. Pull the latest OpenALPR Docker image

  .. code-block:: bash

      docker pull openalpr/agent

  3. Enable the license for the agent

  .. code-block:: bash

      docker run -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/agent openalpr-licenseregister

  4. Configure it to point to a Watchman web server (default is https://cloud.openalpr.com)

  .. code-block:: bash

      docker run -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/agent alprlink-register

  5. Start the container

  .. code-block:: bash

      docker run --restart always -d --cap-add SYS_NICE -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/agent

Jetson
......

The Watchman Agent can be installed on Nvidia Jetson devices such as the Nano, TX-1, and TX-2.  These are excellent options for an edge device due to the power-efficient, embedded GPU.
Native binaries are available for a JetPack versions 4.3.0.  The recommended package for other JetPack versions is the Jetson Docker image

  - **JetPack >= 4.2.1:** pull the Docker image for `openalr/agent:2.8.101-jetson <https://hub.docker.com/r/openalpr/agent/tags>`_ and follow Docker instructions above
  - **JetPack = 4.3.0:** follow Linux install instructions above

Note that both native and Docker installs will automatically include the additional GPU packages.
Therefore, you will not need to reference the GPU acceleration section of the documentation.
When installing via Docker, make sure you have `Nvidia Docker Runtime <https://github.com/NVIDIA/nvidia-docker>`_ available so that the container can access the host GPU.
You will also need to modify any Docker run commands

  .. code-block:: bash

    docker run --runtime nvidia [other_flags] [image_tag] [command]

Axis Cameras
............

Install the Watchman Agent as an app on Axis cameras which sends video to the cloud for processing.
The following prerequisites are required:

  - A professional account with cloud.openalpr.com (on-premise webservers are *not* supported)
  - A supported Axis camera
  - An SD Card with at least 2GB storage space
  - Internet Connectivity (minimum 2Mbps upload speed)
  - Accurate date/time. We recommend configuring NTP (See Setup | Date & Time)
  - DNS Configuration (See Setup | Advanced TCP/IP Settings)
  - The latest Axis firmware (minimum is 5.60.1.1)

After checking the prequisites, follow these steps for installation:

  - Download the latest `Watchman Axis camera Agent <https://deb.openalpr.com/axis/OpenALPR_Cloud_latest_mipsisa32r2el.eap>`_
  - Login to your Axis Camera
  - Click Setup in the top right corner
  - Click Applications
  - Click the Choose File button and select the OpenALPR_Cloud_latest_mipsisa32r2el.eap file (downloaded above), then click the Upload Package button
  - Click OpenALPR Cloud and then Start (you will be prompted for your cloud.openalpr.com credentials)

As vehicles move past the camera, the video will be sent to OpenALPR for archiving and processing.
You can view video recorded in the past 60 days using the "Video" link on the left.
As license plates are detected, they will appear on our cloud account dashboard.


Licensing
---------------

Each Watchman Agent connects to the OpenALPR Cloud service (https://cloud.openalpr.com/) to activate and maintain its license key.  Once the agent is registered, you may send Watchman data to any destination you choose, such as the Watchman Cloud Service, your own web server, or a local queue for application integration.  

After installation, the Watchman service will ask you to register with https://cloud.openalpr.com/.  If you don't already have an account, you can sign-up.

To register your agent using the Graphical User Interface, open the Configuration Utility and choose Configure -> Licensing

To register your agent using the command line run::

  On Linux
  sudo openalpr-licenseregister

  On Windows:
  C:\OpenALPR\Agent\bin\openalpr-licenseregister.exe


NVIDIA GPU Acceleration
-------------------------

Watchman Agent performance can also be drastically accelerated by Nvidia GPU hardware.  OpenALPR maintains binaries for CUDA 11.0 using CuDNN 8 on 64-bit Ubuntu Linux 20.04.  On Ubuntu 18.04 the software runs on CUDA 10.0 using CuDNN 7.  The OpenALPR software has been compiled for CUDA hardware versions 5.2 5.3 6.0 6.1 6.2 7.0 7.2 7.5 8.0.  You can verify your CUDA version here: https://developer.nvidia.com/cuda-gpus

Ubuntu 18.04
................

First setup the Nvidia repos::

  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.0.130-1_amd64.deb -O /tmp/cuda-repo-ubuntu1804_10.0.130-1_amd64.deb
  sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
  sudo dpkg -i /tmp/cuda-repo-ubuntu1804_10.0.130-1_amd64.deb

If you have not already installed Nvidia drivers, you must do this first::

  sudo apt-get update
  sudo apt-get install -y nvidia-driver-410 
  sudo reboot

Now you may install the OpenALPR GPU acceleration package::

  bash <(curl https://deb.openalpr.com/install)

  Select OpenALPR software and the install_nvidia option 

Ubuntu 20.04
...............

First install the Nvidia GPU drivers via the official Nvidia repository::

  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
  sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
  sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/7fa2af80.pub
  sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/ /"
  sudo apt-get update
  sudo apt-get -y install cuda-11-0

After installation, you will likely need to reboot.  Make sure that the drivers are installed using the following command::

  nvidia-smi

Next install the OpenALPR Nvidia acceleration.  Choose "install_nvidia" from the menu::

  bash <(curl https://deb.openalpr.com/install)

The following properties will be set automatically in /etc/openalpr/openalpr.conf::

  hardware_acceleration = 1
  gpu_id = 0
  gpu_batch_size = 5

The batch size controls how many images are simultaneously processed by the GPU.  The default value is usually the best balance of performance and GPU memory usage.  

OpenALPR also maintains special builds for Jetson TX-1/TX-2 hardware on Jetpack v3.1.  To install, follow the standard Linux install instructions.  The defaults are configured such that the GPU will automatically be installed, enabled, and configured with a default batch size of 5.

Windows 10
...............

Prerequisites::

  Windows 10 64-bit
  Nvidia GPU driver >= 417.22

Install:

  - Install the latest version of the Watchman Agent or SDK 
  - Install https://deb.openalpr.com/windows-nvidia/vc_redist_msvc2015.x64.exe
  - Download and extract the following file to the agent bin directory (default C:\\OpenALPR\\Agent\\bin) or SDK directory https://deb.openalpr.com/windows-nvidia/openalpr_nvidiawindowslibs_2.8.101.zip

If running the agent, edit the Watchman Agent config file (c:\\openalpr\\agent\\etc\\openalpr\\alprd.conf) and add the following line::

  hardware_acceleration = 1

Restart the Watchman Agent Service


.. _commercial_config_options:


Configuration
---------------

Agent Properties
..................


You can configure each Watchman Agent manually (by editing the agent configuration files) or through the OpenALPR web server. This section discusses manual configuration. Configuring the agent through the OpenALPR web server is described in the web server section of the documentation.

The primary configuration file for the Watchman Agent is located in the **/etc/openalpr/alprd.conf** file. For your reference, you can find a default configuration file containing every default setting for the agent in **/usr/share/openalpr/config/alprd.defaults.conf**. Any value you change in alprd.conf will take precedence over the default configuration.

The following configuration options can be modified if you edit the alprd.conf file. After adding or changing values in the configuration file, you must restart the Watchman Agent process.


Image Storage Configuration
#############################

The commercial daemon maintains a maximum disk quota and automatically cleans up after itself.  Images stored to the disk are removed on a FIFO-basis.  These parameters control the location, image quality, and maximum size of local image storage. 

.. code-block:: ini

    store_plates = 1
    store_plates_jpeg_quality = 85
    store_plates_location = /var/lib/openalpr/plateimages/
    store_plates_maxsize_mb = 8000


Upload Configuration
#############################

You may enable or disable uploading. When the agent is running, all plate results will first be placed onto a local beanstalkd queue. If the upload_data parameter is set to "1" for enabled, the agent will pull results from the queue and send them to the configured "upload_address" endpoint as an HTTP POST. The upload_threads parameter will specifies the number of simultaneous data uploads. Generally, the number of threads only needs to be increased if the web server is slow to respond or network latency is exceptionally high.

.. code-block:: ini

    upload_data = 1
    upload_address = https://cloud.openalpr.com/push/
    upload_threads = 2


Company ID (required)
#############################

The company_id value is sent in the JSON payload for all results. If you connect to an OpenALPR web server, this value is configured for you automatically. However, if you configure the agent manually, the value is not used, but it is still required. Any alphanumeric string will suffice.

.. code-block:: ini

    company_id = abc-123


Motion Detection
#############################

Motion detection improves efficiency and draws more plate reads from available processors. The ALPR processing only analyzes frames with movement, ignoring areas of the image that have not changed. This parameter controls whether motion detection is enabled (1) or disabled (0).

.. code-block:: ini

    motion_detection = 1


Parallel Processing
#############################

The ALPR processing occurs in parallel; each thread consumes an entire CPU core. Allocating more CPUs for ALPR processing will linearly increase the number of plate reads per second. If you wish to maximize the ALPR processing on your hardware, this number should map the number of CPU cores available on the system.

.. code-block:: ini

    analysis_threads = 2


Back-Buffer Processing
#############################

With motion detection enabled, you are able to buffer moving images and analyze them as CPU becomes available. For example, if a car moves past the camera over 10 frames but the CPU can only analyze 3 of those frames in that period, the buffer allows you to analyze all 10 frames. When activity ceases, the ALPR process drains the buffer, looking for license plates during the period of movement. Increasing the buffer_size increases the duration of this period but will also consume more memory. The camera_buffer_size unit is the number of image frames.

.. code-block:: ini

    camera_buffer_size = 60    

Plate Grouping
#############################

The agent groups similar plate numbers together and sends a JSON post containing the UUIDs of every plate image in the group. These configuration files control the behavior of the plate grouping algorithm:

  - **plate_groups_span_cameras** -- A single agent may have multiple cameras.  Depending on how the cameras are positioned, they may view the same vehicles or different ones. If plate_groups_span_cameras is set to "enabled," the grouping algorithm combines plate events that are seen by different cameras. Otherwise, they are never combined.
  - **plate_groups_min_plates_to_group** -- A plate group must have at least this number of individual plate reads to form a group.
  - **plate_groups_min_confidence** -- The plate read must have at least this confidence to be considered for grouping.
  - **plate_groups_max_plates_to_group** -- At some point, if a plate is seen more than this number of times, it is wrapped into a group and sent even if the plate is still in the frame.
  - **plate_groups_time_delta_ms** -- The plate group waits this long for another plate to be identified with a similar license plate. If a matching plate is seen, this timer is reset.
  - **plate_groups_max_delay_to_send** -- Wait at least this long before sending a plate group event.  This function is primarily intended to ensure that any back-log of processing is complete before the group event is finished. Depending on the value for camera_buffer_size, you may want to increase or decrease this value.

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

The agent may also host a local web server for retreiving images via HTTP GET requests. This web server is turned off, but you may enable it by changing the web_server_enabled parameter to 1.

.. code-block:: ini

    web_server_enabled = 0
    web_server_port = 8355


Vehicle Make/Model Classification
####################################

OpenALPR automatically classifies vehicle make/model, color, and body type for each license plate group it detects.  If you are not using or do not need this feature, you can disable it to save CPU processing time.  

.. code-block:: ini

    classify_vehicles = 1

Camera Streams
................

The agent must be configured to connect to one or more camera streams to process license plates. OpenALPR expects to receive a URL specifying the HTTP or RTSP stream location on the IP camera. You should be able to find this URL by consulting your camera's user manual, or with the help of an ONVIF-capable discovery tool. Once you have found the URL, you can test the stream with a video player such as `VLC <http://www.videolan.org/>`_

To add a camera stream:

1. Create a new unique file in /etc/openalpr/stream.d/ with the extension ".conf".  For example, create a file named "my_new_camera.conf".
2. Add a line to the file with the following format:
   - stream = [Camera HTTP/RTSP stream URL]
3. Restart the Watchman Agent.


Once restarted, you should see the agent successfully connecting to the camera in the log file (/var/log/alpr.log)

Each camera configuration file may **optionally** be configured with the following settings:
  
  - **prewarp** - A series of numbers that adjusts the rotation/angle of the camera image before processing. The exact value can be obtained through the OpenALPR configuration utility
  - **detection_mask_image** - The path to a black and white image file that creates a mask over the video stream before processing. Black areas are ignored, white areas are scanned for plates.


Custom GStreamer Pipeline
####################################

In some cases, you may require more control over the process that grabs video from your device and makes it available to OpenALPR.  The Watchman Agent and AlprStream SDK both allow you to configure a custom GStreamer Pipeline which can handle pulling video from your camera.  GStreamer is open source and supports user plug-ins which allow you to write your own C code in case you need to utilize 3rd party device drivers to pull the data.

To get started, you should first familiarize yourself with GStreamer pipelines.  The following are a few examples of pulling video from various sources.  gst-launch-1.0 is a command line program that will execute the pipeline and display the output.

**Webcam**

    gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw,framerate=30/1,width=1280,height=720 ! decodebin ! videoconvert ! video/x-raw,format=BGR ! videoconvert ! autovideosink

**Video File**

    gst-launch-1.0 filesrc location=/tmp/video.mp4 ! decodebin ! videoconvert ! video/x-raw,format=BGR ! videoconvert ! autovideosink

**RTSP/H264 Camera**

    gst-launch-1.0 rtspsrc location=rtsp://192.168.0.100/video user-id=testuser user-pw=test drop-on-latency=1 latency=1000 ! queue max-size-buffers=0 max-size-time=0 max-size-bytes=0 min-threshold-time=10 ! rtph264depay ! h264parse ! decodebin ! videoconvert ! video/x-raw,format=BGR ! videoconvert ! autovideosink


Once you have experimented with GStreamer to display video, you can now integrate it with OpenALPR.  Our software will see the video exactly as the gst-launch-1.0 command displays it.  If using the agent, you can add your custom gstreamer format as follows:

    1. Edit /etc/openalpr/stream.d/[cameraname].conf
    2. Add gstreamer_format = [your custom gstreamer pipeline]
    3. Remove the "autovideosink" and replace it with "appsink name=sink max-buffers=5"
    4. Make sure you do not include the "gst-launch-1.0" command at the beginning.

If you are using the AlprStream SDK, you will apply the pipeline to the API function "connect_video_stream_url" by passing it a string for "gstreamer_pipeline_format"



Restarting the Agent 
......................

The Watchman Agent must be restarted for any configuration changes to take effect.  

To restart the Watchman Agent:
  - On **Windows**, use the OpenALPR configuration utility or manually restart the "Watchman Agent" Windows service.
  - On **Linux**, run: **sudo systemctl restart openalpr-daemon**.
  - On **Docker**, restart the container.


.. _web_server:

Watchman Webserver
=================================

.. include:: watchman_web.include


Application Integration
========================

.. include:: app_integration.include

.. _web_services_api:


REST API 
========================

The `Web Services API <api/?api=cloudstream>`_ can be used to programmatically query any Watchman webserver (both cloud and on-premises).
