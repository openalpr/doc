********************
Cloud Stream
********************


OpenALPR Cloud Stream enables automatic license plate recognition from any IP camera. You don't need to spend $10,000 on a proprietary "LPR camera," nor do you need to use complicated and costly video management software. The OpenALPR Agent recognizes license plates and the Cloud Stream portal displays results, which can be viewed and searched from anywhere with a web browser.

As mentioned, OpenALPR works with any IP camera, but if you own an Axis camera and sign up for a professional account, we offer a specially-designed app (Agent) that makes installation and management extremely simple.

For professional account users, the OpenALPR Axis camera Agent uploads videos directly from the camera to the OpenALPR Cloud Stream portal, where they are processed and retained for 60 days. No server or VMS software is required. OpenALPR handles all the heavy-duty processing to extract license plates from video streams, and it provides results on an easy-to-read dashboard. Again, there's no additional hardware or software to install or manage — a huge cost and time savings. Simply install the OpenALPR app on the Axis camera and create a Cloud Stream account; we'll take care of the rest. Software updates are applied automatically, so you’ll always receive the latest accuracy and features. A required 4 GB microSD card in the camera will buffer videos if your Internet connection fails or is too slow to keep up. The Agent's intelligent bandwidth controls allow users to manage bandwidth utilization, and all video clips are stored in the cloud, where they can be played back via your browser.

For basic account users, the Windows/Linux OpenALPR Agent works with any IP camera that supports H.264 and MJPEG streams. All video is processed on your own PC/server, and the plate information is uploaded to the OpenALPR Cloud Stream portal, where it is viewable for 30 days. This hybrid cloud service is cost-effective because the license plate processing is performed on your own hardware and plate images are stored locally. Users define how much CPU and storage to utilize for the OpenALPR Agent, which is managed through the Cloud Stream portal with a web browser. Minimal bandwidth is required because only the metadata is uploaded. Full resolution plate images reside locally and can be viewed on demand when the user requires visual verification.

For free account users, data retention is limited to two days, and users are required to login to the Cloud Stream portal to view results. Just like in a basic account, the OpenALPR Agent works with any IP camera and will run on 64-bit Windows or Ubuntu Linux 18.04 operating systems. If you require alert notifications, enhanced search, and longer data retention, you can upgrade to a basic account (or professional account if you are using an Axis camera) at any time by entering your credit card information.


Sign up here: https://cloud.openalpr.com/

Installation
==============


Axis Agent
---------------

Install the OpenALPR Agent as an app on Axis cameras. The agent sends video to the cloud for processing. Requires professional account.

Prerequisites
...............

OpenALPR Agent requires the following to be installed/configured on your Axis Camera:

  - A supported Axis camera
  - An SD Card with at least 2GB storage space
  - Internet Connectivity (minimum 2Mbps upload speed)
  - Accurate date/time. We recommend configuring NTP (See Setup | Date & Time)
  - DNS Configuration (See Setup | Advanced TCP/IP Settings)
  - The latest Axis firmware (minimum is 5.60.1.1)

Installation
..............

  - Download the latest OpenALPR Axis Agent.
  - Login to your Axis Camera.
  - Click Setup in the top right corner.
  - Click Applications.
  - Click the Choose File button and select the OpenALPR_Cloud_latest_mipsisa32r2el.eap file (downloaded above), then click the Upload Package button.
  - Click OpenALPR Cloud and then Start.

Configuration
...............

  - Login to your Axis Camera.
  - Click Setup in the top right corner.
  - Click Applications -> OpenALPR Cloud from the menu on the left pane.
  - Click Configure OpenALPR.
  - Type the e-mail address and password that you used to login to the OpenALPR Cloud portal. Click Connect.
  - If registration was successful, click "OK."

Congratulations — you're all set up! As vehicles move past the camera, the video will be sent to OpenALPR for archiving and processing. You can view video recorded in the past 60 days using the "Video" link on the left. As license plates are detected, they will appear on the dashboard.

Linux
---------------

Install the OpenALPR Agent on your 64-bit Ubuntu Linux 18.04 OS. The OpenALPR Agent will operate as a background daemon.

  - Download the Ubuntu 18.04 64-bit install DVD image and burn it to a DVD.
  - Follow this installation guide to install Ubuntu 18.04 64-bit.
  - Run the following command from the terminal: 
	- bash <(curl -s https://deb.openalpr.com/install)
  - To connect the agent to the OpenALPR Cloud, choose “install agent.”
  - When prompted, type the e-mail address and password that you used to login to the cloud site.

Now that the agent is connected, you can add video streams on the `Agents Configuration Screen <https://cloud.openalpr.com/configure/agents/>`_


Docker
---------------

Run the OpenALPR Agent as a container on any OS supported by Docker.

  - Run the command **docker pull openalpr/commercial-agent** to pull the latest OpenALPR Docker Commercial Agent.
  - Run the following command to register the agent with the service:
    - docker run -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/commercial-agent alprlink-register
  - Start the docker container with the following command:
    - docker run -P -v openalpr-vol1-config:/etc/openalpr/ -v openalpr-vol1-images:/var/lib/openalpr/ -it openalpr/commercial-agent

Now that the agent is connected, you can add video streams on the `Agents Configuration Screen <https://cloud.openalpr.com/configure/agents/>`_


Windows
---------------

Install the OpenALPR Agent on your 64-bit Windows OS. The OpenALPR Agent will operate as a background Windows service.

  - Download the OpenALPR Windows Installer.
  - Install the program onto your PC.
  - Start the Configure OpenALPR program after installation is complete.
  - Select OpenALPR Cloud.
  - Type in your username and password and click Connect.

Now that the agent is connected, you can add video streams on the `Agents Configuration Screen <https://cloud.openalpr.com/configure/agents/>`_
