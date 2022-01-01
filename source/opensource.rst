
********************
Open Source
********************

Compiling
============


Windows
---------

Using Precompiled Binaries
............................

Precompiled binaries are available for Windows in both 32-bit and 64-bit format. You can find the latest binaries on the `GitHub releases page <https://github.com/openalpr/openalpr/releases>`. This is the simplest option for installing OpenALPR on a Windows machine. Included in the package are the Visual Studio 2015 runtime drivers. You must install these first in order to use the OpenALPR software.


Compiling OpenALPR via Build Script
.....................................

`OpenALPR Windows Build Scripts <https://github.com/peters/openalpr-windows>`_


Compiling OpenALPR and Dependencies Manually
............................................

`Video Tutorial <http://youtu.be/ooPln41Q6iM>`_

* Ensure you have a version of Visual Studio that is at least 2008 or above
* Download and install `cmake for windows <http://www.cmake.org/cmake/resources/software.html>`_
* Download `OpenCV for Windows <http://opencv.org/>`_ (3.0.0 at this time)
* Download `Tesseract OCR source code and VS2008 project files <https://code.google.com/p/tesseract-ocr/downloads/list>`_ (3.04 at this time)
* Download `Leptonica vs2008 development package <https://code.google.com/p/leptonica/downloads/list>`_ (Tesseract requirement)
* Download `OpenALPR source code <https://github.com/openalpr/openalpr>`_ from GitHub
* Create a libraries directory and put opencv and Tesseract into them
* Compile OpenCV.  `cd` into the opencv directory and type cmake . to create the VisualStudio project.
* Compile Tesseract.  Tesseract requires that you point it to the leptonica headers and the library binaries before it will compile. Note that tesseract needs to be compiled as a LIBRARY.  Make sure also that your compile mode matches for each of the projects (e.g., Release vs Debug).
* Update the CMakeLists.txt file in the OpenALPR src directory to point to the folders for your Tesseract and OpenCV libraries
* Depending on the versions of the libraries, you may need to tweak the linked libraries in here as well (e.g., liblept168 may be liblept173 at some point in the future).  These correspond to the library files for Leptonica and Tesseract.
* CD into the openalpr src directory and type "cmake ."
* Open the Visual Studio solution and compile.
* If all goes well, there should be an "alpr" executable.  In order to execute it, you will need a number of DLLs from OpenCV.  simply finding them in the OpenCV directories and copying them over to your executable should do the trick.



Linux
---------

OpenALPR compiles on many flavors of Linux. However, the software is officially supported on Ubuntu 14.04 64-bit.

Using Precompiled Binaries
............................

Precompiled binaries are available for Ubuntu 14.04 64-bit OS. This is the simplest option for installing OpenALPR onto a Linux machine. The precompiled binaries are managed via APT, so upgrades will automatically be installed when you use the *apt-get update & apt-get upgrade* command. To install the precompiled binaries, run the following commands on your Ubuntu machine:

.. code-block:: bash 

    wget -O - https://deb.openalpr.com/openalpr.gpg.key | sudo apt-key add -
    echo "deb https://deb.openalpr.com/master/ trusty main" | sudo tee /etc/apt/sources.list.d/openalpr.list
    sudo apt-get update
    sudo apt-get install openalpr openalpr-daemon openalpr-utils libopenalpr-dev

Test the library

.. code-block:: bash 

    # Test US plates
    wget http://plates.openalpr.com/ea7the.jpg
    alpr -c us ea7the.jpg

    # Test European plates
    wget http://plates.openalpr.com/h786poj.jpg
    alpr -c eu h786poj.jpg

Compiling OpenALPR
.....................

.. code-block:: bash 

    # Install prerequisites
    sudo apt-get install libopencv-dev libtesseract-dev git cmake build-essential libleptonica-dev
    sudo apt-get install liblog4cplus-dev libcurl3-dev

    # If using the daemon, install beanstalkd
    sudo apt-get install beanstalkd

    # Clone the latest code from GitHub
    git clone https://github.com/openalpr/openalpr.git

    # Setup the build directory
    cd openalpr/src
    mkdir build
    cd build

    # setup the compile environment
    cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr -DCMAKE_INSTALL_SYSCONFDIR:PATH=/etc ..

    # compile the library
    make

    # Install the binaries/libraries to your local system (prefix is /usr)
    sudo make install

    # Test the library
    wget http://plates.openalpr.com/h786poj.jpg -O lp.jpg
    alpr lp.jpg


Compile OpenALPR and All Dependencies
.......................................

1. Make sure that dependencies and required tools are installed:

  * sudo apt-get install libpng12-dev libjpeg62-dev libtiff4-dev zlib1g-dev
  * sudo apt-get install build-essential
  * sudo apt-get install autoconf automake libtool
  * sudo apt-get install git-core
  * sudo apt-get install cmake

2. Install OpenCV (tutorial):

  * http://docs.opencv.org/2.4/doc/tutorials/introduction/linux_install/linux_install.html

3. Download and install Leptonica and Tesseract-ocr:

  * tesseract-ocr requires leptonica and at least one language package.  
  * http://www.leptonica.org/source/leptonica-1.70.tar.gz
  * https://tesseract-ocr.googlecode.com/files/tesseract-ocr-3.02.02.tar.gz
  * https://tesseract-ocr.googlecode.com/files/tesseract-ocr-3.02.eng.tar.gz
  * move the downloaded tarballs to some directory. I will assume that they are located at /usr/local/src/openalpr/. 
 
4. Unpack the tarballs:

  * tar xf /usr/local/src/openalpr/tesseract-ocr-3.02.02.tar.gz 
  * tar xf /usr/local/src/openalpr/tesseract-ocr-3.02.02.eng.tar.gz
  * tar xf /usr/local/src/openalpr/leptonica-1.70.tar.gz
 
5. Compile Leptonica:

  * cd  /usr/local/src/openalpr/leptonica-1.70/
  * ./configure --prefix=/usr/local
  * make
  * make install
 
6. Compile Tesseract:

  * cd /usr/local/src/openalpr/tesseract-ocr/
  * ./autogen.sh
  * ./configure
  * make
  * sudo make install
  * sudo ldconfig

7. Clone the OpenALPR repo to the /usr/local/src/openalpr/ directory:

  * cd /usr/local/src/openalpr/
  * git clone https://github.com/openalpr/openalpr.git

8. Update CMakeLists.txt compile OpenALPR:

  * cd /usr/local/src/openalpr/openalpr/
  * gedit CMakeLists.txt &
  * SET(OpenCV_DIR "/usr/local/lib")
  * SET(Tesseract_DIR "/usr/local/src/openalpr/tesseract-ocr")
  * cmake ./
  * make

Note: For Tesseract 3.04 the source files can be downloaded from the main svn branch or https://drive.google.com/folderview?id=0B7l10Bj_LprhQnpSRkpGMGV2eE0&usp=sharing#list. 


Mac OS X
-----------

The following are the instructions for compiling on OS X, tested on OS X 10.9.5 (Mavericks).

Using Homebrew
.................

  * brew tap homebrew/science
  * brew install openalpr

Compiling OpenALPR Manually
................................

.. code-block:: bash 

    # Clone the latest code from GitHub
    git clone https://github.com/openalpr/openalpr.git

    # Setup the build directory
    cd openalpr/src
    mkdir build
    cd build

    # setup the compile environment
    cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr -DCMAKE_INSTALL_SYSCONFDIR:PATH=/etc ..

    # compile the library
    make

    # Install the binaries/libraries to your local system (prefix is /usr)
    sudo make install

    # Test the library
    wget http://easy-clan.com/ski/pics/license_plate.JPG -O lp.jpg
    alpr lp.jpg

Mobile (iOS and Android)
----------------------------

The OpenALPR library compiles on Android and iOS.  Example reference apps are available:

  - `Android <https://github.com/sujaybhowmick/OpenAlprDroidApp>`_
  - `iOS <https://github.com/twelve17/openalpr-ios>`_

Docker
-----------

OpenALPR supports containerization inside Docker. It uses Ubuntu 14.04 as a base image and installs all the software using pre-compiled binaries. Download the OpenALPR DockerFile and run the following commands to build it:

.. code-block:: bash

    # Build docker image
    docker build -t openalpr https://github.com/openalpr/openalpr.git

    # Download test image
    wget http://plates.openalpr.com/h786poj.jpg

    # Run alpr on image
    docker run -it --rm -v $(pwd):/data:ro openalpr -c eu h786poj.jpg


.. _alpr_command_line:

Command line utility
========================


The OpenALPR Command Line Interface (CLI) utility is a great feature for quickly testing ALPR against images, videos, or webcams. It is not recommended for sophisticated integration because each time the CLI utility loads, it takes a number of seconds to initialize all the OpenALPR recognition data.

Usage
------

::

       alpr  [-c <country_code>] [--config <config_file>] [-n <topN>] [--seek
             <integer_ms>] [-p <pattern code>] [--motion] [--clock] [-d] [-j]
             [--] [--version] [-h] <> ...


    Where: 

       -c <country_code>,  --country <country_code>
         Country code to identify (either us for USA or eu for Europe). 
         Default=us

       --config <config_file>
         Path to the openalpr.conf file

       -n <topN>,  --topn <topN>
         Max number of possible plate numbers to return.  Default=10

       --seek <integer_ms>
         Seek to the specied millisecond in a video file. Default=0

       -p <pattern code>,  --pattern <pattern code>
         Attempt to match the plate number against a plate pattern (e.g., md
         for Maryland, ca for California)

       --motion
         Use motion detection on video file or stream.  Default=off

       --clock
         Measure/print the total time to process image and all plates. 
         Default=off

       -d,  --detect_region
         Attempt to detect the region of the plate image.  [Experimental] 
         Default=off

       -j,  --json
         Output recognition results in JSON format.  Default=off

       --,  --ignore_rest
         Ignores the rest of the labeled arguments following this flag.

       --version
         Displays version information and exits.

       -h,  --help
         Displays usage information and exits.

       <>  (accepted multiple times)
         (required)  Image containing license plates


Examples
-----------

This  command  will  attempt to recognize number plates in the /source/image.jpg image using the European-style recognition data. The config
file is not provided on the CLI, so it will use the value in the environment variable 'OPENALPR_CONFIG_FILE', if provided, or the default
location.

::

    $ alpr -c eu /source/image.jpg

This command will attempt to recognize number plates in the /source/image.png image using the default USA-style recognition data. The config
file is not provided on the CLI, so it will read the configuration data from /tmp/openalpr.conf.

::

    $ alpr --config /tmp/openalpr.conf /source/image.png

This command will attempt to recognize number plates in all jpeg images in the current directory image using the USA-style recognition data.

::

    $ alpr -c us *.jpg

This command reads data from an input video (/source/video.mp4) and outputs recognition data as JSON.

::

    $ alpr -j /source/video.mp4

This command processes a list of image files provided in /source/imagefilelist.txt and writes JSON results to /out/recognitionresults.txt.

::

    $ alpr -j stdin < /source/imagefilelist.txt > /out/recognitionresults.txt

This command processes video from your webcam. You can also use /dev/video0, /dev/video1, etc., if you have multiple webcams.

::

    $ alpr webcam



Open Source Agent (alprd)
==========================


The OpenALPR daemon will allow you to monitor a camera stream for license plate numbers in the background. Alprd runs as a daemon process on Linux. The plate numbers can be streamed to another server (via HTTP posts) or consumed programmatically via a beanstalkd queue.

Architecture
--------------

Alprd will operate as follows:
  1. The image stream will constantly be pulled from the IP camera via MJPEG over HTTP.
  2. Alprd will process the stream as quickly as possible while looking for plate images. The daemon will automatically skip frames to stay in sync with clock time.
  3. When one or more plates are detected, the information will be written to a local beanstalkd queue (tube name "alprd") as JSON data.
  4. Optionally, alprd will also save the image to a configurable location as a jpeg.
  5. Optionally, alprd will also run a separate process that will drain the beanstalkd queue and upload data to a remote HTTP server via POST.

Alprd can be used in two modes:
  1. Recognition results are streamed to an HTTP server
  2. Recognition results can be read from the beanstalkd queue

::

    +------------------+                     +-------------+         
    |                  |  MJPEG       POST   |             |         
    |  Network Camera  | <---+      +------> | HTTP Server |         
    |                  |     |      |        |             |         
    +------------------+     |      |        +-------------+         
                             |      |                                
                             |      |                                
                             |      |                                
                     +-------+------+                                
                     |              |                                
                     | alprd server |                                
                     |              |                                
                     +---------+----+------------+                   
                               |                 |                   
                               | Beastalkd queue |                   
                               |                 |                   
                               +-----------------+                   


The diagram above shows alprd used to stream data to another HTTP server. Alprd will be configured with a remote HTTP address. As plates are identified, the server will send the JSON data to the remote HTTP server. The beanstalkd queue and the alprd process will be colocated on the same server.

::

    +------------------+                                         
    |                  |  MJPEG                                  
    |  Network Camera  | <---+                                   
    |                  |     |                                   
    +------------------+     |                                   
                             |                       +----------+
                             |                       |Processing|
                             |                       +----+-----+
                     +-------+------+                     |      
                     |              |                     |      
                     | alprd server |                     |      
                     |              |                     |      
                     +---------+----+------------+        |      
                               |                 |        |      
                               | Beastalkd queue | <------+      
                               |                 |               
                               +-----------------+               


The diagram above shows alprd used without the HTTP server. In this case, a beanstalkd consumer can be used to drain the results from the beanstalkd queue. The beanstalkd tube name will be "alprd." Beanstalkd consumers can be written in any language and can be colocated on the alprd server or located elsewhere.


Configuration
-------------

.. code-block:: ini

    [daemon]

    ; Declare each stream on a separate line
    ; each unique stream should be defined as stream = [url]
    
    stream = http://10.1.2.3/camera1/stream.mjpeg
    stream = http://10.1.2.5/camera2/stream.mjpeg
    
    site_id = headquarters-usa 
    
    store_plates = 1
    store_plates_location = /var/www/html/plates/
    
    ; upload address is the destination to POST to
    upload_data = 0
    upload_address = http://localhost:9000/alpr/push/

At least one "stream" must be defined in alprd. This will simply be the URL for the mjpeg stream. You may use multiple streams on one server; each stream will spawn a separate process that will attempt to use a full CPU core.

The site ID will be stored along with the JSON plate results. This will be especially useful if you have multiple servers and need to keep track of where the results come from. Additionally, each result will contain a camera ID (numbered 1 to "n") based on the order of your "stream" statements in the alprd.conf file.


Results
---------
The following is an example of the JSON results. These results will initially be stored in the beanstalkd queue, then optionally sent in an HTTP post.

.. code-block:: json

    {
      "uuid": "f11e0acc-6aaf-4817-9299-9e6773043b8e",
      "camera_id": 1,
      "site_id": "headquarters",
      "img_width": 640,
      "img_height": 480,
      "epoch_time": 1402161050,
      "processing_time_ms": 138.669163,
      "results": [
        {
          "plate": "S11FRE",
          "confidence": 77.130661,
          "matches_template": 0,
          "region": "",
          "region_confidence": 0,
          "coordinates": [
            {
              "x": 218,
              "y": 342
            },
            {
              "x": 407,
              "y": 325
            },
            {
              "x": 407,
              "y": 413
            },
            {
              "x": 218,
              "y": 431
            }
          ],
          "candidates": [
            {
              "plate": "S11FRE",
              "confidence": 77.130661,
              "matches_template": 0
            },
            {
              "plate": "S11ERE",
              "confidence": 75.496307,
              "matches_template": 0
            },
            {
              "plate": "S11RE",
              "confidence": 75.440361,
              "matches_template": 0
            },
            {
              "plate": "S11CRE",
              "confidence": 75.340179,
              "matches_template": 0
            },
            {
              "plate": "S11FHE",
              "confidence": 75.240974,
              "matches_template": 0
            },
            {
              "plate": "S11EHE",
              "confidence": 73.606621,
              "matches_template": 0
            },
            {
              "plate": "S11HE",
              "confidence": 73.550682,
              "matches_template": 0
            },
            {
              "plate": "S11CHE",
              "confidence": 73.450493,
              "matches_template": 0
            },
            {
              "plate": "S11FBE",
              "confidence": 71.782944,
              "matches_template": 0
            },
            {
              "plate": "S11FE",
              "confidence": 71.762756,
              "matches_template": 0
            }
          ]
        },
        {
          "plate": "EJLESSIE",
          "epoch_time": 1402158050,
          "confidence": 78.167984,
          "matches_template": 0,
          "region": "",
          "region_confidence": 0,
          "processing_time_ms": 51.650604,
          "coordinates": [
            {
              "x": 226,
              "y": 369
            },
            {
              "x": 348,
              "y": 348
            },
            {
              "x": 355,
              "y": 406
            },
            {
              "x": 231,
              "y": 429
            }
          ],
          "candidates": [
            {
              "plate": "EJLESSIE",
              "confidence": 78.167984,
              "matches_template": 0
            },
            {
              "plate": "EDLESSIE",
              "confidence": 77.61319,
              "matches_template": 0
            }
          ]
        }
      ]
    }


Calibration
=============


Calibrating your camera will improve detection accuracy when vehicle plates are captured at a steep angle. For example, the plate below was captured at a 40+ degree horizontal angle and would normally not be recognized as a license plate.  


.. image:: images/configuration_calibration_before.jpg
    :scale: 100%
    :alt: Calibration before adjustments

Camera calibration will help when the camera will be operating from a fixed position and all plates will generally be seen at the same angle. This feature is best for short-angle cameras or cameras capturing at a close distance.


Use the "openalpr-utils-calibrate" tool to calibrate your camera. To begin, the utility will need a single example image. This image should be taken from the camera you wish to calibrate and should have a license plate that represents the typical distance/angle of the plate captured on this camera.

    ./openalpr-utils-calibrate camera_image.jpg

Sliders at the top of the screen will control the skew. Adjust the sliders until the plate can be seen straight on (with no angle). Left-click and drag to draw a box (with the correct aspect ratio) to test if the plate is the correct size.

You can also right-click and drag to move the image within the frame. Because this process will distort the original image, the frame will be clipped. If frames are unlikely to be seen in certain areas (e.g., on the ceiling), you may want to adjust the plate image to ensure that those areas will be cropped.

.. image:: images/configuration_calibration_tool.jpg
    :scale: 100%
    :alt: Calibration utility

When you're satisified with the parameters you've chosen, press the "o" key. This will produce a line of configuration in the terminal that you will be able to copy directly into openalpr.conf as the "prewarp" config. Before the camera is used, its calibration settings will be applied against each image for plate recognition.

To make sure the new calibration improves accuracy, test the new settings on a few images from this camera.

    ./alpr camera_image.jpg

.. image:: images/configuration_calibration_after.jpg
    :scale: 100%
    :alt: Calibration after adjustments

The license plate will be correctly detected, and you will be able to view the calibrated image results by enabling the "prewarp" debug option in the openalpr.conf file. Test the accuracy on many different images from the camera before accepting the configuration. Other calibration angles could produce superior results.

Pattern Matching
===================

The pattern matching feature will run the topN results against a regular expression matcher to find results that match common license plate patterns. The regex patterns are customizable and can be found in runtime_data/postprocess/``*``.patterns.

For example, using a pattern against this Czechoslovakian plate resulted in only one possible match (the correct one).  

.. image:: images/configuration_patternmatch.jpg
    :scale: 100%
    :alt: Czechoslovakian number plate


The cz patterns are:
 - cz    #@#####
 - cz    #@@####


Notice the pattern matches 4S50233:

::

    [mhill@mhill-linux tmp]$ alpr -c eu -p cz cz_4s50233.jpg -n 40
    plate0: 40 results
        - 4S5O233     confidence: 90.947      pattern_match: 0
        - 4S5O23      confidence: 87.8683     pattern_match: 0
        - 4S5O23      confidence: 85.1644     pattern_match: 0
        - 4S5O23S     confidence: 84.5445     pattern_match: 0
        - 4S5O23B     confidence: 83.7395     pattern_match: 0
        - 4S5O2S3     confidence: 83.3698     pattern_match: 0
        - 4S5O23G     confidence: 83.1375     pattern_match: 0
        - 4S50233     confidence: 83.0457     pattern_match: 1
        - 4S5O2B3     confidence: 82.5635     pattern_match: 0
        - 4S5O2       confidence: 82.0857     pattern_match: 0
        - 4S5O2G3     confidence: 81.5684     pattern_match: 0
        - 4S5O2J3     confidence: 81.0409     pattern_match: 0
        - 4S5O2S      confidence: 80.2911     pattern_match: 0
        ... more results that do not match ...

You will be able to utilize this from the library code by calling "setDefaultRegion(string region)" with the name of the pattern you wish to use:


Configuration
=================

The OpenALPR library will be configured with the openalpr.conf file. On Linux, this is typically located in /etc/openalpr/openalpr.conf. On Windows, it is usually in the same directory as the binary. Many of the configuration options in this file are documented with comments.



Training OCR
===============

Training the OpenALPR OCR is a quick way to improve the accuracy for a particular country. To do this, you will need:

  1. Around 200 clear images of your country's license plates
  2. 16 hours of free time

This code repository `<http://github.com/openalpr/train-ocr>`_ provides code and data that can be used to train custom license plate fonts in support of the OpenALPR library.

OpenALPR uses the Tesseract OCR library. Many of the tedious aspects of OCR training have been automated via a Python script. However, the input data still needs to be in a specific format to satisfy Tesseract.

For more information about training using Tesseract OCR, please read this tutorial: https://code.google.com/p/tesseract-ocr/wiki/TrainingTesseract3

To get started, first clone the repository and get familiar with the input files. In the "eu/input" folder, there are a number of tif files and box files. Each "font" will have at least one tif and box file. A country's license plate may have many fonts; a different name would simply be used for each one.

The naming convention is:
l[country_code].[fontname].exp[pagenumber].box

For example, the European German license plate font would look like:
leu.germany.exp0.box

Open a tif file. Notice that these are a series of similar-looking letters and numbers. The best way to generate these is from actual license plate images. OpenALPR has several utilities to help generate these input files. The first step is to find many pictures of your license plates. Make sure to separate them by font. Sometimes, even within a single region, the license plate fonts will vary (e.g., between old and new plates, between digital and stamped plates, or between vehicle and bicycle plates). To achieve the highest accuracy, make sure each unique font is a different file.

Adding a new Country
--------------------
If you plan to train the OCR for a completely new country, you will first need to configure the dimensions of the plate and characters. Add a new file in runtime_data/config/ with your country's two-digit code. You can copy and paste a section from another country (e.g., us or eu).  

You should tweak the following values:

  - plate_width_mm = [width of full plate in mm]
  - plate_height_mm = [height of full plate in mm]
  - char_width_mm = [width of a single character in mm]
  - char_height_mm = [height of a single character in mm]
  - char_whitespace_top_mm = [whitespace between the character and the top of the plate in mm]
  - char_whitespace_bot_mm = [whitespace between the character and the bottom of the plate in mm]
  - template_max_width_px = [maximum width of the plate before processing.  Should be proportional to the plate dimensions]
  - template_max_height_px = [maximum height of the plate before processing.  Should be proportional to the plate dimensions]
  - min_plate_size_width_px = [Minimum size of a plate region to consider it valid.]
  - min_plate_size_height_px = [Minimum size of a plate region to consider it valid.]
  - ocr_language = [name of the OCR language -- typically just the letter l followed by your country code]

Understanding Your Country's Plates
------------------------------------

The first thing you need to know is how many fonts your country's license plates have.  In the US, for example, many states use very different fonts for their plates.  Some countries only use one font.  Here is an example of New York and West Virginia,.  Notice how different the "6" character is in both plates:

.. image:: images/training_ocr_plateny.png
    :scale: 100%
    :alt: west virginia license plate
.. image:: images/training_ocr_platewv.png
    :scale: 100%
    :alt: new york license plate

Each font needs to be trained separately. You do not want to combine characters across fonts; this will greatly decrease accuracy. When each font is trained, they can be combined into one dataset for your entire country.

Creating the Character Tiles
----------------------------
When you're ready to start training, you'll need to create a library of character tiles. Each tile is a small image file that contains the black-and-white character and is named after the character. Here are a few character tile examples:


.. image:: images/training_ocr_char1.png
    :scale: 100%
    :alt: character tile 1

0-0-az2012.png

.. image:: images/training_ocr_char3.png
    :scale: 100%
    :alt: character tile 3

c-1-az2012.png

.. image:: images/training_ocr_char4.png
    :scale: 100%
    :alt: character tile 4

d-9-az2012.jpg

.. image:: images/training_ocr_char5.png
    :scale: 100%
    :alt: character tile 5

d-9-2-az2012.jpg

You will want many of these character tiles for each character and font. The character tiles will all be slightly different; this is necessary so the OCR training can understand how to detect characters. In the above examples, notice that the "D" characters have pixels located in different places, but they're clearly the same character.

Producing Tiles
----------------
There are two good ways to produce character tiles:

  1. Use actual images from license plates.
  2. Use a TTF font that looks like the license plate font.

Producing Tiles From Actual Plates
------------------------------------

Gather a large library of license plate images (at least 100), making sure each image is at least 250px wide. The aspect ratio should match your configured width/height for your license plates, and these images should be cropped around the plates. (The imageclipper program [separate repo] is helpful for quickly cropping large numbers of images.) Save the images as png files.

Each file should be prefaced with a two-character identifier for the font/region. For example, for Maryland plates, we would name the file: **md**\ plate1.png.

Create an empty output directory.

To start classifying characters, use the classifychars utility program included in OpenALPR.

Execute the command:
  classifychars [country] [input image directory] [empty output directory]

A GUI will open and analyze each license plate image in your input folder. The following are the steps to classify each plate:
    1. Press the "Enter" key and type the letter or number for each position that you wish to classify. Pressing "Space" will skip the character.
    2. Use the arrow keys and press "Space" to select how you wish to extract characters. The box you select will be highlighted in blue. For each plate, there may be good characters and bad characters; pick the best characters because significant imperfections may confuse the OCR.
    3. Press the "S" key to save each character as a separate file in your out folder.
    4. Press the "N" key to move to the next plate. Repeat this process until you've classified all the plates.

Producing Tiles From a TTF Font
-------------------------------
You can use a TTF font to produce tiles. However, to make a robust OCR detector, you must realistically distort the characters.

The process is as follows:

    1. Figure out all characters that could possibly be in a license plate.
    2. Create a Word document with all of these characters. Make sure there is plenty of spacing between lines and characters.
    3. Copy and paste all of these characters to a text file, leaving no spaces or line breaks.
    4. Print the Word document.
    5. Take a few pictures (five would be sufficient) of the Word document with a digital camera. Vary the angle/rotation very slightly (one to two degrees) between each picture.
    6. Save the pictures to a folder.
    7. Run the openalpr-utils-binarizefontsheet program to produce tiles from each of the images. Provide the program with the text file from step 3 as well as each image file.


Building a Tesseract Training Sheet
-----------------------------------

Once you've classified all the characters, it may be a good idea to scan through the directory to make sure the classifications match the images. Each image filename should be prefaced with the character that it represents. After this, you will need to create a training sheet.

The "openalpr-utils-prepcharsfortraining" utility program in OpenALPR will create the Tesseract training sheet for you. Execute the following command:
openalpr-utils-prepcharsfortraining [output directory from above]

The output will be:
  - combined.box
  - combined.tif

Rename these files (e.g., leu.germany.expo0.box) to match the naming convention used by Tesseract (explained above).

You should create a training sheet for each unique license plate font that you wish to train.

Finish OCR Training
---------------------

Lastly, you'll use the box/tif files created above to train your country's license plate OCR. Create a new directory using your country code, then create an input directory within that code. Copy all the box/tif files created in the previous steps into this directory.

Execute the "train.py" file. Type in your country code.

If all went well, you should have a new file named l[countrycode].traineddata. Copy this file into your runtime_directory (runtime_data/ocr/tessdata/), and it will be ready for OpenALPR to use.

Tesseract may report issues; most commonly it will complain that it could not line up the boxes on the provided image. If you receive many warnings, re-run the openalpr-utils-prepcharsfortraining utility and provide values for --tile_width and --tile_height. Using different values will change how Tesseract sees the image and will potentially improve results.

Training the Detector
========================

The detector will find the general location of a license plate in an image. A single detector can support many different plate styles, as long as they generally have the same aspect ratio. For example, in the USA, license plates are 12 inches by 6 inches (an aspect ratio of 2:1).

To train a license plate detector, you will need:

  1. 3000+ clear images of license plates
  2. 40-60 hours of free time

`This repository <http://github.com/openalpr/train-detector>`_  contains scripts that will help train a license plate detector for a particular region.  Your trained region detector can then be used in OpenALPR.

The license plate region detector uses the Local Binary Pattern (LBP) algorithm. To train the detector, you will need many positive and negative images. This repository already contains a collection of negative images; you will need to add your own positive images.

To get started, you will first need many cropped plate images containing positive license plate matches. Please see the "eu" positive image folder in this repository to understand the types of plate images required. The imageclipper program will be helpful for creating these cropped images.

After you've collected many (hundreds to thousands) of positive plate images, the next step is to train the detector. You must first configure the training script to use the correct dimensions.

Edit the prep.py script and change the WIDTH, HEIGHT, and COUNTRY variables to match the country that you are training. The width and height should be proportional to the plate size (slightly larger is fine). A total pixel area of around 650 seems to work best. Also, adjust the path to your OpenCV libraries if necessary.

When you are ready to start training, enter the following commands:

  - rm ./out/``*``    (Clear the "out" folder in case it contains data from previous runs.)
  - ./prep.py neg
  - ./prep.py pos
  - ./prep.py train
  - Copy the output from the above command to the command line. Adjust the numStages to a smaller value (usually 12 stages works well, but it will depend on your input images). You may also need to adjust the numPos value to a smaller number to complete the training.


Copy the out/cascade.xml file to your OpenALPR runtime directory (runtime_data/region/[countrycode].xml). You should now be able to use the region for plate detection.


Developers Guide
=================

Accuracy can also be improved if you modify the recognition code itself. The OpenALPR library is binary-compatible with the commercial software. Any improvements/modifications you make can be swapped in if you replace the openalpr.dll/libopenalpr.so with your modified version. The information below describes the various stages involved in recognizing license plates.

OpenALPR Design
----------------

OpenALPR operates as a pipeline. The input is an image, various processing occurs in stages, and the output is the possible plate numbers in the image.

The pipeline stages occur in the following order:

=======================  ===================================== ==============================================================================================
  Pipeline Phase                      C++ class                      Description                 
=======================  ===================================== ==============================================================================================
 Detection                regiondetector.cpp                    Finds potential license plate regions 
 Binarization             binarizewolf.cpp                      Converts the plate region image into black and white 
 Char Analysis            characteranalysis.cpp                 Finds character-sized "blobs" in the plate region  
 Plate Edges              platelines.cpp and platecorners.cpp   Finds the edges/shape of the license plate 
 Deskew                   licenseplatecandidate.cpp             Transforms the perspective to a straight-on view based on the ideal license plate size. 
 Character Segmentation   charactersegmenter.cpp                Isolates and cleans up the characters so that they can be processed individually 
 OCR                      ocr.cpp                               Analyzes each character image and provides multiple possible letters/confidences
 Post Processing          postprocess.cpp                       Creates a top n list of plate possibilities based on OCR confidences.  
                                                                Also performs a Regex match against region templates if requested. 
=======================  ===================================== ==============================================================================================

Detection
---------
The detection phase happens one time for each input image.  It uses the LBP algorithm (generally used for face detection) to find possible license plate regions (x,y, width, height).  Each of these regions is sent to the later pipeline phases for further processing.

The detection phase is usually the most processing-intensive phase.  It can be GPU accelerated to improve performance.

Binarization
------------
This phase (and all subsequent phases) will occur multiple times — once for each possible license plate region.

The binarization phase will create multiple binary images for each plate region. Multiple binary images will be used to provide the best possible chance of finding all the characters. For example, a single binarized image may miss characters if the image is too dark or too light. Binarization uses the Wolf-Jolien method as well as the Sauovola method with various parameters. Each binary image will be processed in subsequent phases.  

Character Analysis
------------------
Character analysis will attempt to find character-sized regions in the plate region. It will do this by first finding all connected blobs in the license plate region. Then it will look for blobs roughly the width and height of a license plate character and with tops/bottoms that are in a straight line with other blobs of similar width/height.

This analysis will be done multiple times in the region. First small characters will be detected, then larger characters will be gradually hunted.

If nothing is found in the region, it will be thrown out, and no further processing will follow. If potential characters are found, the character region will be saved and further processing will take place.

Plate Edges
-----------
In this phase, the edges of the license plate will be searched for. Keep in mind that the detection phase will only be responsible for identifying a possible region in which a license plate may exist. Often a region slightly larger or smaller than the actual plate will be detected, but the precise top/bottom/left/right edges of the license plate will be sought.

The first step is to find all of hough lines for the license plate region. At platelines.cpp, the plate image will be processed and a list of horizontal and vertical lines will be computed.

Platecorners will use this list, as well as the character height (computed in Character Analysis), to find the likeliest plate line edges. It will use a number of configurable weights to determine which edge is most logical. It will attempt to use a default edge (based on the ideal width/height of the plate) to see if that makes a good match.

Deskew
------
Given the plate edges, the deskew stage will re-map the plate region to a standard size and orientation. Ideally, this will produce a correctly oriented plate image without rotation or skew.

Character Segmentation
----------------------
The character segmentation phase will try to isolate all the characters that make up the plate image. It will use a vertical histogram to find gaps in the plate characters. This phase will also clean up the character boxes by removing small, disconnected speckles and disqualifying character regions that are too short. It will also try to remove "edge" regions so that the edge of the license plate isn't inappropriately classified as a "1" or an "I."

OCR
---
The OCR phase will analyze each character independently. For each character image, it will compute all possible characters and their confidences.

Post Processing
---------------
Given a list of all possible OCR characters and confidences, post processing will determine the best possible plate letter combinations. It will be organized as a top "n" list. Post processing will disqualify all characters below a particular threshold. It will also have a "soft" threshold; characters below this threshold will still be added to the possible list. However, a possible blank character will be added because the low-confidence character may not really be part of the plate.

The post processing will also handle region validation, if requested. For example, if you tell OpenALPR that a certain plate is a "Missouri" plate, it will try to match the results against a template that matches the Missouri format (e.g., [char][char][number]-[char][number][char]). For example, if the top three list is:
  - CFOCIG
  - CF0CIG
  - CF0C1G

The third entry will match the template, while the other two will not. Thus, post processing will signal that the third entry is the best match.
