.. _camera_placement:

****************************
Camera Placement Guide
****************************

"How accurate is your LPR system?"  This is one of the most common questions we are asked at OpenALPR, and it is also the most difficult to answer.  License Plate Recognition accuracy is highly dependent on the input video.  If the camera is setup properly, accuracy will be high.  Conversely, if the camera does not clearly capture license plates, accuracy will be adversely affected.

A simple test to verify that your camera can work with an LPR system is to see for yourself.  Freeze a frame as a car drives past and try to read the number plates yourself.  If you cannot easily read the plate numbers, then the LPR system will not be successful.  Even if the plate numbers are legible, it still may not be ideally configured for LPR.  Human brains are remarkably good at identifying patterns from visual imagery.  A computer needs a clear, ideal image to perform optimally. 

In the image below, the license plate appears to be legible.  We can read the characters: "GFP 3054".  The letters, however, do not have a sharp contrast.  They blur into shades of gray that blend into the plate background and other characters.  A machine will struggle to read these plates accurately.

  .. image:: images/camera_setup/unclear_plate_combined.png
      :scale: 100%
      :alt: Image of unclear plate

The same license plate captured with upgraded lighting is much better defined when zoomed in.

  .. image:: images/camera_setup/clear_plate_combined.png
      :scale: 100%
      :alt: Image of clear plate

The most important factors that affect LPR accuracy are camera placement and video quality.  In order to achieve the highest possible performance for your LPR system, you must optimize the following variables:

  1. Lighting
  2. Angle between camera and plate
  3. Pixels on Target

Lighting
==========

Adequate lighting is critical to capturing a clear image of a license plate on a moving vehicle.  Sometimes this means installing an external illuminator.  The amount of lighting required to capture fast-moving objects (without the help of bright sunlight) is often more than you may expect.  

Cameras automatically adjust their shutter speed based on the amount of available light in the scene.  At high-noon, on a clear sunny day, the shutter may only open itself up for 1/10000th of a second to capture a video frame.  Because there is so much light in the environment, opening the shutter for longer would overexpose the photo.  On a dark night, the shutter may remain open for an entire second to capture enough light to capture the image.

The shutter speed is important because it directly correlates to the sharpness of a moving vehicle.  In the example above, if the shutter is opened for just 1/10000th of a second, a vehicle traveling 60mph would have only moved 1/10th of an inch.  This would result in virtually no motion blur on the license plate image.  If the shutter was opened for a full second, that same vehicle would have traveled 88 feet.  You can imagine how blurry that license plate would be if it was captured using such a slow shutter.  It would be illegible to a human or computer.

  .. image:: images/camera_setup/motion_blur.jpg
      :scale: 100%
      :alt: Image showing effect of motion blur

Unfortunately, simply setting the shutter speed to a faster value will not improve results; this is because there would be such a small amount of light available in the scene that the image may appear completely black.  Instead, you must increase the amount of lighting.

If the camera is capable of infrared capture, you can use an external infrared illuminator pointed in the direction of the vehicles.  This has the advantage of being invisible to the naked eye.  Alternatively, a spotlight can be installed to improve capture shutter speed.

Some cameras also include an IR cut filter to help for night captures.  This will help when the vehicle image is captured from the front with very bright headlights pointed towards the camera.  The filter will remove the extremely bright headlights, while leaving the less bright license plate image clearly visible.

  .. image:: images/camera_setup/lighting_combined.png
      :scale: 100%
      :alt: Image of proper lighting of plate at night

Angle
=======

A goal for setting up your camera is to capture the license plate at as direct an angle as possible.  Accuracy begins to degrade significantly beyond a 30 degree horizontal or vertical angle.  

Using a longer-range camera lens to zoom the field of view provides an opportunity to decrease the angle.  For example, pointing a zoomed-in camera parallel with the road will give better results than using a wider-angle lens aimed more perpendicularly.

  .. image:: images/camera_setup/camera_zoom_angle.png
      :scale: 100%
      :alt: Image of correct camera placement angles

In some cases, the angle of capture cannot be controlled.  OpenALPR includes a feature to calibrate the camera to compensate for the angle of capture.  This is available in the web interface under Configuration -> Agents -> Configure -> Camera Calibration.  Adjust the sliders until the rotation/angles show a more straight-on license plate in the image. 

Pixels on Target
==================

The number of pixels captured for each license plate is a more important factor than the distance to the target.  In fact, one OpenALPR customer successfully captures license plates from almost a mile away.  Two ways of increasing pixels on target are zoom and resolution.

If your camera is capable of automatic optical zoom, or you have a choice of lenses, it's generally best to zoom the field of view to the area where the license plates will be captured.  For example, the screenshots below compare a wide field of view with a camera zoomed to the area of the license plates.  Both approaches will work, since the pixel widths are sufficient in both shots; however, the zoomed in area will provide higher accuracy plate recognition.

  .. image:: images/camera_setup/zoom_combined.png
      :scale: 100%
      :alt: Image of properly zoomed image

Camera resolution is another way to increase the pixels for each plate.  However, increasing the number of pixels also increases the duration of processing time.  Therefore, if CPU resources are not infinite, increasing the resolution too much may adversely impact accuracy.  We recommend setting the camera resolution no higher than 720p, while making sure that plates still have enough pixels to be detected.  If you are providing sufficient zoom, further decreasing the resolution may, counterintuitively, improve accuracy.

For USA plates, OpenALPR reads plates that are larger than 75 pixels wide.  For European plates the minimum is 90 pixels.  Additional pixel width beyond 250 pixels does not provide a noticeable accuracy benefit.

Conclusion
============

Following this guide will optimize your results with OpenALPR.  Sign-up today and try it out on your cameras.  If you have any questions or need help reviewing your configuration for possible improvements, don't hesitate to `Let us know! <http://www.openalpr.com/contact.html>`_