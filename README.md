# Finding-Lane-Lines-on-the-Road
Detected highway lane lines on a video stream. Used OpencV image analysis techniques to identify lines, including Hough Transforms and Canny edge detection.

Finding Lane Lines on the Road

The goals / steps of this project are the following:

To make a pipeline that finds lane lines on the road

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/2ULM1XtvJBc/0.jpg)](https://www.youtube.com/watch?v=2ULM1XtvJBc)



#### My pipeline consists six steps: 1. Create image mask 2. Apply gaussian blur using adaptive thresholding 3. Apply canny edge detection 4. Define the region of interest 5. Apply hough line transform and create lines 6. Combine the created lane lines and the original image

##### Create Image Maks 
At first, input image was only transformed in grayscale to detect the lanes. Canny edge detector works better in a single channel with a pixel range of 0 to 255. From there on, the following steps were applied to produce the result. This approach worked fairly well with both the white and yellow lane lines. However for the optional challenge video, the lane line detection failed in some frames with shadow casted onto the lane lines. It seemed like canny edge detector failed to distinguish the edges of the lane lines from the road under dark conditions. Change to the pipeline was needed and it took place in the very first step. Instead of processing the entire image in grayscale, color masks were used. This pipeline changes the input image into grayscale to detect white lane lines. Once the image has been transformed into grayscale, a color mask is produced using cv2.inRange. As for the yellow lane lines, instead of using the grayscale, HSV (Hue, Saturation, Value) is used because it is easier to detect yellow than in RGB. When the two color masks are created they are then combined, using bitwise or, to create one color mask for both white and yellow. This final color mask is added onto the original image in grayscale, using bitwise and, to apply the second step of the pipeline.

##### Apply Gaussian Blur 
Using Adaptive Thresholding The grayscale image with the color mask goes through the gaussian blur to reduce noise by focusing only the strongly contrasing boundaries when the third step takes place. Since image data is prone to change in conditions (shadow, strong exposure to sunlight, etc) adaptive thresholding is used in place of global thresholding. This method calculates threshold values for eash small regions of an image, so different threshold values are used to compensate different exposure to light. Source

##### Apply Canny Edge Detection 
Every pixel is processed using gradient values to detect edges in an image. The threshold values are set in 3:1 ratio which is recommended.

##### Define the Region of Interest 
Since only the current lane lines are needed as data, a mask is applied to block out unnecessary edges. The mask is set to be in a trapezoid shape and the vertices are calculated in ration of the input image size.

##### Hough Line Transform and Create Lines 
The edges, the connecting pixels are represend as lines in XY plane when the hough line transformtion takes place. Many changes were made in "draw_lines" method to create the final lines. Since the origin, in terms of XY plane, is located at the top left corner of the image, the lane lines take place in the fourth quadrant. The left and right lane lines seem to have positive slope for left and negative slope for right, but since these lines are in fourth qudrant, the negatives of the slope values must be calculated to match the quadrant(first quadrant). So the left lane line has the negative slope when the right lane line has the positive slope. As the error checking, horizontal lines and lines with slope value less than 1/2 are ignored because they do not seem to be valid lane lines. Left and right lanes are differentiated using the slope value and then averaged to filter out outliers. If the lines lay close to the average these lines are then used to calculate best-fit-line with cv2.fitLine. These best-fit-lines are then averaged for every 4 frames (collections.deque of size 4 is used to collect data used to calculate the average). Finally, using y = mx + b, the averaged best-fit-line is drawn on to the input image.

##### Combining the result and the original image The produced line image is added onto the original image using weighted_img method to produce semi-transparent lines

### 2. Potential shortcomings with my current pipeline 
One very obvious shortcoming would be for lane lines with different colors other than white and yellow. Since this pipeline only masks white and yellow colors to detect lane lines, if a lane line color is not white or yellow, lane line detection may fail. Another shortcoming would be for a curvy lane lines. The best-fit-line produced from this pipeline is a linear line, so for curvy lane lines, the best-fit-lines produced may not be appropriate. The region of interest is fixedb based on the input image size. But there may be occasions where the region of interest need to change (when there is incline/decline in the road)

### 3. Possible improvements to my pipeline 
The processing speed of this pipeline may be improved. To go through every step of this pipleline on the fly may take too much time in some occasions. Maybe the method of taking average fo calculated lines need to change. Improvement in setting vertices of the region of interest is needed for the shortcoming mentioned above. Changing the method of taking the best-fit-line (not always linear?) may be needed for different road conditions.
