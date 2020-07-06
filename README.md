## Advanced Lane Finding
<img src="output_images/video_out_curve.jpg" width="960" height="480" alt="video_out_curve" />

In this project, our goal is to write a software pipeline to identify the lane boundaries in a video.


Pipeline
---
My pipeline mainly consist of following functionalities which are distributed across different classes

* Camera Calibration
First we need to do camera calibration with known images which has object whose physical geometry is known to us. <br />
Camera calibration gives us Calibration matrix and distiortion coefficients which are used later to remove the distortion in an image

* Start with image or frame from video clip
<img src="test_images/straight_lines1.jpg" width="480" alt="straight_lines1" />

* Correct Distortion <br />
Distortion is corrected using the calibration matrix found in above step along with distortion coefficients. 
<img src="output_images/Undistorted_Image_straight_lines1.jpg" width="480" alt="Undistorted_Image_straight_lines1" />

* Color and Gradient Thresholding <br />
To find the lane lines correctly, we first need to understand what characteristics of an image can be used to correctly
extract lane lines. Here, I have used HSV color space, since yellow lines are clearly visible in S channel. The extracted S <br />
channel image is then thresholded with predefined threshold values to get binary image. For the computation of x and y gradients <br />
I used the L channel from the input image. I have used Sobel operator with Kernel size of 3 to get the gradients.<br />
To threshold the gradient image I have come up with a function that automatically calculates lower and higher thresholds for magnitude <br />
instead of using predefined fix values. The function takes the histogram of magnitude of gradients and finds thresholds. At the end <br />
gradient direction threshold is applied to get lines which have required orientation. <br />
At the end both color and gradient binaries are combined to get final thresholded binary image. 
<img src="output_images/Binary_Thresholded_Image_straight_lines1.jpg" width="480" alt="Binary_Thresholded_Image_straight_lines1" />

* Perspective Transform <br />
Binary threholded image is then warped to get the bird's eye view which makes us to see the lane lines correctly. If lane lines <br />
are parallel in the scene then they remain parallel after applying perspective transform. For the perspective transform we need to <br />
select four source points as a trapezoid to map to the corresponding points in the destination image. I have manually found the source and
destinations points by trial and error method. These points are then used to get the trasformation matrices to do warping and de-warping. 
<img src="output_images/Binary_Warped_Image_straight_lines1.jpg" width="480" alt="Binary_Warped_Image_straight_lines1" />
<img src="output_images/Color_Warped_Image_straight_lines1.jpg" width="480" alt="Color_Warped_Image_straight_lines1" />


* Finding Lane Lines <br />
Here comes the most tedius part of the project. The warped binary image is used for finding the lane lines. First I get rid of the area <br />
of image which I don't need for finding lanes lines. I then take the histogram of bottom half of the image. Peaks in the histogram give <br />
me the starting point for the left and right lane lines. A sliding window search is then performed to find all pixels which belong to lane <br />
lines. I then fit the polynomial on the lane lines pixels I have found. This operation is performed only on the first image. For the subsequent <br />
frames I serach for lanes pixels around already found lane lines. This makes the algorithm more efficient. Once the polynomial is fit, an interpolation
is used to cover all the points between two lane lines. 
<img src="output_images/Sliding_Window_Image_straight_lines1.jpg" width="480" alt="Sliding_Window_Image_straight_lines1" />


* Calcualte Radius of Curvature and Center Distance <br />
Furthermore, the radius of curvature for both the lane lines is calcualted along with the center distance. Pixel to meter ratios are <br /> 
used to get the real world radius of curvature. 

* Final Output <br />
Finally, I unwarped the image and then a final weighted image is created with overlays drawn on it along with the texts for radius <br />
of curvature and center distance. 
<img src="output_images/Final_De_warped_Image_straight_lines1.jpg" width="480" alt="Final_De_warped_Image_straight_lines1" />

Outcome
---
The proposed solution works best with test images and project video. Needless to say that it failed miserably on challenge video. <br />
One potential shortcoming with this approch is, the algorithm does not work well when there are many shadows on the road and lanes lines are faded. <br />
It also doesn't work well when there are false edges on the roads such as those created due to construction. 

Possible Improvement
---
There is a much scope to improve this algorithm. First thing would be to investigate other color spaces which do good job on specific types of lane <br />
lines. Secondly, the algorithm needs to have a mechanism to automatically switch to different color spaces based upon some detection. 
And last but not the least,for the source points which are needed for perspective transform are currently hard coded, I have tried some feature detectors <br />
like Harris Corner Detector and SURF to get those points during run time and we won't need to hard code them. But, it did not work as I expected.
It needs further investigation. 

The result can be seen in this [Video](https://github.com/akashchavan15/CarND-Advanced-Lane-Lines/blob/master/output_images/project_video.mp4)
