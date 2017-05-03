
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

Integration Libraries
-----------------------

Because the OpenALPR Cloud API is REST-based, it works with any programming language on any operating system. You can make API calls using whatever method
you prefer.

To simplify integration, the OpenALPR Cloud API also includes permissively licensed open-source client libraries in a variety of languages. 
The GitHub repo is available here: https://github.com/openalpr/cloudapi


Manual Integration
----------------------

The endpoint for the OpenALPR Cloud APIs are described in the `REST API documentation <api/cloudapi.html>`_.  Our REST API is defined explicitly in a `Swagger Yaml Descriptor <api/specs/cloudapi.yaml>`_

For example, to connect to the OpenALPR API from the command line:

    curl -X POST -F image=@/car1.jpg 'https://api.openalpr.com/v2/recognize?recognize_vehicle=1&country=us&secret_key=sk_DEMODEMODEMODEMODEMODEMO'

The JSON response is as follows:

.. code-block:: json


    {
      "data_type": "alpr_results",
      "epoch_time": 1493845123778,
      "processing_time": {
        "total": 563.16100000004,
        "plates": 105.437424,
        "vehicles": 387.21999999984
      },
      "img_height": 600,
      "img_width": 600,
      "results": [
        {
          "plate": "627WWI",
          "confidence": 94.061699,
          "region_confidence": 83,
          "vehicle_region": {
            "y": 31,
            "x": 34,
            "height": 529,
            "width": 529
          },
          "region": "wa",
          "plate_index": 0,
          "processing_time_ms": 14.55243,
          "candidates": [
            {
              "matches_template": 1,
              "plate": "627WWI",
              "confidence": 94.061699
            },
            {
              "matches_template": 0,
              "plate": "627WI",
              "confidence": 81.672684
            },
            {
              "matches_template": 1,
              "plate": "627WKI",
              "confidence": 79.146172
            },
            {
              "matches_template": 1,
              "plate": "627WVI",
              "confidence": 78.999771
            },
            {
              "matches_template": 1,
              "plate": "627KWI",
              "confidence": 78.723572
            },
            {
              "matches_template": 0,
              "plate": "627WW1",
              "confidence": 78.552238
            },
            {
              "matches_template": 0,
              "plate": "627I",
              "confidence": 66.982719
            },
            {
              "matches_template": 0,
              "plate": "627KI",
              "confidence": 66.334557
            },
            {
              "matches_template": 0,
              "plate": "627W1",
              "confidence": 66.163216
            },
            {
              "matches_template": 0,
              "plate": "627VI",
              "confidence": 64.309807
            }
          ],
          "coordinates": [
            {
              "y": 358,
              "x": 242
            },
            {
              "y": 358,
              "x": 356
            },
            {
              "y": 411,
              "x": 356
            },
            {
              "y": 410,
              "x": 244
            }
          ],
          "vehicle": {
            "color": [
              {
                "confidence": 40.467598,
                "name": "blue"
              },
              {
                "confidence": 27.456575,
                "name": "silver"
              },
              {
                "confidence": 22.455431,
                "name": "gray"
              },
              {
                "confidence": 7.715964,
                "name": "green"
              },
              {
                "confidence": 1.071705,
                "name": "white"
              },
              {
                "confidence": 0.591513,
                "name": "black"
              },
              {
                "confidence": 0.109028,
                "name": "red"
              },
              {
                "confidence": 0.090082,
                "name": "gold"
              },
              {
                "confidence": 0.040028,
                "name": "yellow"
              },
              {
                "confidence": 0.002081,
                "name": "_ignore_"
              }
            ],
            "make": [
              {
                "confidence": 81.062904,
                "name": "mini"
              },
              {
                "confidence": 14.504533,
                "name": "chevrolet"
              },
              {
                "confidence": 2.192204,
                "name": "jeep"
              },
              {
                "confidence": 1.432994,
                "name": "scion"
              },
              {
                "confidence": 0.619696,
                "name": "fiat"
              },
              {
                "confidence": 0.093039,
                "name": "pontiac"
              },
              {
                "confidence": 0.063397,
                "name": "hyundai"
              },
              {
                "confidence": 0.01175,
                "name": "volvo"
              },
              {
                "confidence": 0.005218,
                "name": "saturn"
              },
              {
                "confidence": 0.002759,
                "name": "dodge"
              }
            ],
            "body_type": [
              {
                "confidence": 99.990334,
                "name": "sedan-compact"
              },
              {
                "confidence": 0.006352,
                "name": "sedan-wagon"
              },
              {
                "confidence": 0.00316,
                "name": "suv-standard"
              },
              {
                "confidence": 0.000124,
                "name": "sedan-sport"
              },
              {
                "confidence": 1.3e-5,
                "name": "suv-crossover"
              },
              {
                "confidence": 7.0e-6,
                "name": "sedan-standard"
              },
              {
                "confidence": 6.0e-6,
                "name": "van-mini"
              },
              {
                "confidence": 1.0e-6,
                "name": "van-full"
              },
              {
                "confidence": 7.532104e-7,
                "name": "antique"
              },
              {
                "confidence": 3.668671e-7,
                "name": "sedan-convertible"
              }
            ],
            "make_model": [
              {
                "confidence": 64.938133,
                "name": "chevrolet_sonic"
              },
              {
                "confidence": 25.10136,
                "name": "mini_cooper"
              },
              {
                "confidence": 7.424521,
                "name": "scion_xa"
              },
              {
                "confidence": 0.8777,
                "name": "mini_cooper-clubman"
              },
              {
                "confidence": 0.790787,
                "name": "jeep_liberty"
              },
              {
                "confidence": 0.504434,
                "name": "fiat_500"
              },
              {
                "confidence": 0.15509,
                "name": "chevrolet_aveo"
              },
              {
                "confidence": 0.022665,
                "name": "jeep_compass"
              },
              {
                "confidence": 0.022426,
                "name": "volvo_c30"
              },
              {
                "confidence": 0.019563,
                "name": "saturn_ion"
              }
            ]
          },
          "matches_template": 1,
          "requested_topn": 10
        }
      ],
      "credits_monthly_used": 100,
      "version": 2,
      "credits_monthly_total": 1000,
      "error": false,
      "regions_of_interest": [
        {
          "y": 0,
          "x": 0,
          "height": 600,
          "width": 600
        }
      ],
      "credit_cost": 2
    }