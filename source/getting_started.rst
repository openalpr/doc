*********************
Watchman Quick Start
*********************

Create an Account
=================

Watchman software handles recognizing license plate data from a surveillance camera stream.
Visit https://cloud.openalpr.com/account/register to sign up for a new account.
You will be emailed an activation link shortly.

Hardware Requirements
=====================

Most modern PCs from the past five years should be able to run the Watchman Agent without issues.


If you would like to do a more thorough assessment, start by determining the resolution and *total* frames per second (fps) your hardware will need to handle.
For instance, to support four cameras with 15, 15, 30, and 20 fps, respectively, you will need hardware capable of 80 fps.
If you are unsure what fps value to set your camera(s) to, we suggest the following rules of thumb based on the speed of traffic being monitored

  - **Low Speed** (under 25 mph): 5-10 fps
  - **Medium Speed** (25-45 mph): 10-15 fps
  - **High Speed** (over 45 mph): 15-30 fps

Next, find the specifications for your computer's processor (see guides for `Windows <https://www.windowscentral.com/how-check-your-computer-full-specifications-windows-10>`_ or `Linux <https://www.binarytides.com/linux-cpu-information/>`_).
Compare your total fps value and resolution with our `list of benchmarked processors <https://docs.google.com/spreadsheets/d/1FNwEuJAgZ1LyM2GGd7VRJo85x99IFbecveO8rmFH1to/edit?usp=sharing>`_.
Note, there are different tabs depending on your operating system (we have found Windows performance to be 16-22% lower than Ubuntu).
For example, most modern Intel i7 CPUs can handle around 60 fps of 720p resolution video with Ubuntu as the operating system.
If your processor is not listed, you can either

  1. Lookup its `PassMark score <https://www.cpubenchmark.net/cpu_list.php>`_ and interpolate between the existing benchmarks
  2. Use our code and instructions on `Github <https://github.com/openalpr/speed_benchmark>`_ to run the same benchmark script on your hardware

Lastly, consider using a CPU with a higher total fps rating if you will run other processes besides OpenALPR on the hardware.

Install Watchman Agent
======================

On the hardware you have previously sized, follow the appropriate steps based on your operating system to install the Watchman Agent.

.. _openalpr_windows_agent_install:

Windows 10
----------

  1. Download the `OpenALPR Windows Installer <https://deb.openalpr.com/windows-agent/openalpr-agent-latest.exe>`_
  2. Right click the .exe file, run as administrator, and follow the install wizard

Ubuntu Linux
-------------

Installation on Linux requires basic knowledge of using the terminal.
If you do *not* already have Linux installed as an operating system, please complete these steps before continuing

  1. Download the `Ubuntu 18.04 64-bit install DVD image <http://releases.ubuntu.com/18.04/>`_ and burn it to a DVD
  2. Follow this `installation guide <http://www.ubuntu.com/download/desktop/install-ubuntu-desktop>`_

If you already have Linux installed

  1. Open a new terminal
  2. Ensure you have curl installed
  3. Run our installer script and choose "install_agent"
  4. Select yes/no when prompted to connect the Agent to your cloud account

  .. code-block:: bash

      sudo apt update && sudo apt install -y curl
      bash <(curl -s https://deb.openalpr.com/install)  # select "install_agent"

Docker
------

  1. Open a new terminal
  2. Pull the latest OpenALPR Docker image

  .. code-block:: bash

      docker pull openalpr/commercial-agent

  3. Enable the license for the agent

  .. code-block:: bash

      docker run -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/agent openalpr-licenseregister

  4. Configure it to point to a Watchman web server (default is https://cloud.openalpr.com)

  .. code-block:: bash

      docker run -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/agent alprlink-register

  5. Start the container

  .. code-block:: bash

      docker run --restart always -d --cap-add SYS_NICE -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/commercial-agent

.. _commercial_config_options:

Axis Cameras
------------

Install the Watchman Agent as an app on Axis cameras which sends video to the cloud for processing.
The following prerequisites are required:

  - A professional account with cloud.openalpr.com (on-premise webservers are *not* supported)
  - A supported Axis camera
  - An SD Card with at least 2GB storage space
  - Internet Connectivity (minimum 2Mbps upload speed)
  - Accurate date/time. We recommend configuring NTP (See Setup > Date & Time)
  - DNS Configuration (See Setup > Advanced TCP/IP Settings)
  - The latest Axis firmware (minimum is 5.60.1.1)

After checking the prequisites, follow these steps for installation:

  1. Download the latest `Watchman Axis camera Agent <https://deb.openalpr.com/axis/OpenALPR_Cloud_latest_mipsisa32r2el.eap>`_
  2. Login to your Axis Camera
  3. Click Setup in the top right corner
  4. Click Applications
  5. Click the Choose File button, select OpenALPR_Cloud_latest_mipsisa32r2el.eap (downloaded above), and then click the Upload Package button
  6. Click OpenALPR Cloud and then Start (you will be prompted for your cloud.openalpr.com credentials)

As vehicles move past the camera, the video will be sent to OpenALPR for archiving and processing.
You can view video recorded in the past 60 days using the "Video" link on the left.
As license plates are detected, they will appear on our cloud account dashboard.

.. _data_destinations:

Set Data Destination
====================

After completing installation, you can can choose between four data destination options depending on how you wish to use the Watchman Agent

  .. image:: images/agent-windows-config.png
      :scale: 100%
      :alt: Windows Agent Configuration

  1. **Watchman Cloud:** Send data to https://cloud.openalpr.com (a Watchman webserver hosted by OpenALPR)
  2. **Watchman On-Premises:** Send data to an on-premise Watchman webserver (hosted by you on a Linux machine)
  3. **Other HTTP Web Server:** Send data to your own HTTP endpoint
  4. **Local Queue:** Store data locally in beanstalkd and drain the queue programmatically from your application

If you intend to use the Agent for surveillance purposes, you will most likely select option 1 or 2.
Options 3 and 4 are useful if you are an developer integrating OpenALPR data into your own application.
More details can be found in :ref:`Application Integration <application_integration>`.

For Watchman Agents installed on hardware with a GUI

  1. Launch the Configure OpenALPR program
  2. From the top menu, go to Configure > Data Destination

For hardware without a GUI (Linux only)

  1. Registering with a Watchman Webserver (cloud or on-premise) using your email and password

  .. code-block:: bash

    sudo openalpr-licenseregister
    sudo alprlink-register -w https://[ip_address_of_web_server]


  2. Sending to a generic HTTP URL

  .. code-block:: bash

    echo "upload_address = [ip_address_of_http_endpoint]" | sudo tee -a /etc/openalpr/alprd.conf

Connect to Cameras
==================

There are four approaches for connecting cameras to the OpenALPR Watchman Agent software.
Regardless of which method you use, it is a good idea to first play the video stream in an external media player like `VLC <https://www.videolan.org/vlc/>`_ (click Media > Open Network Stream).
This ensures the URL is correctly formatted and there are no firewall/network issues.
If your stream will not play in VLC, the OpenALPR Agent will not be able to connect to it either.

**1) Auto Discovery**

The Agent GUI's has an auto discovery feature which should work for camera's supporting the ONVIF communication protol.
Most major brands of IP cameras will fall under this category.

  1. Launch the Configure OpenALPR program
  2. Toggle the cameras tab on the left if it is not already active
  3. Click Add > Discover Devices
  4. Click the IP address matching your camera and you will be provided with a list of available substreams (different resolutions and/or frame rates)
  5. Choose a substream, test, and save under a unique camera name

**2) Configure the agent from the webserver**

For this option, make sure your Agent is registered with the cloud or an on-premise Watchman Webserver (see :ref:`data_destinations`)

  1. Login to the webserver
  2. Navigate to Configuration > Agents
  3. Click the blue Configure button for the Agent you want to add the camera to
  4. Scroll to the Camera Stream Config section
  5. Select your manufacturer from the drop down list or choose Generic URL
  6. Fill out the remaining fields, test the stream, and save the camera with a unique name

**3) Manually add URL in the Agent GUI**

  1. Determine the stream URL for your camera.
     The format varies by manufacturer, but typically has the form rtsp://username:password@ip_address:port_number/some/unique/extension (may also start with http:// instead).
     The username and password portion may not be required for all cameras but typically comes with a simple factory default such as root:1234 or admin:password.
     For RTSP streams, the default port number is 554 while HTTP uses 80.
     If you do not know the /some/unique/extension portion, please review our templates for different camera manufacturers in approach 2.5, browse your camera's configuration interface if available, or contact the camera manufacturer directly.
  2. Follow approach 1, except instead of selecting Discover Devices chose Stream URL Manual

**4) Manually edit configuration files**

  1. Follow approach 3.1 to determine your camera's stream URL
  2. Open a blank text file in the Agent's stream folder (located at /etc/openalpr/stream.d on Linux or C:\\OpenALPR\\Agent\\etc\\openalpr\\stream.d on Windows)
  3. Add a line to the file with the following format: stream = [Camera HTTP/RTSP stream URL]
  4. Save with the file with a unique name and .conf extension (i.e. my_new_camera.conf)
  5. Restart the Agent

    * *GUI:* Services tab > Agent > Stop > Start
    * *Linux Terminal:* :code:`sudo systemctl restart openalpr-daemon`
