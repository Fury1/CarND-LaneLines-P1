## Project Lane Lines

---

**Finding Lane Lines on the Road**

The goals / steps of this project were the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

---

**Reflection:**

My first project was to make a rough pipeline for a lane line detection algorithm.  

My solution consisted of several steps to massage each image/frame to extract lane lines.

To start, I blurred the image slightly to help reduce noise. I found that when I didn't blur the image, I would detect small irrelevant pixels that would then filter down into the edge detection. By blurring the image slightly, that easily got rid of any image noise and helped me define my relevant edges like line lanes more clearly.

Once the image was blurred, I then used color selection with a focus on whites and yellows to extract lane line markers. I found that using color selection very clearly gave me the yellow and white lines with defined edges against a black mask. This step proved to be important for me because it vastly enhanced my edge detection process which was an issue at times before I used color selection.

![alt text][image1]
[image1]: ./image_filtering/colorselect_whiteCarLaneSwitch.jpg "Color Select"

Following my color selection, I converted the image to gray scale to further enhance contrast. The goal was to get the most contrast between the lane lines and the road.

[image2]: ./image_filtering/gray_whiteCarLaneSwitch.jpg "Gray Scale"
![alt text][image2]

Next, I applied canny edge detection to find my lane line edges. With the contrast of the black road and the highlighted lane lines, I was able to collect very accurate edge points without to much effort tuning thresholds. In addition, there were very few irrelevant points in the areas of interest to me.

[image3]: ./image_filtering/cannygray_whiteCarLaneSwitch.jpg "Canny Edges"
![alt text][image3]

With the extracted points now in play, I could define a specific area of interest and mask off any of areas that would not be used. I defined a trapezoid that extracted my line lines as far out as the image allowed. Because the camera was mounted at a fixed point on the vehicle, I could consistently get my region of interest.

[image4]: ./image_filtering/regionofinterest_whiteCarLaneSwitch.jpg "Region of Interest"
![alt text][image4]

At this point I had a very clearly defined set of points that represent lane lines. From here I input my points into 'Hough Space'.
This proved to be one of more time intensive task because it took a while to get the right parameters tuned correctly. I don't believe there is one set of parameters that is perfect for everything. From the experience I have, I found that if you change anything ahead of your hough transform it can vastly effect it, thus requiring a re-tune of your hough algorithm. It took me a while, but in the end I found a set of parameters that worked well and accurately detected the lines.

[image5]: ./image_filtering/houghspaceraw_whiteCarLaneSwitch.jpg "Hough Lines Raw Output"
![alt text][image5]

Aside from detecting the lines, it was also required to write a new way to draw the actual lines. OpenCV's HoughLinesP returns more then just the two lines desired. When the lines were first drawn with the raw output, there was the appearance lane detection, however, it was many small lines that added up to almost look like one line on each side of the road. Because of this, it was required to average all of the x1,y1,x2,y2 coordinates returns into only one set for the left and right lines. It was also very import to calculate the slope for three reasons:
* Separation of right and left lines
* Calculation of an average slope for left and right lines
* With the averaged slopes and lines it allowed for the calculation of new coordinates based on the old detected coordinates

The last bullet is important because we could specify new starting and ending points for the lines (y1,y2) while solving for their new horizontal points (x1,x2). Ideally, the lines should start at the bottom of the image and travel to around the half way point of the image. Using the averaged line points and slope were the only way to accurately project these values and draw the lines in areas where the lane lines are hashed for example.

[image6]: ./image_filtering/houghspacefinal_whiteCarLaneSwitch.jpg "Hough Lines Final"
![alt text][image6]

At this point we have successfully detected and extrapolated lane lines so the last step is to just overlay the lines on to a copy of the original image.

[image7]: ./test_images/PROCESSED_whiteCarLaneSwitch.jpg "Hough Lines Final"
![alt text][image7]

That completes the filtering of the image for lane lines.

---

**Possible Problems:**

The main issue with this pipeline to detect lines is that even though it works well in this instance, its only scratching the surface. This is nothing more then a starting point for the following reasons:

* It is a perfect picture, on a perfect day, with perfect lines. In the real world there are often lines missing in the road, or your view is obstructed etc

* The road is not always black and the lines are not always yellow and white. A shift in the color spectrum on the road would prove to be an issue with the way I am singling out colors

* I have not accounted for shadows on the road, this again could effect the colors I am singling out

---

**Future Improvments:**

I would like to make the algorithm more robust to handle things like changes in weather, road conditions, obstructed views, shadows and the like.

---

**Conclusion:**

In conclusion, I feel this is a good starting point and there were many lessons learned. However, my eyes have been opened to how complicated autonomy can be and I see that there is a lot to be learned and improved upon.
