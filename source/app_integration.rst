
You can integrate OpenALPR with your application in two ways:

  - Use the :ref:`OpenALPR SDK <commercial_sdk>` to compile OpenALPR code libraries into your application and process individual image frames or video streams.  
  - Run the OpenALPR Agent to directly pull video feeds and send the plate results and images to your application.





Integrating With the Agent
------------------------------

The OpenALPR agent runs as either a Windows service (on Windows) or a daemon (on Linux). The process runs constantly in the background, watching the configured video streams for license plates. When a license plate is found, it immediately places JSON data onto a local beanstalkd queue for uploading or processing.

Decide whether your application should receive data from OpenALPR (sent via HTTP POST) or pull data from a queue on demand. Both approaches are equally valid; your choice simply depends on the most appropriate integration method based on your application architecture.


Receiving HTTP POSTs
-------------------------

The OpenALPR agent can be configured to send HTTP POSTs to your HTTP server. To enable this option, set the following parameters in /etc/openalpr/alprd.conf:

.. code-block:: ini

    upload_data = 1
    upload_address = [your HTTP server endpoint]
    websockets_enabled = 0
    web_server_enabled = 1

In addition, you must add a valid on-premises license to /etc/openalpr/license.conf

Once updated, restart the OpenALPR agent service to allow the settings to take effect. As plates are recognized, the JSON data described in this section will be sent to your HTTP server as an HTTP POST.


Pulling From the Queue
-------------------------

OpenALPR maintains a local Beanstalkd queue. All JSON results are placed onto this queue. Your application can grab and process the latest plate results from this queue .  

Beanstalkd maintains client libraries in many popular programming languages. For a complete list, visit: https://github.com/kr/beanstalkd/wiki/Client-Libraries

To configure OpenALPR to make the results available via the local queue, set the following parameters in /etc/openalpr/alprd.conf:

.. code-block:: ini

    upload_data = 0
    websockets_enabled = 0
    web_server_enabled = 1

In addition, you must add a valid on-premises license to /etc/openalpr/license.conf

Once updated, restart the OpenALPR agent service to allow the settings to take effect.


Below are sample scripts in Python and PHP that pull results from the local Beanstalkd queue:

.. code-block:: python

    #!/usr/bin/python

    import beanstalkc
    import json
    from pprint import pprint

    beanstalk = beanstalkc.Connection(host='localhost', port=11300)

    TUBE_NAME='alprd'

    # For diagnostics, print out a list of all the tubes available in Beanstalk.
    print beanstalk.tubes()

    # For diagnostics, print the number of items on the current alprd queue.
    try:
        pprint(beanstalk.stats_tube(TUBE_NAME)) 
    except beanstalkc.CommandFailed:
        print "Tube doesn't exist"

    # Watch the "alprd" tube; this is where the plate data is.
    beanstalk.watch(TUBE_NAME)

    # Loop forever
    while True:

        # Wait for a second to get a job. If there is a job, process it and delete it from the queue.  
        # If not, return to sleep.
        job = beanstalk.reserve(timeout=1.0)

        if job is None:
            print "No plates available right now, waiting..."

        else:
            print "Found a plate!"
            plates_info = json.loads(job.body)

            # Print all the info about this plate to the console.
            pprint(plates_info)

            # Do something with this data (e.g., match a list, open a gate, etc.).
            if 'data_type' not in plates_info:
                print "This shouldn't be here... all OpenALPR data should have a data_type"
            elif plates_info['data_type'] == 'alpr_results':
                print "This is a plate result"
            elif plates_info['data_type'] == 'alpr_group':
                print "This is a group result"
            elif plates_info['data_type'] == 'heartbeat':
                print "This is a heartbeat"

            # Delete the job from the queue when it is processed.
            job.delete()

.. code-block:: php

    <?php
        // Load PheanStalk PHP Library.
        require('vendor/autoload.php');
        use Pheanstalk\Pheanstalk;

        //Create PheanStalk instance
        $pheanstalk = Pheanstalk::create('127.0.0.1');

        //Get available tubes
        echo "----Available Tubes----\r\n\r\n";
        var_dump($pheanstalk->listTubes());
        echo "\r\n\r\n----End of Tube List----\r\n\r\n";

        //Watch the alprd-alt tube
        $pheanstalk->watch('alprd-alt');

        //Print the tubes statistics
        echo "----Tube Statistics----\r\n\r\n";
        var_dump($pheanstalk->statsTube('alprd-alt'));
        echo "\r\n\r\n----End of Tube Statistics----\r\n\r\n";

        //Do this forever
        while(true){
            try{
                //Reserve a job off the queue
                $job = $pheanstalk->reserve();
            }

            catch(Exception $e){
                //If we get an exception just keep going
                continue;
            }

            //If the job is NULL then there is nothing to do
            if($job == NULL){
                echo "Nothing for us to do right meow. Just chill. \r\n";
            }

            else{
                //Otherwise we have a job ready to process
                echo "There is something in the job queue. Get to work! \r\n";
                echo "----Job Data----\r\n\r\n";
                echo $job->getData() . "\r\n\r\nJob ID:" . $job->getId() . "\r\n\r\n";
                echo "----End of Job Data ----\r\n\r\n";
            }
        }
    ?>

JSON Plate Results
-------------------------

OpenALPR generates an "alpr_results" JSON value for every frame of video in which a license plate is recognized. This value will be sent multiple times for a single vehicle. For example, as a vehicle drives past the camera over the course of three seconds, OpenALPR may recognize the same license plate fifty times. All fifty recognition results will be recorded as alpr_results JSON values.

The number of recognition results largely depends on the processing speed and number of cameras being processed. A faster processor will produce more of these per vehicle because it is able to process video frames at a faster rate.  

Individual plate results are best suited for applications that need to operate on plate data in real time. 

JSON Plate results are sent in the following format:

.. code-block:: json

    {
      "version": 2,
      "data_type": "alpr_results",
      "epoch_time": 1490574589596,
      "img_width": 1280,
      "img_height": 720,
      "processing_time_ms": 259.132385,
      "error": false,
      "regions_of_interest": [
        {
          "x": 60,
          "y": 600,
          "width": 620,
          "height": 120
        },
        {
          "x": 0,
          "y": 0,
          "width": 1280,
          "height": 570
        }
      ],
      "results": [
        {
          "plate": "AE1T3E",
          "confidence": 93.904076,
          "matches_template": 1,
          "plate_index": 0,
          "region": "mo",
          "region_confidence": 34,
          "processing_time_ms": 17.029213,
          "requested_topn": 10,
          "coordinates": [
            {
              "x": 110,
              "y": 300
            },
            {
              "x": 227,
              "y": 295
            },
            {
              "x": 227,
              "y": 348
            },
            {
              "x": 113,
              "y": 352
            }
          ],
          "vehicle_region": {
            "x": 0,
            "y": 34,
            "width": 433,
            "height": 433
          },
          "candidates": [
            {
              "plate": "AE1T3E",
              "confidence": 93.904076,
              "matches_template": 1
            },
            {
              "plate": "AET3E",
              "confidence": 83.211739,
              "matches_template": 0
            },
            {
              "plate": "A1T3E",
              "confidence": 79.840271,
              "matches_template": 0
            },
            {
              "plate": "AE1T3F",
              "confidence": 79.130386,
              "matches_template": 1
            },
            {
              "plate": "AEIT3E",
              "confidence": 78.878433,
              "matches_template": 0
            },
            {
              "plate": "4E1T3E",
              "confidence": 78.217575,
              "matches_template": 0
            },
            {
              "plate": "AT3E",
              "confidence": 69.147926,
              "matches_template": 0
            },
            {
              "plate": "AET3F",
              "confidence": 68.438042,
              "matches_template": 0
            },
            {
              "plate": "4ET3E",
              "confidence": 67.52523,
              "matches_template": 0
            },
            {
              "plate": "A1T3F",
              "confidence": 65.066574,
              "matches_template": 0
            }
          ]
        }
      ],
      "uuid": "unspecified-cam16488027-1490574589596",
      "camera_id": 16488027,
      "agent_uid": "YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY",
      "agent_version": "2.3.111",
      "agent_type": "alprd",
      "site_id": "unspecified",
      "company_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }


JSON Group Results
-------------------------


OpenALPR generates an "alpr_group" JSON value for a collection of similar license plates, generally delegating a single plate group per vehicle. When the plate is not seen for a number of seconds, the plate_group result is sent with a reference (by UUID) to all the individual plate results that make up the group. The group also includes a best estimate for the license plate number based on a weighted score from all the frames included in the group.

Additionally, each plate group includes a detection value for the make/model, color, and vehicle body type.

Group results are most suitable for surveillance-type purposes. When these values are used, results may be delayed up to ten seconds after the vehicle has passed, so it is important that the application is not sensitive to this delay. If more real-time results are needed, it is recommended that you ignore the plate_group value and use only the individual plate results.

ALPR group results are sent in the following JSON format:

.. code-block:: json

    {
       "data_type" : "alpr_group",
       "version" : 2,
       "company_id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
       "agent_uid" : "YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY",
       "camera_id" : 2090267338,
       "best_plate_number" : "6PK132",
       "best_confidence" : 90.2536163330078,
       "best_region" : "us-mo",
       "best_region_confidence" : 83,
       "best_image_width" : 1280,
       "best_image_height" : 720,
       "best_uuid" : "YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY-2090267338-1538924513527",
       "matches_template" : false,
       "epoch_start" : 1538924513527,
       "epoch_end" : 1538924513627,
       "frame_start" : 68,
       "frame_end" : 71,
       "is_parked" : false
       "agent_type" : "alprd",
       "user_data" : "hello world",
       "uuids" : [
          "YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY-2090267338-1538924513527",
          "YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY-2090267338-1538924513627"
       ],
       "travel_direction" : 285.945404052734,
       "agent_version" : "2.6.101",
       "country" : "us",
       "plate_indexes" : [
          0,
          0
       ],
       "candidates" : [
          {
             "matches_template" : 0,
             "confidence" : 90.2536163330078,
             "plate" : "6PK132"
          }
          {
             "matches_template" : 0,
             "confidence" : 70.315123252332,
             "plate" : "6PKI32"
          }
       ],
       "best_plate" : {
          "plate_index" : 0,
          "confidence" : 90.2536163330078,
          "region_confidence" : 70,
          "plate_crop_jpeg" : "...base64_jpeg_bytes...",
          "processing_time_ms" : 21.2537994384766,
          "candidates" : [
             {
                "plate" : "6PK132",
                "matches_template" : 0,
                "confidence" : 90.2536163330078
             },
             {
                "confidence" : 75.9956741333008,
                "matches_template" : 0,
                "plate" : "6PKI32"
             },
             {
                "plate" : "6PK32",
                "confidence" : 75.5074081420898,
                "matches_template" : 0
             },
             {
                "matches_template" : 0,
                "confidence" : 75.3532333374023,
                "plate" : "6PKA32"
             },
             {
                "plate" : "6PK12",
                "matches_template" : 0,
                "confidence" : 74.8602905273438
             },
             {
                "matches_template" : 0,
                "confidence" : 74.8599395751953,
                "plate" : "PK132"
             },
             {
                "confidence" : 74.7018280029297,
                "matches_template" : 0,
                "plate" : "GPK132"
             },
             {
                "confidence" : 74.6972503662109,
                "matches_template" : 0,
                "plate" : "6PK122"
             },
             {
                "plate" : "6PKI2",
                "matches_template" : 0,
                "confidence" : 60.6023483276367
             }
          ],
          "region" : "us-mo",
          "matches_template" : 0,
          "requested_topn" : 20,
          "plate" : "6PK132",
          "vehicle_region" : {
             "y" : 71,
             "width" : 229,
             "height" : 229,
             "x" : 0
          },
          "coordinates" : [
             {
                "y" : 219,
                "x" : 62
             },
             {
                "x" : 115,
                "y" : 217
             },
             {
                "y" : 245,
                "x" : 114
             },
             {
                "x" : 61,
                "y" : 247
             }
          ]
       },
    }



JSON Heartbeat
-------------------------

Every minute, the OpenALPR agent adds one heartbeat message to the queue. The heartbeat provides general health and status information. The format is as follows:

.. code-block:: json

    {
       "data_type" : "heartbeat",
       "company_id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       "agent_uid" : "YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY",
       "agent_hostname" : "mymachine",
       "license_systemid" : "10799630758371023307",
       "license_key" : "...license_key...",
       "license_valid" : true,
       "memory_swapused_bytes" : 8265138176,
       "memory_last_update" : 1538925240573,
       "memory_total_bytes" : 67475775488,
       "memory_swaptotal_bytes" : 12813594624,
       "memory_consumed_bytes" : 67061047296,
       "cpu_last_update" : 1538925240573,
       "cpu_cores" : 8,
       "cpu_usage_percent" : 54.1457290649414,
       "disk_quota_earliest_result" : 1538924513527,
       "disk_quota_total_bytes" : 2147483648,
       "disk_quota_consumed_bytes" : 524288000,
       "processing_threads_configured" : 1,
       "processing_threads_active" : 1,
       "timestamp" : 1538925244693,
       "daemon_uptime_seconds" : 35,
       "system_uptime_seconds" : 585390,
       "recording_enabled" : false,
       "agent_type" : "alprd",
       "agent_version" : "2.6.101",
       "beanstalk_queue_size" : 0,
       "openalpr_version" : "2.6.101",
       "os" : "linux",
       "video_streams" : [
          {
             "fps" : 30,
             "camera_id" : 2090267338,
             "camera_name" : "h265",
             "last_update" : 1538925244689,
             "is_streaming" : true,
             "url" : "rtsp://192.168.0.1/stream"
          }
       ],
    }


Local Image Retrieval
-------------------------

The OpenALPR agent exposes a simple web service for retrieving license plate images. Each image is referenced by a UUID that is sent along with the JSON metadata.

By default, this web server is disabled on the agent, but you can turn it on by adding "web_server_enabled = 1" to the alprd.conf file.

Assuming that the daemon port is set to the default (8355), the full image is referenced with the following URL:

  - http://[*Agent_IP*]:8355/img/[*plate_event_uuid*].jpg

In some cases, you may prefer to retrieve a cropped image of just the license plate. This requires significantly less bandwidth than downloading the entire source image. The X and Y coordinates can be computed from the JSON metadata x/y coordinates of the license plate. The x1/y1 coordinates reference the top left of the license plate crop region, and the x2/y2 coordinates reference the bottom right. For example, assuming the crop is located at (477,258), (632,297):

  - http://[*Agent_IP*]:8355/crop/[*plate_event_uuid*]?x1=477&y1=258&x2=632&y2=297

In addition, the web server exposes a `web service API <api/?api=cloudstream>`_ for searching license plates and groups. Detailed documentation is available in the :ref:`web server section <web_services_api>`
