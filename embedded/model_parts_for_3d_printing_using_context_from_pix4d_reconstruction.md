# Model Parts for 3D Printing Using Context From Pix4D Reconstruction

[Original URL](http://www.stefangordon.com/3dprint-from-3d-reconstruction/)

> While constructing a new fixed wing drone I needed to design and 3d print a small mount for a GoPro action camera. Typically I would do this by taking a large number of measurements using a...

![The 3D model I used as context for designing my part](http://www.stefangordon.com/wp-content/uploads/2016/01/drone_scan_animated.gif)

While constructing a new fixed wing drone I needed to design and 3d print a small mount for a GoPro action camera. Typically I would do this by taking a large number of measurements using a ruler or calipers, and then designing the part based on those measurements. This time I decided to try a completely different workflow involving photogrammetric 3d reconstruction, which worked wonderfully, and will be my new method for these scenarios going forward.

Here I'll show you how I used my camera, and a great bit of software called Pix4D, to quickly and easily reconstruct a 3D model I could import into OpenSCAD and use as a template for designing my new part quickly and easily.

Here you can see the area in the wing of my plane where the GoPro will sit. It points down through a hole in the wing. This picture is one of about 20 that I quickly snapped with my camera (another GoPro). I took pictures while moving the camera laterally to catch various angles.

![Wing bay with gopro](http://www.stefangordon.com/wp-content/uploads/2016/01/scan_image-768x1024.jpg)

Here are thumbnails from the rest of the photos.

![grid](http://www.stefangordon.com/wp-content/uploads/2016/01/grid-300x294.png)

I then loaded these images into [Pix4D Mapper](https://www.pix4d.com/), an application that performs photogrammetric reconstruction. I configured Pix4D to generate a textured mesh and output it as an OBJ file. I chose a reasonably small triangle and texture size for faster processing.

[![pix4d_settings](http://www.stefangordon.com/wp-content/uploads/2016/01/pix4d_settings-1024x832.png)](http://www.stefangordon.com/wp-content/uploads/2016/01/pix4d_settings.png)

A mesh was generated for me that contained good detail for the area I was interested in. Here is the raw output.

![drone_scan_1](http://www.stefangordon.com/wp-content/uploads/2016/01/drone_scan_1.png)

Next I fired up [MeshLab](http://meshlab.sourceforge.net/), a wonderful free and open source mesh processing tool. In MeshLab I performed three tasks.

1. Crop the mesh to the area I am interested in
2. Run a smoothing filter to remove some of the artifacts from the mesh
3. Run a quadratic decimation filter to reduce the size of the mesh

[![sidebyside](http://www.stefangordon.com/wp-content/uploads/2016/01/sidebyside.png)](http://www.stefangordon.com/wp-content/uploads/2016/01/sidebyside.png)

I then exported the mesh as STL format and brought it into my preferred modelling tool, OpenSCAD. OpenSCAD can import any STL file and it can be used in operations against your model.

![openscad_0](http://www.stefangordon.com/wp-content/uploads/2016/01/openscad_0-1024x785.png)

From here you are ready to model. Keep in mind the scale of the model is probably incorrect, so make a single measurement of something in the model and apply an appropriate scaler. In my case I just adjusted the scale of the imported model until the width of the GoPro was correct.

I built out my model using the imported data as context, so it was easy to know what shape would fit and get it right the first time.

[![openscad_1](http://www.stefangordon.com/wp-content/uploads/2016/01/openscad_1-1024x603.png)](http://www.stefangordon.com/wp-content/uploads/2016/01/openscad_1.png)

My design was very simple, but it did have several beveled corners to fit in the cavity, and fit perfectly the first time. It's designed to hold a rubber band over the GoPro for easy removal.

End to end this process took under an hour, so I look forward to using it in the future for more complex projects.

[![final_gopro](http://www.stefangordon.com/wp-content/uploads/2016/01/IMG_20151221_140036-1024x768.jpg)](http://www.stefangordon.com/wp-content/uploads/2016/01/IMG_20151221_140036.jpg)
