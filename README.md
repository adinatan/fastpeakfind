[![View Fast 2D peak finder on File Exchange](https://www.mathworks.com/matlabcentral/images/matlab-file-exchange.svg)](https://www.mathworks.com/matlabcentral/fileexchange/37388-fast-2d-peak-finder)

# fastpeakfind

A simple and fast 2D peak finder. The aim was to be faster than more sophisticated techniques yet good enough to find peaks in noisy data. The code analyzes noisy 2D images and find peaks using robust local maxima finder (1 pixel resolution) or by weighted centroids (sub-pixel resolution). The code is designed to be as fast as possible, so I kept it pretty basic. It best works when using uint16 \ uint8 images, and assumes that peaks are relatively sparse.

The code requires Matlab's Image Processing Toolbox, and can be used inside parfor for faster processing times.


please cite As:


Natan, Adi. "Fast 2D peak finder." MATLAB Central File Exchange. Available at https://www.mathworks.com/matlabcentral/fileexchange/37388-fast-2d-peak-finder (2021)



How the code works:
In theory, each peak is a smooth point spread function (SPF), like a Gaussian of some size, etc. In reality, there is always noise, such as
"salt and pepper" noise, which typically has a 1-pixel variation.  Because the peak's PSF is assumed to be larger than 1 pixel, the "true"
local maximum of that PSF can be obtained if we can get rid of these single-pixel noise variations. There comes medfilt2, which is a 2D median
filter that gets rid of "salt and pepper" noise. Next we "smooth" the image using conv2, so that with high probability there will be only one
pixel in each peak that will correspond to the "true" PSF local maximum. The weighted centroid approach uses the same image processing, with the
difference that it just calculated the weighted centroid of each connected object that was obtained following the image processing.  While
this gives sub-pixel resolution, it can overlook peaks that are very close to each other, and runs slightly slower. Read more about how to treat these
cases in the relevant code comments.


  Inputs:
  
* d  -    The 2D data raw image - assumes a Double\Single-precision floating-point, uint8 or unit16 array. Please note that the code
         casts the raw image to uint16 if needed.  If the image dynamic range is between 0 and 1, I multiplied to fit uint16. This might not be
         optimal for generic use, so modify according to your needs.
* thres -    A number between 0 and max(raw_image(:)) to remove  background
* filt -     A filter matrix used to smooth the image. The filter size should correspond the characteristic size of the peaks
* edg -       A number>1 for skipping the first few and the last few 'edge' pixels
* res -       A handle that switches between two peak finding methods:  1 - the local maxima method (default). 2 - the weighted centroid sub-pixel resolution method.
         Note that the latter method takes ~20% more time on average.
* fid -      In case the user would like to save the peak positions to a file, the code assumes a "fid = fopen([filename], 'w+');" line in the
        script that uses this function.

Optional Outputs:

 * cent  -        a 1xN vector of coordinates of peaks (x1,y1,x2,y2,...
 * [cent cm]  -   in addition to cent, cm is a binary matrix  of size(d) with 1's for peak positions. (not supported in the weighted centroid sub-pixel resolution method)

Example:

>   p=FastPeakFind(image);
>   
>   imagesc(image); hold on
>   
>   plot(p(1:2:end),p(2:2:end),'r+')

   ![Fig1](https://github.com/adinatan/fastpeakfind/blob/main/fpf.png)

