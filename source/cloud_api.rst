
.. _cloud_api:

Cloud API
=============

The OpenALPR Cloud API is a web-based service that analyzes images for license plates as well as vehicle information such as make, model, and color. 
The Cloud API service is easy to integrate into your application via a web-based REST service. When you send image data to the OpenALPR API, we process that data 
and return JSON data describing the license plate and vehicle.

Check out the online demo: http://www.openalpr.com/demo-image.html

Sign Up
---------

When you're ready to get started, sign-up for an account at https://cloud.openalpr.com/

Once enrolled, you will automatically be assigned a free account with a limited number of API credits per month. Each time you use the service, you will use one or more 
API credits. You may enter your credit card information and upgrade your plan to receive access to more credits per month.

Integration 
-----------------------

Because the OpenALPR Cloud API is REST-based, it works with any programming language on any operating system. You can make API calls using whatever method
you prefer.

The endpoint for the OpenALPR Cloud APIs are described in the `REST API documentation <api/?api=cloudapi>`_.

Code Samples
----------------------

For example, to connect to the OpenALPR API from the command line:


Bash
###############

.. code-block:: bash

    curl -X POST -F image=@/car1.jpg 'https://api.openalpr.com/v2/recognize?recognize_vehicle=1&country=us&secret_key=sk_DEMODEMODEMODEMODEMODEMO'

Python
###############

.. code-block:: python

    #!/usr/bin/python

    import requests
    import base64
    import json

    # Sample image file is available at http://plates.openalpr.com/ea7the.jpg
    IMAGE_PATH = '/tmp/sample.jpg'
    SECRET_KEY = 'sk_DEMODEMODEMODEMODEMODEMO'

    with open(IMAGE_PATH, 'rb') as image_file:
        img_base64 = base64.b64encode(image_file.read())

    url = 'https://api.openalpr.com/v2/recognize_bytes?recognize_vehicle=1&country=us&secret_key=%s' % (SECRET_KEY)
    r = requests.post(url, data = img_base64)

    print(json.dumps(r.json(), indent=2))


C#
#########################

.. code-block:: C#

    using System;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Net.Http;
    using System.IO;

    namespace ConsoleApplicationTest
    {
        class Program
        {
            private static readonly HttpClient client = new HttpClient();

            public static async Task<string> ProcessImage(string image_path)
            {
                string SECRET_KEY = "sk_DEMODEMODEMODEMODEMODEMO";

                Byte[] bytes = File.ReadAllBytes(image_path);
                string imagebase64 = Convert.ToBase64String(bytes);

                var content = new StringContent(imagebase64);

                var response = await client.PostAsync("https://api.openalpr.com/v2/recognize_bytes?recognize_vehicle=1&country=us&secret_key=" + SECRET_KEY, content).ConfigureAwait(false);

                var buffer = await response.Content.ReadAsByteArrayAsync().ConfigureAwait(false);
                var byteArray = buffer.ToArray();
                var responseString = Encoding.UTF8.GetString(byteArray, 0, byteArray.Length);

                return responseString;
            }

            static void Main(string[] args)
            {
                Task<string> recognizeTask = Task.Run(() => ProcessImage(@"C:\Temp\car1.jpg"));
                recognizeTask.Wait();
                string task_result = recognizeTask.Result;
                
                System.Console.WriteLine(task_result);
            }
        }
    }

Java
###############

.. code-block:: Java

    import java.net.*;
    import java.io.*;
    import java.nio.file.*;
    import java.util.Base64;


    class TestOpenALPR {

        public static void main(String[] args)
        {
            try
            {
                String secret_key = "sk_DEMODEMODEMODEMODEMODEMO";

                // Read image file to byte array
                Path path = Paths.get("/storage/projects/alpr/samples/testing/car1.jpg");
                byte[] data = Files.readAllBytes(path);

                // Encode file bytes to base64
                byte[] encoded = Base64.getEncoder().encode(data);

                // Setup the HTTPS connection to api.openalpr.com
                URL url = new URL("https://api.openalpr.com/v2/recognize_bytes?recognize_vehicle=1&country=us&secret_key=" + secret_key);
                URLConnection con = url.openConnection();
                HttpURLConnection http = (HttpURLConnection)con;
                http.setRequestMethod("POST"); // PUT is another valid option
                http.setFixedLengthStreamingMode(encoded.length);
                http.setDoOutput(true);

                // Send our Base64 content over the stream
                try(OutputStream os = http.getOutputStream()) {
                    os.write(encoded);
                }

                int status_code = http.getResponseCode();
                if (status_code == 200)
                {
                    // Read the response
                    BufferedReader in = new BufferedReader(new InputStreamReader(
                                            http.getInputStream()));
                    String json_content = "";
                    String inputLine;
                    while ((inputLine = in.readLine()) != null) 
                        json_content += inputLine;
                    in.close();

                    System.out.println(json_content);
                }
                else
                {
                    System.out.println("Got non-200 response: " + status_code);
                }


            }
            catch (MalformedURLException e)
            {
                System.out.println("Bad URL");
            }
            catch (IOException e)
            {
                System.out.println("Failed to open connection");
            }

        }
    }

JavaScript
###############

.. code-block:: Java

<html>
    <title>
        Cloud API Demo
    </title>

    <head>
        <script>
	    // Open connection to api.openalpr.com
	    var secret_key = "sk_DEMODEMODEMODEMODEMODEMO";
	    var url = "https://api.openalpr.com/v2/recognize_bytes?recognize_vehicle=1&country=us&secret_key=" + secret_key;
	    var xhr = new XMLHttpRequest();
        xhr.open("POST", url);

        // Send POST data and display response
        xhr.send("base64_string");
        xhr.onreadystatechange = function() {
            if (xhr.readyState == 4) {
                document.getElementById("response").innerHTML = xhr.responseText;
            } else {
                document.getElementById("response").innerHTML = "Waiting on response...";
            }
        }
        </script>
    </head>

    <body>
        JSON response: <p id="response"></p><br>
    </body>
</html>

Results
###############

The JSON response is as follows:

.. code-block:: json


    {
       "uuid" : "",
       "regions_of_interest" : [
          {
             "height" : 600,
             "width" : 600,
             "y" : 0,
             "x" : 0
          }
       ],
       "credits_monthly_used" : 7040,
       "credit_cost" : 2,
       "img_height" : 600,
       "error" : false,
       "epoch_time" : 1522978197756,
       "version" : 2,
       "results" : [
          {
             "plate_index" : 0,
             "vehicle_region" : {
                "y" : 7,
                "width" : 568,
                "height" : 568,
                "x" : 16
             },
             "processing_time_ms" : 68.9315719604492,
             "vehicle" : {
                "body_type" : [
                   {
                      "name" : "sedan-compact",
                      "confidence" : 89.6389389038086
                   },
                   {
                      "name" : "suv-standard",
                      "confidence" : 2.92187452316284
                   },
                   {
                      "name" : "sedan-wagon",
                      "confidence" : 2.83985614776611
                   }
                ],
                "year" : [
                   {
                      "confidence" : 47.3032341003418,
                      "name" : "2000-2004"
                   },
                   {
                      "name" : "2005-2009",
                      "confidence" : 39.6665573120117
                   },
                   {
                      "name" : "1995-1999",
                      "confidence" : 7.91491031646729
                   }
                ],
                "make_model" : [
                   {
                      "confidence" : 40.9104766845703,
                      "name" : "chevrolet_hhr"
                   },
                   {
                      "name" : "toyota_ist",
                      "confidence" : 22.9741859436035
                   },
                   {
                      "confidence" : 6.41662836074829,
                      "name" : "chevrolet_trailblazer"
                   },
                   {
                      "confidence" : 1.58923923969269,
                      "name" : "mazda_verisa"
                   },
                   {
                      "confidence" : 1.31779313087463,
                      "name" : "nissan_micra"
                   },
                   {
                      "name" : "mazda_tribute",
                      "confidence" : 1.24455153942108
                   },
                   {
                      "confidence" : 0.991917014122009,
                      "name" : "chevrolet_uplander"
                   }
                ],
                "make" : [
                   {
                      "confidence" : 32.4275550842285,
                      "name" : "chevrolet"
                   },
                   {
                      "name" : "toyota",
                      "confidence" : 31.9965953826904
                   },
                   {
                      "confidence" : 15.4623928070068,
                      "name" : "nissan"
                   },
                   {
                      "confidence" : 8.25705337524414,
                      "name" : "daihatsu"
                   },
                   {
                      "name" : "mazda",
                      "confidence" : 3.8371422290802
                   }
                ],
                "color" : [
                   {
                      "name" : "silver-gray",
                      "confidence" : 73.2146682739258
                   },
                   {
                      "name" : "blue",
                      "confidence" : 15.9568424224854
                   }
                ],
                "orientation" : [
                   {
                      "name" : "180",
                      "confidence" : 97.7202453613281
                   },
                   {
                      "confidence" : 1.84529066085815,
                      "name" : "225"
                   }
                ]
             },
             "matches_template" : 1,
             "plate" : "627WWI",
             "requested_topn" : 10,
             "coordinates" : [
                {
                   "x" : 237,
                   "y" : 357
                },
                {
                   "y" : 359,
                   "x" : 364
                },
                {
                   "x" : 362,
                   "y" : 416
                },
                {
                   "x" : 237,
                   "y" : 414
                }
             ],
             "region_confidence" : 99,
             "region" : "wa",
             "candidates" : [
                {
                   "matches_template" : 1,
                   "confidence" : 94.9990844726562,
                   "plate" : "627WWI"
                }
             ],
             "confidence" : 94.9990844726562
          }
       ],
       "credits_monthly_total" : 10000000000,
       "img_width" : 600,
       "data_type" : "alpr_results",
       "processing_time" : {
          "total" : 621.703000000025,
          "plates" : 140.277725219727,
          "vehicles" : 476.741000000004
       }
    }
