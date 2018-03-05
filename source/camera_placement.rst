
****************************
Camera Configuration
****************************


.. _camera_placement:

Camera Placement Guide
--------------------------

"How accurate is your LPR system?" This is one of the most common questions we are asked at OpenALPR, as well as the most difficult to answer. License plate recognition accuracy is highly dependent on the quality of input video. If a human can’t discern the license plate characters, then software will also struggle. If the camera was properly set up, high accuracy is likely. Conversely, results may be less accurate if the camera did not clearly capture license plates.

To verify that your camera can work with an LPR system, try performing a test. Freeze a frame as a car drives past and try to read the number plates. If you cannot do this easily, the LPR system will not be successful. Even if the plate numbers are legible, the camera may not be ideally configured for LPR. Human brains are remarkably good at identifying patterns from visual imagery; however, a computer needs a clear, ideal image to perform optimally. 

In the image below, the license plate appears to be legible. We can read "GFP 3054." The letters, however, are not sharply contrasted. Their blurry shades of gray blend into the plate background and other characters. A machine will struggle to accurately read a plate such as this.

  .. image:: images/camera_setup/unclear_plate_combined.png
      :scale: 100%
      :alt: Image of unclear plate

The same license plate is much better defined in the following photo, in which the lighting was upgraded and the camera was zoomed in.

  .. image:: images/camera_setup/clear_plate_combined.png
      :scale: 100%
      :alt: Image of clear plate

The most important factors affecting LPR accuracy are camera placement and video quality. To achieve the highest possible performance for your LPR system, optimize the following variables:

  1. Lighting
  2. Angle of Capture
  3. Pixels on Target
  4. Camera Image Settings

Lighting
==========

Adequate lighting is critical for capturing a clear image of a license plate on a moving vehicle. Sometimes this requires the installation of an external illuminator. The amount of lighting required to capture fast-moving objects (without the help of bright sunlight) is often more than you may expect.  

Cameras automatically adjust their shutter speed based on the amount of available light. At high noon on a clear, sunny day, the shutter may only open for 1/10000th of a second to capture a video frame. Because there would be so much light in the environment, if the shutter opened for a longer period, the photo would be overexposed. On a dark night, the shutter may remain open for an entire second to draw enough light.

The shutter speed is important because it directly correlates to the sharpness of a moving vehicle. In the example above, if the shutter were to open for 1/10000th of a second, a vehicle traveling 60 mph would move only 1/10th of an inch. This would result in virtually no motion blur on the license plate image. If the shutter were to open for a full second, that same vehicle would travel 88 feet. In the latter case, the license plate would be illegible to a human or computer.

  .. image:: images/camera_setup/motion_blur.jpg
      :scale: 100%
      :alt: Image showing effect of motion blur

Unfortunately, simply increasing the shutter speed will not improve results. If too little light is available, the image may appear completely black. Instead, you must increase the lighting.

If the camera is day/night capable and has an IR cut filter (to filter out the visible "color" light) you can point an external infrared illuminator in the direction of the vehicle. This has the advantage of being invisible to the naked eye, yet be reflective off the plate. The filter will remove the headlights, leaving the dimmer license plate image clearly visible. Alternatively, you can install a spotlight or white LED lights.  

  .. image:: images/camera_setup/lighting_combined.png
      :scale: 100%
      :alt: Image of proper lighting of plate at night

Angle of Capture
==================

When setting up your camera, try to capture the license plate at as direct an angle as possible. Accuracy degrades beyond a 30-degree horizontal or vertical angle.  If possible mount the camera high enough and angled slightly downward so as to avoid direct headlight/taillight glare.

Using a longer-range camera lens to zoom the field of view is one way to decrease the angle. For example, pointing a zoomed-in camera parallel with the road will provide better results than aiming a wider-angle lens more perpendicularly.

  .. image:: images/camera_setup/camera_zoom_angle.png
      :scale: 100%
      :alt: Image of correct camera placement angles


Pixels on Target
==================

The number of pixels captured for each license plate is more important than the distance to the target. In fact, one OpenALPR customer successfully captures license plates from almost a mile away. To increase pixels on target, adjust your camera's zoom and resolution.

If your camera is capable of automatic optical zoom, or if you have a choice of lenses, it's generally best to zoom the field of view to the area where the license plates will be captured. The screenshots below compare a camera with a wide field of view to a camera zoomed to the area of the license plates. Both approaches will work because the pixel width is sufficient in each shot; however, plate recognition will be more accurate in the zoomed example.

  .. image:: images/camera_setup/zoom_combined.png
      :scale: 100%
      :alt: Image of properly zoomed image

Adjusting your camera's resolution can also increase the pixels for each plate. However, increasing the number of pixels will also increase the processing time. Therefore, if CPU resources are not infinite, increasing the resolution too much may decrease accuracy. We recommend setting the camera resolution no higher than 720p, making sure the plates still have enough pixels to be detected. If the camera is sufficiently zoomed, further decreasing the resolution may, counterintuitively, improve accuracy.

OpenALPR reads USA plates wider than 75 pixels and European plates wider than 90 pixels. Pixel width beyond 250 pixels will not noticeably increase accuracy.

Camera Image Settings
=======================

Getting the correct image settings takes some trial and error because no two scenes are alike.  If your goal to capture plates 24/7 you’ll have to make some trade-offs. Settings that work best at night aren’t necessarily going to work best during the day and vice versa.  Most modern IP cameras provide good image quality using the default/automatic settings.  However here are some suggestions that you can try to improve accuracy in varying lighting conditions.

  - **Resolution/Frame Rate** = 720p (1280x720) and 20 frames per second are a good starting point depending on how far away the camera is to the plate. Remember your pixels on target equation above to determine maximum distance of the camera.  The more you increase the resolution the more CPU processing power is required unless you use the Detection Zones masking feature to tell OpenALPR where to look for plates.

  - **Compression** = 20. A lower setting will produce better image quality at the tradeoff of more bandwidth consumption.  

  - **Smart Codecs** = off.  If your camera manufacturer uses technology to compress the image based on a region of interest or motion detection disable it.

  - **Camera Capture Mode**

    - Wide Dynamic Range = Off. This feature adds noise to the image which affects accuracy in low light conditions.

  - **Image Appearance**

    - Color level = default setting
    - Brightness = default setting
    - Sharpness = 60% - 65%
    - Contrast = 60 - 75%

  - **White Balance**

    - White balance = Automatic
    - White balance window = Automatic

  - **Wide Dynamic Range**

    - Enable Dynamic Contrast = off

  - **Exposure Settings**

    - Exposure value = 70%
    - Exposure control = Automatic
    - Maximum Exposure Time =1/1000 second
    - Backlight compensation = off
    - Exposure zone = Auto
    - Shutter Speed = Fixed @ 1/2000 - for slow speed; 1/4000 + for highway speed.
    - Gain = Auto
    - Max Gain = +12 (day) +24 db (night). Avoid excessive gain settings which will add noise to the image.

  - **Image Settings**

    - Enable automatic iris adjustment = yes

  - **Day/Night**

    - IR cut filter = On (during Day), Off (Night) 

  - **IR Illumination** (if required)

    - Enable IR illumination = yes

Conclusion
============

Following this guide will optimize your results with OpenALPR. Sign up today and try it out on your cameras. If you have any questions or need help reviewing your configuration for possible improvements, don't hesitate to `let us know! <http://www.openalpr.com/contact.html>`_



Axis Best Practices
------------------------

Camera Positioning
====================

Mount the camera at a maximum range of 40 pixels per foot (or 131 pixels per meter) to ensure enough pixels on target (divide the horizontal camera resolution by 40 or 131 = maximum distance in feet/meter respectively). Use the pixel counter feature (see Focus & Zoom settings) to ensure at least 100 horizontal pixels (USA) or 150 (EU) and at least 80 vertical pixels on the plate.  

Keep the angle of capture less than 40 degrees horizontally and vertically. The more you can minimize the angle the better the results will be.  If possible mount the camera high enough and angled slightly downward so as to avoid direct headlight/taillight and solar roof/hood/boot glare. 


  .. figure:: images/camera_setup/axis-verticalmount.png
      :scale: 100%
      :alt: Maximum Vertical Angle

      Maximum Vertical Angle

  .. figure:: images/camera_setup/axis-horizontalmount.png
      :scale: 100%
      :alt: Maximum Horizontal Angle

      Maximum Horizontal Angle

  .. figure:: images/camera_setup/axis-idealmount.png
      :scale: 100%
      :alt: Ideal Horizontal Angle

      Ideal Horizontal Angle


Camera Settings
====================

  - **Resolution/Frame Rate** = 1080p (1920x1080) or 720p (1280x720) and 20 frames per second are a good starting point.  

  - **Compression** = 20.  A lower setting will produce better image quality.

  - **Zipstream** = Off

  - **Camera Capture Mode** 

    - WDR = Off.  This feature adds too much noise to the image which affects accuracy.

  - **Image Appearance**

    - Color level = 50
    - Brightness = 50
    - Sharpness = 60
    - Contrast = 70
    - Local Contrast = 55

  - **White Balance** 

    - White balance = Automatic
    - White balance window = Automatic

  - **Wide Dynamic Range**

    - Enable Dynamic Contrast = No checkmark

  - **Exposure Settings** 

    - Exposure value = 70
    - Exposure control = Automatic
    - Maximum exposure Time =1/1000 
    - Enable backlight compensation = Unchecked
    - Exposure zone = Auto
    - Shutter = Fixed 1/2000 - for slow speed; 1/4000 for highway speed.
    - Gain = Auto 
    - Max Gain = 24 db.  Avoid excessive gain settings which will add noise to the image.

  - **Image Settings**

    - Enable automatic iris adjustment = check

  - **Day/Night**

    - IR cut filter = On (Day), Off (Night) *if* NOT using an Event Action rule below = Auto.

  - **IR Illumination**

    - Enable IR illumination = check
    - Synchronize IR illumination with Day/Night = On *if* using Auto IR cut filter. Off *if* using an Event Action rule below.


Event Action Rules:
====================

Enable Event Action Rules to ensure IR cut filter is off and built-in IR illuminator (if equipped) is active during the night time or when lighting conditions warrant its use.  See two Action Rule examples:

  .. figure:: images/camera_setup/axis-actionrule1.png
      :scale: 100%
      :alt: Action Rule Configuration

  .. figure:: images/camera_setup/axis-actionrule2.png
      :scale: 100%
      :alt: Action Rule Configuration

Actions
**Day/Night Vision Mode** - Sets Auto mode, day mode or night mode. When this mode is selected, the Action Rule will switch the IR cut filter to Auto mode (depending on lighting conditions), to Day mode (IR cut filter on) or to Night mode (IR cut filter off). Note that the IR cut filter setting in Video & Audio > Camera Settings page must not be set to Auto.

**IR Illumination** - Turns on the built-in Infrared (IR) light illuminator. It can be used in an Action Rule to turn on the IR light illuminator so that the camera can perform video surveillance in areas with low light, without requiring addition of external lighting.