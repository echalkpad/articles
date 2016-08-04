# Unity 2D Flappy Bird Tutorial

[Original URL](http://noobtuts.com/unity/2d-flappy-bird-game)

> Foreword In this Tutorial we will learn how easy it is to make a game like Flappy Bird in Unity. Flappy Bird was released in 2013 and became the most downloaded iOS App in January 2014\. The...

![Unity 2D Flappy Bird Game](http://noobtuts.com/content/unity/2d-flappy-bird-game/unity_flappy_bird.png "unity_flappy_bird")

## Foreword

In this Tutorial we will learn how easy it is to make a game like Flappy Bird in Unity. [Flappy Bird](http://en.wikipedia.org/wiki/Flappy_Bird) was released in 2013 and became the most downloaded iOS App in January 2014.

The gamedesign is very simple: a bird is supposed to fly horizontally between obstacles, and the player can press a button to make the bird flap and fly upwards. Simple games like that are always perfect for a Tutorial, all we need are 40 lines of code and a few sprites.

As usual, everything will be explained as easy as possible so everyone can understand it.

## Requirements

### Knowledge

Our Tutorial does not require any special skills. If you know your way around Unity and heard about [GameObjects](http://noobtuts.com/unity/gameobjects-and-components) and Transforms before, then you are ready to go. And if you didn't, don't worry about it too much.

Feel free to read our easier [Unity Tutorials](http://noobtuts.com/unity) like [Unity 2D Pong Game](http://noobtuts.com/unity/2d-pong-game) to get used to the engine.

### Unity Version

Our Flappy Bird Tutorial will use **Unity 5.0.0f4**. Newer versions should work fine as well, older versions may or may not work. The free version of Unity 5 now comes with all the engine features, which makes it the recommended version.

## Project Setup

Let's get to it. We will start Unity and select **New Project**:<br>
![Unity New Project](http://noobtuts.com/content/unity/2d-flappy-bird-game/new_project.png "new_project")

We will name it **flappybird**, select any location like **C:\**, select **2D** and click **Create Project**:<br>
![Unity Create new 2D Project](http://noobtuts.com/content/unity/2d-flappy-bird-game/project_wizard_create_2d.png "project_wizard_create_2d")

If we select the **Main Camera** in the **Hierarchy** then we can set the **Background Color** to a light blue _(R=198, G=208, B=230)_ for the sky color and adjust the **Size** like shown in the following image:<br>
![Camera Properties](http://noobtuts.com/content/unity/2d-flappy-bird-game/camera_properties.png "camera_properties")

## The Background

We will begin by drawing a very simple sky background in our drawing tool of choice:<br>
![Flappy Bird Background](http://noobtuts.com/content/unity/2d-flappy-bird-game/background.png "background")<br>
_Note: right click on the image, select **Save As...**, navigate to the project's **Assets** folder and save it in a new **Sprites** folder._

After saving it, we can select the background in the **Project Area**:<br>
![Background in Project Area](http://noobtuts.com/content/unity/2d-flappy-bird-game/background_projectarea.png "background_projectarea")

And then modify the **Import Settings** in the **Inspector**:<br>
![Background Import Settings](http://noobtuts.com/content/unity/2d-flappy-bird-game/background_importsettings.png "background_importsettings")<br>
_Note: a **Pixels Per Unit** value of **16** means that 16 x 16 pixels will fit into one unit in the game world. We will use this value for all our textures, because the Bird will be 16 x 16 pixels, which should end up being 1 unit in the game world._

Alright so let's add the background to the game world by dragging it from the **Project Area** onto the **Main Camera** in the **Hierarchy**:<br>
![Background to Hierarchy](http://noobtuts.com/content/unity/2d-flappy-bird-game/background_to_hierarchy.png "background_to_hierarchy")

So that the background becomes a **child** of the **Main Camera**:<br>
![Background as Child of Camera](http://noobtuts.com/content/unity/2d-flappy-bird-game/background_under_camera.png "background_under_camera")

Now that the background is a child of the camera, it will always go where the camera goes. Or in other words, the player will always see the background.<br>
_Note: we could also place several backgrounds next to each other so that there is still a background when the camera moves horizontally, but making it a child of the camera is much easier._

Let's take a look at the **Inspector** and position the background one unit lower at **Y=-1**, just so that it fits to the rest of the game later on:<br>
![Background position](http://noobtuts.com/content/unity/2d-flappy-bird-game/background_position.png "background_position")

If we press **Play** then we can already see the background sky:<br>
![Background in Scene](http://noobtuts.com/content/unity/2d-flappy-bird-game/background_inscene.png "background_inscene")

There is one more adjustment to be made here. We will add the bird and some obstacles soon, so let's also make sure that the background is really drawn behind everything else. Unity uses the SpriteRenderer's **Sorting Layer** and **Order in Layer** properties to decide which parts of a game should be in front of which other parts.

We will simply set the **Order in Layer** to **-1** so that everything else will be drawn in front of it:<br>
![Background Order in Layer](http://noobtuts.com/content/unity/2d-flappy-bird-game/background_order_in_layer.png "background_order_in_layer")<br>
_Note: the higher the order, the more it is in the foreground. The lower the order, the more it is in the background._

## The Ground

Let's also draw some kind of terrain for the ground. We will make it very wide so that there is enough space for our obstacles later:<br>
![Flappy Bird Ground](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground.png "ground")<br>
_Note: right click on the image, select **Save As...** and save it in the project's **Assets/Sprites** folder._

We will select the ground in the **Project Area** and then assign the same **Import Settings** that we used before:<br>
![Ground Import Settings](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground_importsettings.png "ground_importsettings")

Afterwards we will drag it from the **Project Area** into the **Hierarchy** again:<br>
![Add Ground to Hierarchy](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground_to_hierarchy.png "ground_to_hierarchy")<br>
_Note: this time we won't make it a child of the camera._

Let's take a look at the **Inspector** and position the ground at **X=16** **Y=-6**: so that it is below the background and so that most of the area is at the right of the screen:<br>
![Ground position](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground_position.png "ground_position")

This time we will select a **Order in Layer** value of **1**, so that it will always be in front of the bird and the obstacles later on:<br>
![Ground Order in Layer](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground_order_in_layer.png "ground_order_in_layer")

### Ground Physics

The ground should the part of the physics world. Right now it is really just an image in the game world, just a visual effect and nothing more. We want the ground to be like a wall that the bird can collide with, so let's select **Add Component**->**Physics 2D**->**Box Collider 2D** in the **Inspector**:<br>
![Ground Collider](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground_collider.png "ground_collider")

Normally we would be done now, but there is one more adjustment to be made here. Later on we will add obstacles to our game _(like the green pipes in the original Flappy Bird game)_, and those obstacles will move upwards and downwards into the ground. The obstacles and the ground will both be part of the physics world, and according to the laws of physics, there can never be two objects at the same place _(or in our case, two Colliders)_.

There are several ways to work around this. As usual we will choose the easiest way by creating a new physics **Layer** that we will use for the ground and the obstacles. Afterwards we will tell Unity to simply ignore collisions in-between that layer.

We can create a new layer by selecting **Add Layer** in the **Inspector**:<br>
![Layer](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground_add_layer.png "ground_add_layer")

Afterwards we will add one **User Layer**, let's call it **WeirdPhysics**:<br>
![Weird Physics Layer](http://noobtuts.com/content/unity/2d-flappy-bird-game/layers_weirdphysics.png "layers_weirdphysics")

Now we can select the ground in the **Hierarchy** again and then assign the **WeirdPhysics** layer:<br>
![Ground WeirdPhysics Layer](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground_layer_weirdphysics.png "ground_layer_weirdphysics")

Afterwards we select **Edit**->**Project Settings**->**Physics 2D** from the top menu and disable the **WeirdPhysics** vs. **WeirdPhysics** collisions in the **Layer Collision Matrix**:<br>
![Physics ignore WeirdPhysics Layer Collisions](http://noobtuts.com/content/unity/2d-flappy-bird-game/physics_layer_ignore_weirdphysics.png "physics_layer_ignore_weirdphysics")<br>
_Note: it's very rare that we have to do this in Unity, but our Flappy Bird game is one of those exceptions._

Now the ground will never collide with any of the obstacles.

Also if we press **Play** then we can see the sky and the ground already:<br>
![Ground in Scene](http://noobtuts.com/content/unity/2d-flappy-bird-game/ground_inscene.png "ground_inscene")

## The Bird

### The Bird Image

Alright, let's get to the most important part of our game: the bird. We will begin by drawing a bird flying animation with 4 frames:<br>
![Bird Animation](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird.png "bird")<br>
_Note: right click on the image, select **Save As...** and save it in the project's **Assets/Sprites** folder._

We will use the following **Import Settings** for it:<br>
![Bird ImportSettings](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_importsettings.png "bird_importsettings")

Our bird image contains several smaller images, hence it's important that we select **Multiple** as **Sprite Mode**. Afterwards we can click the **Sprite Editor** button:<br>
![Bird Sprite Editor Button](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_spriteeditor_button.png "bird_spriteeditor_button")

In the **Sprite Editor** we **Slice** it as a **16 x 16** Grid:<br>
![Bird SpriteEditor Settings](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_spriteeditor_settings.png "bird_spriteeditor_settings")

After pressing the **Slice** button we can close the Sprite Editor. Unity will ask us if we want to apply the **Unimported Import Settings**, so let's select **Apply**.

Now we can see our 4 slices as children of the **bird** image in the **Project Area**:<br>
![Bird Children](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_children.png "bird_children")

### The Bird Animation

Let's select all the slices and then drag them into the **Hierarchy**:<br>
![Drag Bird Slices to Hierarchy](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_slices_to_hierarchy.png "bird_slices_to_hierarchy")

Unity knows that we want to create an animation from those slices, which is why it asks us where to save the animation files. We will create a new **BirdAnimation** folder and then save the animation as **fly.anim**.

Afterwards we can see two new files in our **BirdAnimation** folder:<br>
![Bird Animation Files](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_animation_files.png "bird_animation_files")

The **bird_0** file is the state machine that takes care of animation states and speeds. The second file is the **fly** animation itself. Let's double click the **bird_0** file really quick so that we can see the animation state machine:<br>
![Bird in Animator](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_animator.png "bird_animator")<br>
_Note: we don't have to worry about the animation states because we only have one animation._

We will click on the **fly** state and then simply decrease the **speed** to **0.5** in the **Inspector**:<br>
![Bird Fly Animation Speed](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_animation_fly_speed.png "bird_animation_fly_speed")

And since we only have one animation, we are already done here. If we press **Play** then we can even see it in action:<br>
![Bird animated](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_animated.gif "bird_animated")

### Bird Physics

Our bird is supposed to be part of the physics world. Let's begin by giving it a Collider, just like we did with the ground. We will select **Add Component**->**Physics 2D**->**Circle Collider 2D** in the **Inspector**:<br>
![Bird Collider](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_collider.png "bird_collider")

Now everything in the physics world that is supposed to move around will also need a **Rigidbody**. A Rigidbody takes care of things like gravity, velocity and movement forces. We can add a Rigidbody by selecting **Add Component**->**Physics 2D**->**Rigidbody 2D** in the **Inspector**. We will also enable the **Fixed Angle** property so that the bird doesn't suddenly start rotating:<br>
![Bird Rigidbody](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_rigidbody.png "bird_rigidbody")

If we press **Play** then we can already see the Rigidbody's gravity property in action:<br>
![Bird Gravity](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_gravity.gif "bird_gravity")

### The Bird Movement Script

Our bird already looks pretty decent, but it should also fly towards the right at all times, and it should flap its wings to fly upwards if the user presses a button.

This kind of behavior can be implemented with a Script. Let's select **Add Component**->**New Script** in the **Inspector**, name it **Bird** and select **CSharp** as the language. We will also create a new **Scripts** folder in our **Project Area** so that we can put the Bird Script into it:<br>
![Bird Script in Project Area](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_script_projectarea.png "bird_script_projectarea")

We can double click the Script in order to open it:

```
using UnityEngine

using System.Collections



public class Bird MonoBehaviour 



    // Use this for initialization

    void Start 







    // Update is called once per frame

    void Update 
```

We can make the bird fly towards the right at all times by first adding a new **speed** variable and then using the Rigidbody's **velocity** property:

```
using UnityEngine

using System.Collections



public class Bird MonoBehaviour 

    // Movement speed

    public float speed 



    // Use this for initialization

    void Start    

        // Fly towards the right

        GetComponentRigidbody2Dvelocity Vector2right speed





    // Update is called once per frame

    void Update 
```

_Note: the velocity is exactly the movement direction multiplied by the movement speed._ If we save the Script and press **Play** then we can see how the bird flies towards the right of the Screen.

Now we create a new **force** variable and then use our **Update** function to check for key presses. If the user pressed the **Space** key then we will make the bird fly upwards with the **force**:

```
using UnityEngine

using System.Collections



public class Bird MonoBehaviour 

    // Movement speed

    public float speed 



    // Flap force

    public float force 300



    // Use this for initialization

    void Start    

        // Fly towards the right

        GetComponentRigidbody2Dvelocity Vector2right speed





    // Update is called once per frame

    void Update 

        // Flap

        InputGetKeyDownKeyCodeSpace

            GetComponentRigidbody2DAddForceVector2 force
```

If we press **Play** then we can now make the bird fly upwards:<br>
![Bird flapping](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_flapping.gif "bird_flapping")

There is one last thing to do here. We want to restart the game as soon as the bird collided with the ground or with an obstacle. Our bird already has a **Collider** and a **Rigidbody**, which means that Unity automatically calls the **OnCollisionEnter2D** function. All we have to do is actually add it to our Script:

```
void OnCollisionEnter2DCollision2D coll 

    // Restart

    ApplicationLoadLevelApplicationloadedLevel
```

_Note: **Application.LoadLevel** can be used to load a Scene. **Application.loadedLevel** is the currently loaded Scene. Or in other words, we simply restart the current Scene._

## Camera Follow

Right now the Camera never moves. And since the bird always flies towards the right of the screen, we won't be able to see it for a very long time. We will solve this problem by creating a new Script that makes the Camera follow the bird all the time.

Let's select the **Main Camera** in the **Hierarchy** and then click on **Add Component**->**New Script**, name it **CameraFollow** and select **CSharp** as the language. We will also move it into our **Scripts** folder and then open it:

```
using UnityEngine

using System.Collections



public class CameraFollow MonoBehaviour 



    // Use this for initialization

    void Start 







    // Update is called once per frame

    void Update 
```

We won't need the **Start** function this time, so let's remove it:

```
using UnityEngine

using System.Collections



public class CameraFollow MonoBehaviour 



    // Update is called once per frame

    void Update 
```

Let's add a public **Transform** variable to specify which target to follow:

```
using UnityEngine

using System.Collections



public class CameraFollow MonoBehaviour 

    // The Target

    public Transform target



    // Update is called once per frame

    void Update 
```

_Note: we can set that target in the **Inspector** later on._ Afterwards we will simply use the **Update** function to always set the Camera's **X** position to the target's **X** position:

```
// Update is called once per frame

void Update 

    transformposition new Vector3targetposition,

                                     transformposition,

                                     transformposition
```

_Note: the **X** position is the horizontal position._ Now our Script is done. However it's usually considered best practice to do Camera movement after everything else in the Scene was updated. We will simply change our **Update** function to **LateUpdate**, just to have it perfectly smooth:

```
void LateUpdate 

    transformposition new Vector3targetposition,

                                     transformposition,

                                     transformposition
```

If we save the Script then we can take a look at the **Inspector** and drag the **bird_0** GameObject from the **Hierarchy** into the Script's **Target** slot:<br>
![Camera Follow Target](http://noobtuts.com/content/unity/2d-flappy-bird-game/camera_follow_target.png "camera_follow_target")

If we press **Play** then we can now see how the Camera automatically follows the bird:<br>
![Camera following the Bird](http://noobtuts.com/content/unity/2d-flappy-bird-game/camera_following_bird.gif "camera_following_bird")

## The Obstacles

### The Obstacle Image

Right now our game is not very hard. We can change that by adding some obstacles. Let's draw one:<br>
![Obstacle](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle.png "obstacle")<br>
_Note: right click on the image, select **Save As...** and save it in the project's **Assets/Sprites** folder._

We will use the following **Import Settings** for it:<br>
![Obstacle ImportSettings](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_importsettings.png "obstacle_importsettings")

And then drag it from the **Project Area** into the **Hierarchy** in order to create a GameObject from it:<br>
![Obstacle in Hierarchy](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_in_hierarchy.png "obstacle_in_hierarchy")

We will position it at **X=3**, **Y=-5**:<br>
![Obstacle Position](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_position.png "obstacle_position")

Here is how it looks in the Scene:<br>
![Obstacle in Scene](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_inscene.png "obstacle_inscene")

### Obstacle Physics

The obstacle should be part of the physics world again. The bird should be able to collide with it, so let's select **Add Component**->**Physics 2D**->**Box Collider 2D** in the **Inspector**:<br>
![Obstacle Collider](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_collider.png "obstacle_collider")

We talked about how the obstacles might end up inside the ground and how we don't want the two to collide with each other, so let's make it part of our **WeirdPhysics** layer, too:<br>
![Obstacle Layer](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_layer.png "obstacle_layer")<br>
_Note: since we disabled collisions in-between our WeirdPhysics layer, the ground will never collide with an obstacle. The bird can still collide with the ground and with the obstacle because it has a different layer (the default one)._

Alright so some of the obstacles are also supposed to move up and down. Everything in the physics world that is supposed to move will need a Rigidbody, so let's select **Add Component**->**Physics 2D**->**Rigidbody 2D** in the **Inspector** again. We don't want it to be affected by gravity, so let's set the **Gravity Scale** to **0**. We also don't want it to rotate, so let's also enable **Fixed Angle** again:<br>
![Obstacle Rigidbody](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_rigidbody.png "obstacle_rigidbody")

If we press **Play** and let the bird fly against the obstacle then we can see how the level restarts:<br>
![Bird vs Obstacle](http://noobtuts.com/content/unity/2d-flappy-bird-game/bird_vs_obstacle.gif "bird_vs_obstacle")

### Obstacle Movement

Alright so some of the obstacles are supposed to move up and down. This kind of behavior can be implemented with a Script again. Let's select **Add Component**->**New Script**, name it **Obstacle**, move it into our **Scripts** folder and then open it:

```
using UnityEngine

using System.Collections



public class Obstacle MonoBehaviour 



    // Use this for initialization

    void Start 







    // Update is called once per frame

    void Update 
```

There are many different ways to make the obstacle move up and down all the time. As usual, we will use the easiest way.

We will begin by adding a **speed** variable and then setting the Rigidbody's **velocity** so that the obstacles move upwards with the **speed**:

```
using UnityEngine

using System.Collections



public class Obstacle MonoBehaviour 

    // Movement Speed (0 means don't move)

    public float speed 



    // Use this for initialization

    void Start 

        // Initial Movement Direction

        GetComponentRigidbody2Dvelocity Vector2 speed    





    // Update is called once per frame

    void Update 
```

Now the trick is to use Unity's [InvokeRepeating](http://docs.unity3d.com/ScriptReference/MonoBehaviour.InvokeRepeating.html) function to reverse that velocity every few seconds:

```
using UnityEngine

using System.Collections



public class Obstacle MonoBehaviour 

    // Movement Speed (0 means don't move)

    public float speed 



    // Switch Movement Direction every x seconds

    public float switchTime 



    void Start 

        // Initial Movement Direction

        GetComponentRigidbody2Dvelocity Vector2 speed



        // Switch every few seconds

        InvokeRepeating"Switch", , switchTime





    void Switch 

        GetComponentRigidbody2Dvelocity 
```

_Note: the Switch function simply reverses the Rigidbody's velocity. We then use **InvokeRepeating** to tell Unity to call that function every few seconds. We also added a **switchTime** variable to specify the time in which **Switch** should be called._ Let's save the Script and set the obstacle's **Speed** to **1**:<br>
![Obstacle Script in Inspector](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_script_inspector.png "obstacle_script_inspector")<br>
_Note: if we don't want an obstacle to move then we can either set it's **Speed** to **0** or disable the Script._

If we press **Play** then we can see our obstacle moving up- and downwards:<br>
![Obstacle moving](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_moving.gif "obstacle_moving")

### Adding more Obstacles

Let's right click the obstacle in the **Hierarchy**, select **Duplicate** and move it a bit further towards the right:<br>
![Duplicated Obstacle](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_duplicated.png "obstacle_duplicated")

We will also duplicate one and then set the **Scale.Y** property to **-1**:<br>
![Obstacle Scale Y-1](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_scale_y-1.png "obstacle_scale_y-1")

This way it looks properly when positioning it upside-down:<br>
![Obstacle upside-down](http://noobtuts.com/content/unity/2d-flappy-bird-game/obstacle_upsidedown.png "obstacle_upsidedown")

We can add as many obstacles with as many different **speed** and **switchTime** properties as we want:<br>
![Unity 2D Flappy Bird Game](http://noobtuts.com/content/unity/2d-flappy-bird-game/unity_flappy_bird.png "unity_flappy_bird")

## Summary

We just learned how incredibly easy it is to make a Flappy Bird game in Unity. We used some very powerful Unity features like Physics, Mecanim _(for the animation)_, Layers, the Sprite Editor and Scripting. As usual now it's up to the reader to make the game fun and more difficult.

## Download Source Code & Project Files

The _Unity 2D Flappy Bird Tutorial_ source code & project files can be downloaded by [Premium](http://noobtuts.com/premium) members.

**All Tutorials. All Source Codes & Project Files. One time Payment.**<br>
[Get Premium](http://noobtuts.com/premium) today!

© 2012-2015 [noobtuts.com](http://noobtuts.com)
