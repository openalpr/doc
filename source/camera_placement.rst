.. _camera_placement:

****************************
Camera Placement Guide
****************************

"How accurate is your LPR system?" This is one of the most common questions we are asked at OpenALPR, as well as the most difficult to answer. License plate recognition accuracy is highly dependent on the input video. If the camera was properly set up, precision is likely. Conversely, results may be ambiguous if the camera did not clearly capture license plates.

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
  2. Angle between camera and plate
  3. Pixels on target

Lighting
==========

Adequate lighting is critical for capturing a clear image of a license plate on a moving vehicle. Sometimes this requires the installation of an external illuminator. The amount of lighting required to capture fast-moving objects (without the help of bright sunlight) is often more than you may expect.  

Cameras automatically adjust their shutter speed based on the amount of available light. At high noon on a clear, sunny day, the shutter may only open for 1/10000th of a second to capture a video frame. Because there would be so much light in the environment, if the shutter opened for a longer period, the photo would be overexposed. On a dark night, the shutter may remain open for an entire second to draw enough light.

The shutter speed is important because it directly correlates to the sharpness of a moving vehicle. In the example above, if the shutter were to open for 1/10000th of a second, a vehicle traveling 60 mph would move only 1/10th of an inch. This would result in virtually no motion blur on the license plate image. If the shutter were to open for a full second, that same vehicle would travel 88 feet. In the latter case, the license plate would be illegible to a human or computer.

  .. image:: images/camera_setup/motion_blur.jpg
      :scale: 100%
      :alt: Image showing effect of motion blur

Unfortunately, simply increasing the shutter speed will not improve results. If too little light is available, the image may appear completely black. Instead, you must increase the lighting.

If the camera is capable of infrared capture, you can point an external infrared illuminator in the direction of the vehicles. This has the advantage of being invisible to the naked eye. Alternatively, you can install a spotlight.

Some cameras also include an IR cut filter to aid night captures. This is helpful when the vehicle image is captured from the front with very bright headlights pointed toward the camera. The filter will remove the headlights, leaving the dimmer license plate image clearly visible.

  .. image:: images/camera_setup/lighting_combined.png
      :scale: 100%
      :alt: Image of proper lighting of plate at night

Angle
=======

When setting up your camera, try to capture the license plate at as direct an angle as possible. Accuracy degrades significantly beyond a 30-degree horizontal or vertical angle.  

Using a longer-range camera lens to zoom the field of view is one way to decrease the angle. For example, pointing a zoomed-in camera parallel with the road will provide better results than aiming a wider-angle lens more perpendicularly.

  .. image:: images/camera_setup/camera_zoom_angle.png
      :scale: 100%
      :alt: Image of correct camera placement angles

In some cases, the angle of capture cannot be controlled. OpenALPR offers a feature that calibrates the camera to compensate for the angle of capture. This is available in the web interface under Configuration -> Agents -> Configure -> Camera Calibration. Adjust the sliders until the rotations/angles show a more straight-on license plate in the image. 

Pixels on Target
==================

The number of pixels captured for each license plate is more important than the distance to the target. In fact, one OpenALPR customer successfully captures license plates from almost a mile away. To increase pixels on target, adjust your camera's zoom and resolution.

If your camera is capable of automatic optical zoom, or if you have a choice of lenses, it's generally best to zoom the field of view to the area where the license plates will be captured. The screenshots below compare a camera with a wide field of view to a camera zoomed to the area of the license plates. Both approaches will work because the pixel width is sufficient in each shot; however, plate recognition will be more accurate in the zoomed example.

  .. image:: images/camera_setup/zoom_combined.png
      :scale: 100%
      :alt: Image of properly zoomed image

Adjusting your camera's resolution can also increase the pixels for each plate. However, increasing the number of pixels will also increase the processing time. Therefore, if CPU resources are not infinite, increasing the resolution too much may decrease accuracy. We recommend setting the camera resolution no higher than 720p, making sure the plates still have enough pixels to be detected. If the camera is sufficiently zoomed, further decreasing the resolution may, counterintuitively, improve accuracy.

OpenALPR reads USA plates wider than 75 pixels and European plates wider than 90 pixels. Pixel width beyond 250 pixels will not noticeably increase accuracy.

Conclusion
============

Following this guide will optimize your results with OpenALPR. Sign up today and try it out on your cameras. If you have any questions or need help reviewing your configuration for possible improvements, don't hesitate to `let us know! <http://www.openalpr.com/contact.html>`_