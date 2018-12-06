Poisson Blending
================

## Toy Problem

To perform Poisson Blending, we proceeded with the Toy Problem to restore the original image from zero image with minimal gradient from the original image.
To obtain an image satisfying all three equations, we wrote the source code as shown below.
Denote the intensity of the source image at (x,y) as s(x,y), and the values of the image to solve for as v(x,y). 

![image](https://user-images.githubusercontent.com/44015662/47510708-a2fc7600-d8b3-11e8-8f29-a19b82ea8faa.png)


``` 
I = double(imread('toy_problem.png'));
[imh, imw, nn] = size(I);
 
Im2var = zeros(imh,imw);
Im2var(1:imh*imw) = 1:imh*imw;
 
A = sparse([],[],[],1+(imw-1)*imh+(imh-1)*imw,imw*imh);
 
b(1) = I(1,1);
A(1,:) = zeros(1,imh*imw);
A(1,1) = 1;
 
for k=1:(imw-1)*imh+(imh-1)*imw
    k
    if k < (imw-1)*imh + 1
        
        y = floor(1+(k-1)/imw);
        x = 1+mod(k-1,imw-1);
        
        A(k+1,Im2var(y,x+1)) = 1;
        A(k+1,Im2var(y,x)) = -1;
        b(k+1) = I(y,x+1) - I(y,x);
    else
        k_temp = k-imh*(imw-1);
        y = 1+mod(k_temp-1,imh-1);
        x = floor(1+(k_temp-1)/imh);
        
        A(k+1,Im2var(y+1,x)) = 1;
        A(k+1,Im2var(y,x)) = -1;
        b(k+1) = I(y+1,x) - I(y,x);
    end
end
 
v = A\b';
v = reshape(v,[119,110]);
imshow(v,[])
```

Since Matrix A is sparse, we use MATLAB’s sparse function to initialize it so that the \ operator can run fast. The results of the Toy Problem is as follows. From the left are the original image, the output image and the error image of the two images.

![image](https://user-images.githubusercontent.com/44015662/47510922-169e8300-d8b4-11e8-972f-03818b34ad5d.png) ![image](https://user-images.githubusercontent.com/44015662/47510927-1900dd00-d8b4-11e8-95b0-645af0ba5884.png) ![image](https://user-images.githubusercontent.com/44015662/47510933-1bfbcd80-d8b4-11e8-8d12-be44f989050e.png)

From the results, we verified that the Matrix for finding the x-gradient and y-gradient of the image works well.

## Poisson Blending with Mixed Gradients

Unlike the Toy problem above, Poisson blending had to solve the least squares problem.

![image](https://user-images.githubusercontent.com/44015662/47511048-54031080-d8b4-11e8-88cd-ddb095b4e779.png)

The second term of the equation is an expression for the masked boundary. However, in my source code, I excluded this term because the boundary portion became awkward with the image from the first term. 
The background image and input image to be used for Poisson blending are shown below.

![image](https://user-images.githubusercontent.com/44015662/47511086-6ed58500-d8b4-11e8-8cc2-eeb9047e4f7b.png)

![image](https://user-images.githubusercontent.com/44015662/47511094-7301a280-d8b4-11e8-8aff-22cde9a25d44.png) ![image](https://user-images.githubusercontent.com/44015662/47511098-7432cf80-d8b4-11e8-9ba0-b7247dda09e8.png)

In addition, to obtain a more natural image, we used a technique of mixing the input image with the gradient of the background image. In Equation above, Av = b, the elements of vector b are put into conditional as follows.

![image](https://user-images.githubusercontent.com/44015662/47511214-b65c1100-d8b4-11e8-8eb6-baae25561caa.png)
![image](https://user-images.githubusercontent.com/44015662/47511221-b825d480-d8b4-11e8-8b12-64c205fda955.png)

The results of Poisson blending are as follows.

![image](https://user-images.githubusercontent.com/44015662/47511222-b9ef9800-d8b4-11e8-9ec5-bc004cb9fd52.png)

Input image (left), Poisson Blended image (right)

![image](https://user-images.githubusercontent.com/44015662/47511227-bc51f200-d8b4-11e8-8dc3-b38cdd8bfa78.png)
![image](https://user-images.githubusercontent.com/44015662/47511235-bf4ce280-d8b4-11e8-8265-33319fb854ce.png)

For some results, Poisson blending did not work well because the background brightness varies greatly.

![image](https://user-images.githubusercontent.com/44015662/47511242-c3790000-d8b4-11e8-90ba-a2a8d445750f.png)
![image](https://user-images.githubusercontent.com/44015662/47511253-c83db400-d8b4-11e8-8be4-6c3d1b035cbe.png)

In order to solve this case, we blended naturally by histogram matching with surrounding background values.

![image](https://user-images.githubusercontent.com/44015662/47511264-cd026800-d8b4-11e8-807d-bc9fc715b669.png)

However, there were still some points to be supplemented.

## Your Own Examples

![image](https://user-images.githubusercontent.com/44015662/47511269-cffd5880-d8b4-11e8-9aa1-ccdd7445b0e8.png) ![image](https://user-images.githubusercontent.com/44015662/47511273-d25fb280-d8b4-11e8-8323-7d101133c308.png)
![image](https://user-images.githubusercontent.com/44015662/47511278-d7246680-d8b4-11e8-8bf5-87c8bd7140f4.png)![image](https://user-images.githubusercontent.com/44015662/47511292-d986c080-d8b4-11e8-87e3-2c614ead40aa.png)
![image](https://user-images.githubusercontent.com/44015662/47511300-de4b7480-d8b4-11e8-9ceb-1bfc81c81004.png)![image](https://user-images.githubusercontent.com/44015662/47511305-e0153800-d8b4-11e8-992d-6becdaa3f2ca.png)
