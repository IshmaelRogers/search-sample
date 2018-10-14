## Project: Search and Sample Return
##Ishmael Rogers

Project 1 Notes: 

Apply color threshold and perspective transform to identify navigable terrain
Update map with identified obstacles and navigable terrain 
Create a map of the navigable terrain and newly identified rock samples

Overall goal autonomous mapping 

Settings 

Screen resolution 1024 x 768 
Graphics quality: good 

Unity was chosen for this project because of its higher quality photorealistic images; which is perfect for computer vision applications 

The telemetry data for this project is in the form of images from a forward facing camera. Output images are 320 x 160 pixels 

Secondary telemetry data includes rover’s position, throttle, brake, steering angle, speed and roll, pitch yaw.

The perspective transform is completed as followed:

Define 4 source points
Define 4 destination points (listed in the same order as source points)
Use cv2.getPerspectiveTransform() to get the M, the transform matrix
Use cv2.warpPerspective() to apply M and warp the image to a top-down view 

Converting to Rover Centric Coordinates

In this step we are essentially identifying a coordinate system that is fixed with respect to the robot. This allows us to describe the position of objects in the environment with respect to the robot’s camera. 

From here we will extract the pixel positions of all navigable terrain pixels and transform those values to “rover-centric” coordinates (i.e a coordinate frame where the rover camera is at (x,y) =(0,0)

Map 2 World Coordinates

The goal in this portion of the project is to write code that will use the rover’s position, orientation and camera image to map its environment and compare against the ground truth map provided.

Now we must map the navigable terrain points in rover-centric coordinates to points in the world coordinates 

Concepts: 

Rotation: required to account for the fact that when the camera takes a picture the rover can be pointing in any arbitrary direction given by the yaw angle.  
Translation: required to account for the fact that the rover may be located at any position in the world when it takes a picture. 

The 2 step process

Rotate the rover-centric coordinates so that the x and y airs are parallel to the axes in the world space. 
Apply a rotation matrix to te rover space pixel values called Xpix and Ypix 
[x’ , y’] ‘ = [ [cos(teta), -sin(theta)], [sin(theta), cos(theta)]] *  [x, y] 
Code implementation
See Map2world.py 
Translate the rotated positions by the x and y position values given by the rover’s position vector in the world. 
Add the x and y components of the rover’s position to the x_rotated and y_rotated values. 
Keep in mind scaling If, for example, as suggested in the previous exercise, you have mapped pixels in rover space such that each represents 0.1 x 0.1 m, and in your world map (as will be the case in the project) each pixel is 1 x 1 m, then you need to divide your rover space pixel values by 10

Decision making:

Decisions are made based on the perception of the robot. Ow to decided on a steering angle. 

Average angle
Determine te steering angle by choosing the direction wit the clearest path aka the most navigable terrain pixels.
Steps:

Convert (x,y) pixel positions to polar coordinates where each pixel position is represented by its distance forte origin and angle counterclockwise from the positive x-direction polar_coords.py
Choose a conversion between average angle and the steering angle to send to the rover. (Con vert to degrees and clip to the range +/- 15
 avg_angle_degrees = avg_angle * 180/np.pi
steering = np.clip(avg_angle_degrees, -15, 15)
	
Considerations:

For this project the field of view of the camera gives the average values for angle that we will get vary between +/- pi/4 radians. 
The input for the steering angles that can be sent to the rover is in degrees over the range -15 (to the right) and +15 (to the left)


[//]: # (Image References)

[image1]: ./misc/rover_image.jpg


**Results**

![alt text][image1]

The Rover is good at following the left wall and geeting unstuck. Depending on a few variables, the rover can collect the majority of rock samples. 

**Future Work**



**Return Home**

Future work will include a complete function for returning to the starting point.


**Collect all the sample rocks**

Improvements to the rock collecting function would allow the rover to find a rock, check to see if it has been collected and if not, check to see if the rock is in sight. If it is, move closer to it. If it is not, turn the robot towards the direction of the rock.

**PID control**

Implementing a PID controller was difficult without robot parameters. Maybe I can inquire about the parameters seperately and implement in a future assignment. 
