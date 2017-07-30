#**Finding Lane Lines on the Road** 

The goal of the project is to create an image processing pipeline to identify lane lines on a road. To start with , we will identify white and yellow lane lines in a series of image. Then we will expand it to identify lane lines in a video (a collection of images). We will then reflect on the work on a short writeup.


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

###1. Description of the pipeline. 

The pipeline consisted of 4 steps. 

First, we blur the image with gaussian_blur.

Then we apply Canny edge detection to detect the sharp changes of colors in the blurred image. We tune the low and high threshold parameters of canny
```
low_threshold = 200
high_threshold = 300
edges = canny(blur_gray, low_threshold, high_threshold)
```    
Then we crop out the area of interest 

```
x = edges.shape[0]
y = edges.shape[1]
vertices = np.array( [[[3*x/4, 3*y/5],[x/4, 3*y/5],[40, y],[x - 40, y]]], dtype=np.int32 )
masked_edges = region_of_interest(edges, vertices)
```
Then we apply the hough line detection logic. We tune the parameters for hough line detection

```
rho = 3
theta = np.pi/180
threshold = 40
min_line_length = 30
max_line_gap = 200
```
Then we draw lines on the lane lines of the original image

```
result = weighted_img(line_img, image)
fig = plt.figure(figsize=(6,10))
```

##Updating the draw_line() function

We find the slope and length of the hypotenous of the line. The slope determines whether we are operating on the left or right line of the lane. We draw an imaginary line in the bottom and the center of the image and find the intersection of our left and right lines with those imaginary lines.

```
upLeftPoint = seg_intersect(upLinePoint1,upLinePoint2, p3,p4)
downLeftPoint = seg_intersect(downLinePoint1,downLinePoint2, p3,p4)
```
We then find out the average position of the left and right line over multiple image frames and draw lines

```
x1 = int(avgx1)
y1 = int(avgy1)
x2 = int(avgx2)
y2 = int(avgy2)
cv2.line(img, (x1, y1), (x2, y2), [255,255,255], 12)
 ```

###2. Potential shortcomings

The main shortcoming is that the accuracy of detecting the exact lane lines are less than 100%. We tuned some parameters provided in the sample code but the interraltion of those parameter was not really clear to me. 

Another shortcoming is the way we improved the draw_line() function. We are averaging out the intersection of left and right lines with an imaginary line in the center and the bottom of the image. This simple averaging technique also leads to less than 100% accuracy in lane detection specially in the video. This technique will be specially problematic in detecting lanes in hilly and curvy roads. 


###3. Possible improvements

One improvement would be to find out a better way of improving the draw_line() function rather than simply averaging out the intersections.
