
.. _language_bindings:

.. _commercial_sdk:

Commercial SDK
==================

The OpenALPR commercial SDK exposes APIs for identifying license plates in individual images, as well as videos or feeds of sequential images.  The SDK can run on your hardware and integrate directly with your application.

The APIs are available as C/C++ library with additional bindings in C#, Java, and Python. 

Installation
------------------

Windows
########

The Windows SDK package can be downloaded here:
https://deb.openalpr.com/windows-sdk/openalpr64-sdk-latest.zip

Linux
#######

The Linux SDK is available in OpenALPR's commercial APT repository. The libopenalpr-dev package will install the necessary headers and library packages into your system.  To install, run:

  .. code-block:: bash

    bash <(curl -s https://deb.openalpr.com/install)


Select "install_sdk" from the menu.  



Alpr API
------------------

Overview
##########

The OpenALPR C API recognizes license plates given individual images. In addition to the
installation options above, the Alpr API is available as a pip package through PyPI here:
https://pypi.org/project/openalpr/.

The library instance should be initialized once for each thread.  There 
is a significant initilization time (a few seconds) so you should not destroy 
the instance until you are finished using it.

This instance is not threadsafe by design.  An ALPR instance should be used on a single
thread and not shared across threads.

C++
##########
.. doxygenfile:: alpr.h

C
##########
.. doxygenfile:: alpr_c.h

Samples
##########


C
..................

First, install the OpenALPR library on your target platform. Make sure the software runs by testing it with the alpr command-line executable.  

1. Add "alpr_c.h" as an include file to your project.
2. Include the libopenalpr.dll (Windows) or libopenalpr.so (Unix) file with your binaries.
3. Include all other required shared libraries.
4. Insert the openalpr.conf and runtime_data directory in the same location as the binaries. Alternatively, you can specify the location of the runtime_data in openalpr.conf or directly in the code.
5. Add a commercial license key to the "license.conf" file in the same location as the binaries.

Below is a simple example of using the OpenALPR library with the C library:

.. code-block:: c


    #include <stdio.h>
    #include <stdlib.h>
    #include <alpr_c.h>

    long read_file(const char* file_path, unsigned char** buffer)
    {
        FILE *fileptr;
        long filelen;

        fileptr = fopen(file_path, "rb");     // Open the file in binary mode
        if (!fileptr)
            return 0;
        
        fseek(fileptr, 0, SEEK_END);          // Jump to the end of the file
        filelen = ftell(fileptr);             // Get the current byte offset in the file
        rewind(fileptr);                      // Jump back to the beginning of the file

        *buffer = (unsigned char *)malloc((filelen+1)*sizeof(char)); // Enough memory for file + \0
        fread(*buffer, filelen, 1, fileptr); // Read in the entire file
        fclose(fileptr); // Close the file

        return filelen;
    }

    int main(int argc, char *argv[])
    {
        OPENALPR* alpr_obj;

        if (argc != 2)
        {
            printf("Usage: %s [path to image file]\n", argv[0]);
            return 1;
        }

        const char* file_path = argv[1];

        // Leave the config and runtime directory blank to look for these in the current directory.
        alpr_obj = openalpr_init("us", "", "");

        if (openalpr_is_loaded(alpr_obj))
        {
            // We don't want to restrict the size of the recognition area, so we set this to an extremely large pixel size
            // rather than decode and find the actual image width/height.
            struct AlprCRegionOfInterest roi;
            roi.x = 0;
            roi.y = 0;
            roi.width = 10000;
            roi.height = 10000;

            // Read the image file
            unsigned char* buffer;
            long long length = read_file(file_path, &buffer);

            printf("file size (bytes): %d\n", length);

            if (length > 0)
            {
                char* plate_response = openalpr_recognize_encodedimage(alpr_obj, buffer, length, roi);
                printf("Alpr response:\n%s\n", plate_response);
                openalpr_free_response_string(plate_response);
            }

            free(buffer);


        }

        openalpr_cleanup(alpr_obj);


        return 0;
    }


C++
..................

The C++ commercial SDK is available for Linux. For Windows computers, we recommend that you use the C library.

Add "alpr.h" as an include file to your project.

Below is a simple example of using the OpenALPR library with the C library:


.. code-block:: c++ 


    #include <alpr.h>

    // Initialize the library using United States-style license plates.  
    // You can use other countries/regions as well (for example: "eu", "au", or "kr").
    alpr::Alpr openalpr("us", "/path/to/openalpr.conf");

    // Optionally, you can specify the top N possible plates to return (with confidences). The default is ten.
    openalpr.setTopN(20);

    // Optionally, you can provide the library with a region for pattern matching. This improves accuracy by 
    // comparing the plate text with the regional pattern.
    openalpr.setDefaultRegion("md");

    // Make sure the library loads before continuing.  
    // For example, it could fail if the config/runtime_data is not found.
    if (openalpr.isLoaded() == false)
    {
        std::cerr << "Error loading OpenALPR" << std::endl;
        return 1;
    }

    // Recognize an image file. Alternatively, you could provide the image bytes in-memory.
    alpr::AlprResults results = openalpr.recognize("/path/to/image.jpg");

    // Carefully observe the results. There may be multiple plates in an image, 
    // and each plate returns the top N candidates.
    for (int i = 0; i < results.plates.size(); i++)
    {
      alpr::AlprPlateResult plate = results.plates[i];
      std::cout << "plate" << i << ": " << plate.topNPlates.size() << " results" << std::endl;
      
        for (int k = 0; k < plate.topNPlates.size(); k++)
        {
          alpr::AlprPlate candidate = plate.topNPlates[k];
          std::cout << "    - " << candidate.characters << "\t confidence: " << candidate.overall_confidence;
          std::cout << "\t pattern_match: " << candidate.matches_template << std::endl;
        }
    }


C# and VB.NET
..................

Source code: https://github.com/openalpr/openalpr/tree/master/src/bindings/csharp

.. code-block:: c#

    using AlprNet;

    var alpr = new Alpr("us", "/path/to/openalpr.conf", "/path/to/runtime_data");
    alpr.Initialize();
    if (!alpr.IsLoaded())
    {
        Console.WriteLine("OpenAlpr failed to load!");
        return;
    }

    var frame_lpr_data = alpr.Recognize("C:/path/to/imagefile/infiniti.jpg");
    int i = 0;
    foreach (var result in frame_lpr_data.results)
    {
        Console.WriteLine("Plate {0}: {1} result(s)", ++i, result.candidates.Count);
        Console.WriteLine("  Processing Time: {0} msec(s)", result.processing_time_ms);
        foreach (var plate in result.candidates)
        {
            Console.WriteLine("  - {0}\t Confidence: {1}\tMatches Template: {2}", plate.plate,
                              plate.confidence, plate.matches_template);
        }
    }




Python
..................

Source code: https://github.com/openalpr/openalpr/tree/master/src/bindings/python

.. code-block:: python

    from openalpr import Alpr

    alpr = Alpr("us", "/path/to/openalpr.conf", "/path/to/runtime_data")
    if not alpr.is_loaded():
        print("Error loading OpenALPR")
        sys.exit(1)
        
    alpr.set_top_n(20)
    alpr.set_default_region("md")

    results = alpr.recognize_file("/path/to/image.jpg")

    i = 0
    for plate in results['results']:
        i += 1
        print("Plate #%d" % i)
        print("   %12s %12s" % ("Plate", "Confidence"))
        for candidate in plate['candidates']:
            prefix = "-"
            if candidate['matches_template']:
                prefix = "*"

            print("  %s %12s%12f" % (prefix, candidate['plate'], candidate['confidence']))

    # Call when completely done to release memory
    alpr.unload()



Java
..................

Source code: https://github.com/openalpr/openalpr/tree/master/src/bindings/java

.. code-block:: java

    import com.openalpr.jni.Alpr;
    import com.openalpr.jni.AlprPlate;
    import com.openalpr.jni.AlprPlateResult;
    import com.openalpr.jni.AlprResults;

    Alpr alpr = new Alpr("us", "/path/to/openalpr.conf", "/path/to/runtime_data");

    // Set top N candidates returned to 20.
    alpr.setTopN(20);

    // Set pattern to Maryland.
    alpr.setDefaultRegion("md");

    AlprResults results = alpr.recognize("/path/to/image.jpg");
    System.out.format("  %-15s%-8s\n", "Plate Number", "Confidence");
    for (AlprPlateResult result : results.getPlates())
    {
        for (AlprPlate plate : result.getTopNPlates()) {
            if (plate.isMatchesTemplate())
                System.out.print("  * ");
            else
                System.out.print("  - ");
            System.out.format("%-15s%-8f\n", plate.getCharacters(), plate.getOverallConfidence());
        }
    }

    // Make sure to call this to release memory.
    alpr.unload();


Node.js
..................

A Node.js binding to OpenALPR is available here:
https://www.npmjs.com/package/node-openalpr

The source code is available here:
https://github.com/netPark/node-openalpr





AlprStream API
------------------

Overview
##########

OpenALPR AlprStream API is used for license plate recognition on videos or time-ordered image sequences.

The AlprStream API organizes video streams so that they can be efficiently processed
by OpenALPR.  The library utilizes motion detection to improve performance, and groups
sequential plate results for each vehicle into a single result with the highest possible confidence.  
AlprStream also buffers incoming video and handles cases where video input is faster than the available processing by 
evenly dropping frames from the video buffer.

You may configure AlprStream to connect directly to a video feed (either an IP camera stream URL or a file). 
AlprStream will create a background thread that constantly pulls from the source to keep the video buffer full.
You may also feed video frames directly.  If sending video manually, you should do this on a separate thread.

AlprStream maintains a buffer of frames for processing.  You may share an AlprStream object across threads.
Each thread should maintain its own Alpr object.  Passing the Alpr object to AlprStream using a process_frame()
call performs a recognition and returns individual results.  The AlprStream object keeps track of these results and 
forms plate groups.

Plate Groups can be accessed at any time by popping or peeking from the active list.  Once a plate group is 
fully formed, and we are confident that the vehicle is no longer in the scene, it is available to be popped.
Otherwise, it will remain on the list that you can peek, until enough time has passed.

Vehicle recognition is optionally run on each AlprGroup after it has been popped.  
The vehicle recognition is CPU/GPU intensive.  AlprStream uses a region centered around the license plate
to perform the vehicle recognition.

The AlprStream object should be initialized once for each video stream.  The initialization time is minimal.
The AlprStream instance is threadsafe.

 
C++
##########
.. doxygenfile:: alprstream.h

C
##########
.. doxygenfile:: alprstream_c.h

Samples
##########

C
..................

The following sample uses AlprStream API to recognize frames from a video.  The Video is fed into the library asynchronously, 
and the processing occurs on the main thread.

To use ALPR on multiple CPU cores, you should create multiple threads that call the alprstream_process_frame() function.  
Each thread should have its own Alpr object, but can share the AlprStream object across threads.


.. code-block:: c

    // System imports
    #include <cstdlib>
    #include <vector>
    #include <string>
    #include <string.h>
    #include <sstream>
    #include <iostream>

    // Import OpenALPR alprstream_c (also pulls in alpr_c.h and vehicleclassifier_c.h)
    #include <alprstream_c.h>
    #include <alpr_c.h>

    using namespace std;


    int main(int argc, char** argv) {

        cout << "Initializing" << endl;
        const std::string LICENSEPLATE_COUNTRY = "eu";

        // Video buffer frames controls the number of frames to buffer in memory. 
        const int VIDEO_BUFFER_SIZE = 15;

        // The stream will assume sequential frames.  If there is no motion from frame to frame, then 
        // processing can be skipped for some frames
        const int USE_MOTION_DETECTION = 1;

        // The point in time (ms) to start in the video file
        const int VIDEO_START_MS = 0;

        OPENALPR* alpr = openalpr_init(LICENSEPLATE_COUNTRY.c_str(), "", "");
        ALPRSTREAM* stream = alprstream_init(VIDEO_BUFFER_SIZE, USE_MOTION_DETECTION);

        // AlprStream will spawn a background thread to read the eu-clip.mp4 video file
        // and push to the queue.  Alternatively, you may connect to a stream URL, or 
        // push individual image frames into the alprstream object yourself
        alprstream_connect_video_file(stream, "C:\\Temp\\eu-clip.mp4", VIDEO_START_MS);

        // Process until the video file is done and all remaining frames in the buffer have been processed
        while (true)
        {
            // If the buffer is empty wait for it to replenish
            if (alprstream_get_queue_size(stream) <= 0)
                Sleep(100);


            // AlprStream will now perform recognition on the oldest video frame on the queue
            AlprStreamRecognizedFrameC* response = alprstream_process_frame(stream, alpr);

            cout << "Content: " << response->results_str << endl;

            // Writes the image frame to a temp file on disk for demonstration purposes
            if (response->image_available)
            {
                FILE *file = fopen("c:\\temp\\test.jpg", "wb");
                fwrite(response->jpeg_bytes, sizeof(char), response->jpeg_bytes_size, file);
                fclose(file);
            }

            // Free the memory for each response
            alprstream_free_frame_response(response);

            // Get Group results:
            char* group_result = alprstream_pop_completed_groups_and_recognize_vehicle(stream);
            cout << "Groups: " << group_result << endl;
            alprstream_free_response_string(group_result);

            cout << "Stream queue size: " << alprstream_get_queue_size(stream) << endl;
        }

        cout << "Done" << endl;

        // Cleanup the memory for the Alpr object
        openalpr_cleanup(alpr);

        // Cleanup the memory for the AlprStream object
        alprstream_cleanup(stream);
        return 0;
    }

Python
..................

Download our sample video file (http://download.openalpr.com/bench/720p.mp4) which has several cars with license plates
in a parking lot (or take your own video in .mp4 format). Then, run the following code:

.. code-block:: python

    import sys
    from alprstream import AlprStream
    from openalpr import Alpr

    alpr = Alpr("us", "/path/to/openalpr.conf", "/path/to/runtime_data")
    if not alpr.is_loaded():
        print("Error loading OpenALPR")
        sys.exit(1)
    alpr_stream = AlprStream(frame_queue_size=10, use_motion_detection=True)
    if not alpr_stream.is_loaded():
        print("Error loading AlprStream")
        sys.exit(1)

    alpr_stream.connect_video_file('/path/to/720p.mp4', 0)

    while alpr_stream.video_file_active() or alpr_stream.get_queue_size() > 0:
        single_frame = alpr_stream.process_frame(alpr)
        active_groups = len(alpr_stream.peek_active_groups())
        print("Active groups: {:<3} \tQueue size: {}".format(active_groups, alpr_stream.get_queue_size()))
        groups = alpr_stream.pop_completed_groups()
        for group in groups:
            print("=" * 50)
            print("Group ({}-{}): {} ({:.2f}% confident)".format(
                group['epoch_start'], group['epoch_end'],
                group['best_plate']['plate'], group['best_plate']['confidence']))
            print("=" * 50)
