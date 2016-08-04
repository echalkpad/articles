# Algorithm turns smartphones into 3-D scanners

[Original URL](http://www.kurzweilai.net/algorithm-turns-smartphones-into-3-d-scanners)

> Structured light 3-D scanning normally requires a projector and camera to be synchronized. A new technique eliminates the need for synchronization, which makes it possible to do structured light...

[![](http://www.kurzweilai.net/images/Unsynchronized-Structured-Light.jpg "Unsynchronized Structured Light")](http://www.kurzweilai.net/images/Unsynchronized-Structured-Light.jpg) Structured light 3-D scanning normally requires a projector and camera to be synchronized. A new technique eliminates the need for synchronization, which makes it possible to do structured light scanning with a smartphone. (credit: Taubin Lab/Brown University)

An algorithm developed by [Brown University](http://www.brown.edu/) researchers my help bring high-quality 3-D depth-scanning capability to standard commercial digital cameras and smartphones.

"The 3-D scanners on the market today are either very expensive or unable to do high-resolution image capture, so they can't be used for applications where details are important," said [Gabriel Taubin](http://mesh.brown.edu/taubin/), a professor in Brown's School of Engineering -- like 3-D printing.

Most of the high-quality 3-D scanners capture images using a technique known as [structured light](https://en.wikipedia.org/wiki/Structured_light). A projector casts a series of light patterns on an object, while a camera captures images of the object. The way these patterns deform when striking surfaces allows the [structured-light 3-D scanner](https://en.wikipedia.org/wiki/Structured-light_3D_scanner) to calculate the depth and surface configurations of the objects in the scene, creating a 3-D image.

**No sync required**

The limitation with current 3-D depth scanners is that the pattern projector and the camera have to precisely synchronized, which requires specialized and expensive hardware.

The problem in trying to capture 3-D images _without_ synchronization is that the projector could switch from one pattern to the next while the image is in the process of being exposed. As a result, the captured images are mixtures of two or more patterns. A second problem is that most modern digital cameras use a rolling shutter mechanism. Rather than capturing the whole image in one snapshot, cameras scan the field either vertically or horizontally, sending the image to the camera's memory one pixel row at a time. As a result, parts of the image are captured a slightly different times, which also can lead to mixed patterns.

**The fix**

The algorithm Taubin and his students have developed enables the structured light technique to be done _without_ synchronization between projector and camera. That means an off-the-shelf camera can be used with an untethered (unconnected by a wire) structured light flash. The camera just needs to have the ability to capture uncompressed images in burst mode (several successive frames per second), which many DSLR cameras and smartphones can do.

After the camera captures a burst of images, the algorithm calibrates the timing of the image sequence using the binary information embedded in the projected pattern. Then it goes through the images, pixel by pixel, to assemble a new sequence of images that captures each pattern in its entirety. Once the complete pattern images are assembled, a standard structured light 3D reconstruction algorithm can be used to create a single 3-D image of the object or space.

The researchers presented a paper describing the algorithm last month at the SIGGRAPH Asia computer graphics conference. In their paper, the researchers showed that the technique works just as well as synchronized structured light systems. During testing, the researchers used a fairly standard structured light projector, but the team envisions working to develop a structured light flash that could eventually be used as an attachment to any camera.

Northwestern University engineers have developed another inexpensive solution to the problem (see [A fast, high-quality, inexpensive 3-D camera](http://www.kurzweilai.net/team-develops-faster-higher-quality-3-d-camera)), but it uses a proprietary 3-D capture camera instead of an existing smartphone.

**Abstract of _Unsynchronized structured light_**

Various Structured Light (SL) methods are used to capture 3D range images, where a number of binary or continuous light patterns are sequentially projected onto a scene of interest, while a digital camera captures images of the illuminated scene. All existing SL methods require the projector and camera to be hardware or software synchronized, with one image captured per projected pattern. A 3D range image is computed from the captured images. The two synchronization methods have disadvantages, which limit the use of SL methods to niche industrial and low quality consumer applications. Unsynchronized Structured Light (USL) is a novel SL method which does not require synchronization of pattern projection and image capture. The light patterns are projected and the images are captured independently, at constant, but possibly different, frame rates. USL synthesizes new binary images as would be decoded from the images captured by a camera synchronized to the projector, reducing the subsequent computation to standard SL. USL works both with global and rolling shutter cameras. USL enables most burst-mode-capable cameras, such as modern smartphones, tablets, DSLRs, and point-and-shoots, to function as high quality 3D snapshot cameras. Beyond the software, which can run in the devices, a separate SL Flash, able to project the sequence of patterns cyclically, during the acquisition time, is needed to enable the functionality.
