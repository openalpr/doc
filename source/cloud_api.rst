
.. _cloud_api:

Cloud API
=============

The OpenALPR Cloud API is a web-based service that analyzes images for license plates as well as vehicle information such as make, model, and color.  
The Cloud API service is easy to integrate into your application via a web-based REST service.  You send image data to the OpenALPR API, we process the data, 
and return JSON data describing the license plate and vehicle.

Check out the online demo: http://www.openalpr.com/demo-image.html

Sign-Up
---------

When you're ready to get started, sign-up for an account at https://cloud.openalpr.com/

Once enrolled, you will automatically be assigned a free account that has a limited number of API credits per month.  Each time you use the service, you use one or more 
API credits.  You may enter your credit card information and upgrade your plan to give you access to more credits per month.

Integrate
----------

Because the OpenALPR Cloud API is REST-based, it works with any programming language on any operating system.  You can make API calls using whatever method
you prefer.

To make integration easier, the OpenALPR Cloud API also includes permissively licensed open source client libraries in a variety of languages.  
The GitHub repo is available here: https://github.com/openalpr/cloudapi

Check out the `REST API documentation <api/cloudapi.html>`_ for more detailed information about the REST service.  
This is generated from the `OpenALPR Cloud API Swagger definition <api/specs/cloudapi.yaml>`_