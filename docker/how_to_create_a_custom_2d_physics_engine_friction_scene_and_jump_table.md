# How to Create a Custom 2D Physics Engine: Friction, Scene and Jump Table

[Original URL](http://gamedevelopment.tutsplus.com/tutorials/how-to-create-a-custom-2d-physics-engine-friction-scene-and-jump-table--gamedev-7756)

> In the first two tutorials in this series, I covered the topics of Impulse Resolution and Core Architecture. Now it's time to add on some of the final touches to our 2D, impulse-based physics engine....

In the first two tutorials in this series, I covered the topics of [Impulse Resolution](http://gamedev.tutsplus.com/tutorials/implementation/create-custom-2d-physics-engine-aabb-circle-impulse-resolution/) and [Core Architecture](http://gamedev.tutsplus.com/tutorials/implementation/how-to-create-a-custom-2d-physics-engine-the-core-engine/). Now it's time to add on some of the final touches to our 2D, impulse-based physics engine.

The topics we'll look at in this article are:

- Friction
- Scene
- Collision Jump Table

I highly recommended reading up on [the previous two articles in the series](http://gamedev.tutsplus.com/series/custom-game-physics-engine/) before attempting to tackle this one. Some key information in the previous articles is built upon within this article.

***Note:** Although this tutorial is written using C++, you should be able to use the same techniques and concepts in almost any game development environment.*

## Video Demo

Here's a quick demo of what we're working towards in this part:

## Friction

Friction is a part of collision resolution. Friction always applies a force upon objects in the direction opposite to the motion in which they are to travel.

In real life, friction is an incredibly complex interaction between different substances, and in order to model it, vast assumptions and approximations are made. These assumptions are implied within the math, and are usually something like "the friction can be approximated by a single vector" - similarly to how rigid body dynamics simulates real life interactions by assuming bodies with uniform density that cannot deform.

Take a quick look at the video demo from the first article in this series:

The interactions between the bodies are quite interesting, and the bouncing during collisions feels realistic. However, once the objects land on the solid platform, they just sort of all press away and drift off the edges of the screen. This is due to a lack of friction simulation.

### Impulses, Again?

As you should recall from [the first article in this series](http://gamedev.tutsplus.com/tutorials/implementation/create-custom-2d-physics-engine-aabb-circle-impulse-resolution/), a particular value, `j`, represented the magnitude of an impulse required to separate two objects penetration during a collision. This magnitude can be referred as `jnormal` or `jN` as it is used to modify velocity along the collision normal.

Incorporating a friction response involves calculating another magnitude, referred to as `jtangent` or `jT`. Friction will be modeled as an impulse. This magnitude will modify the velocity of an object along the negative tangent vector of the collision, or in other words along the friction vector. In two dimensions, solving for this friction vector is a solvable problem, but in 3D the problem becomes much more complex.

Friction is quite simple, and we can make use of our previous equation for `j`, except we will replace all instances of the normal `n` with a tangent vector `t`.

\[ Equation 1:\\<br>
j = \frac{-(1 + e)(V^{B}-V^{A})\cdot n)}<br>
{\frac{1}{mass^A} + \frac{1}{mass^B}}\]

Replace `n` with `t`:

\[ Equation 2:\\<br>
j = \frac{-(1 + e)((V^{B}-V^{A})\cdot t)}<br>
{\frac{1}{mass^A} + \frac{1}{mass^B}}\]

Although only a single instance of `n` was replaced with `t` in this equation, once rotations are introduced a few more instances must be replaced besides the single one in the numerator of Equation 2.

Now the matter of how to calculate `t` arises. The tangent vector is a vector perpendicular to the collision normal that is facing more towards the normal. This might sound confusing - don't worry, I have a diagram!

Below you can see the tangent vector perpendicular to the normal. The tangent vector can either point to the left or the right. To the left would be "more away" from the relative velocity. However, it is defined as the perpendicular to the normal that is pointing "more towards" the relative velocity.

![Vectors of various types within the timeframe of a collision of rigid bodies.](https://cdn.tutsplus.com/gamedev/uploads/2013/05/custom-physics-tangent-normal-rv.png)<br>
Vectors of various types within the timeframe of a collision of rigid bodies. As stated briefly earlier, friction will be a vector facing opposite to the tangent vector. This means that the direction in which to apply friction can be directly computed, since the normal vector was found during the collision detection.

Knowing this, the tangent vector is (where `n` is the collision normal):

\[ V^R = V^{B}-V^{A} \\<br>
t = V^R - (V^R \cdot n) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii n \]

All that is left to solve for `jt`, the magnitude of the friction, is to compute the value directly using the equations above. There are some very tricky pieces after this value is computed that will be covered shortly, so this isn't the last thing needed in our collision resolver:

```
// Re-calculate relative velocity after normal impulse
// is applied (impulse from first article, this code comes
// directly thereafter in the same resolve function)
Vec2 rv = VB - VA

// Solve for the tangent vector
Vec2 tangent = rv - Dot( rv, normal ) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii normal
tangent.Normalize( )

// Solve for magnitude to apply along the friction vector
float jt = -Dot( rv, t )
jt = jt / (1 / MassA + 1 / MassB)
```

The above code follows Equation 2 directly. Again, it's important to realize that the friction vector points in the opposite direction of our tangent vector, and as such we must apply a negative sign when we dot the relative velocity along the tangent to solve for the relative velocity along the tangent vector. This negative sign flips the tangent velocity and suddenly points in the direction in which friction should be approximated as.

### Coulomb's Law

[Coulomb's law](http://en.wikipedia.org/wiki/Friction#Dry_friction) is the portion of friction simulation that most programmers have trouble with. I myself had to do quite a bit of studying to figure out the correct way of modeling it. The trick is that Coulomb's law is an inequality.

Coulomb friction states:

\[ Equation 3: \\<br>
F_f <= \mu F_n \]

In other words, the force of friction is always less than or equal to the normal force multiplied by some constant `μ` (whose value depends on the materials of the objects).

The normal force is just our old `j` magnitude multiplied by the collision normal. So if our solved `jt` (representing the force of friction) is less than `μ` times the normal force, then we can use our `jt` magnitude as friction. If not, then we must use our normal force times `μ` instead. This "else" case is a form of clamping our friction below some maximum value, the max being the normal force times `μ`.

The whole point of Coulomb's law is to perform this clamping procedure. This clamping turns out to be the most difficult portion of friction simulation for impulse-based resolution to find documentation on anywhere - until now, at least! Most white papers I could find on the subject either skipped friction entirely, or stopped short and implemented improper (or non-existent) clamping procedures. Hopefully by now you have an appreciation for understanding that getting this part right is important.

Lets just dish out the clamping all in one go before explaining anything. This next code block is the previous code example with the finished clamping procedure and friction impulse application all together:

```
// Re-calculate relative velocity after normal impulse
// is applied (impulse from first article, this code comes
// directly thereafter in the same resolve function)
Vec2 rv = VB - VA

// Solve for the tangent vector
Vec2 tangent = rv - Dot( rv, normal ) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii normal
tangent.Normalize( )

// Solve for magnitude to apply along the friction vector
float jt = -Dot( rv, t )
jt = jt / (1 / MassA + 1 / MassB)

// PythagoreanSolve = A^2 + B^2 = C^2, solving for C given A and B
// Use to approximate mu given friction coefficients of each body
float mu = PythagoreanSolve( A->staticFriction, B->staticFriction )

// Clamp magnitude of friction and create impulse vector
Vec2 frictionImpulse
if(abs( jt ) < j articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii mu)
 frictionImpulse = jt articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii t
else
{
 dynamicFriction = PythagoreanSolve( A->dynamicFriction, B->dynamicFriction )
 frictionImpulse = -j articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii t articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii dynamicFriction
}

// Apply
A->velocity -= (1 / A->mass) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii frictionImpulse
B->velocity += (1 / B->mass) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii frictionImpulse
```

I decided to use this formula to solve for the friction coefficients between two bodies, given a coefficient for each body:

\[ Equation 4: \\<br>
Friction = \sqrt[]{Friction^2_A + Friction^2_B} \]

I actually saw someone else do this in their own physics engine, and I liked the result. An average of the two values would work perfectly fine to get rid of the use of square root. Really, any form of picking the friction coefficient will work; this is just what I prefer. Another option would be to use a lookup table where the type of each body is used as an index into a 2D table.

It is important that the absolute value of `jt` is used in the comparison, since the comparison is theoretically clamping raw magnitudes below some threshold. Since `j` is always positive, it must be flipped in order to represent a proper friction vector, in the case dynamic friction is used.

### Static and Dynamic Friction

In the last code snippet static and dynamic friction were introduced without any explanation! I'll dedicate this whole section to explaining the difference between and necessity of these two types of values.

Something interesting happens with friction: it requires an "energy of activation" in order for objects to start moving when at complete rest. When two objects are resting upon one another in real life, it takes a fair amount of energy to push on one and get it moving. However once you get something sliding it is often easier to keep it sliding from then on.

This is due to how friction works on a microscopic level. Another picture helps here:

![Microscopic view of what causes energy of activation due to friction.](https://cdn.tutsplus.com/gamedev/uploads/2013/05/custom-physics-energy-activation.png)<br>
Microscopic view of what causes energy of activation due to friction. As you can see, the small deformities between the surfaces are really the major culprit that creates friction in the first place. When one object is at rest on another, microscopic deformities rest between the objects, interlocking. These need to be broken or separated in order for the objects to slide against one another.

We need a way to model this within our engine. A simple solution is to provide each type of material with two friction values: one for static and one for dynamic.

The static friction is used to clamp our `jt` magnitude. If the solved `jt` magnitude is low enough (below our threshold), then we can assume the object is at rest, or nearly as rest and use the entire `jt` as an impulse.

On the flipside, if our solved `jt` is above the threshold, it can be assumed that the object has already broken the "energy of activation", and in such a situation a lower friction impulse is used, which is represented by a smaller friction coefficient and a slightly different impulse computation.

## Scene

Assuming you did not skip any portion of the Friction section, well done! You have completed the hardest part of this entire series (in my opinion).

The `Scene` class acts as a container for everything involving a physics simulation scenario. It calls and uses the results of any broad phase, contains all rigid bodies, runs collision checks and calls resolution. It also integrates all live objects. The scene also interfaces with the user (as in the programmer using the physics engine).

Here is an example of what a scene structure may look like:

```
class Scene
{
public:
 Scene( Vec2 gravity, real dt );
 ~Scene( );

 void SetGravity( Vec2 gravity )
 void SetDT( real dt )

 Body *CreateBody( ShapeInterface *shape, BodyDef def )

 // Inserts a body into the scene and initializes the body (computes mass).
 //void InsertBody( Body *body )

 // Deletes a body from the scene
 void RemoveBody( Body *body )

 // Updates the scene with a single timestep
 void Step( void )

 float GetDT( void )
 LinkedList *GetBodyList( void )
 Vec2 GetGravity( void )
 void QueryAABB( CallBackQuery cb, const AABB& aabb )
 void QueryPoint( CallBackQuery cb, const Point2& point )

private:
 float dt // Timestep in seconds
 float inv_dt // Inverse timestep in sceonds
 LinkedList body_list
 uint32 body_count
 Vec2 gravity
 bool debug_draw
 BroadPhase broadphase
};
```

There is not anything particularly complex about the `Scene` class. The idea is to allow the user to add and remove rigid bodies easily. The `BodyDef` is a structure that holds all information about a rigid body, and can be used to allow the user to insert values as a sort of configuration structure.

The other important function is `Step()`. This function performs a single round of collision checks, resolution and integration. This should be called from within the timestepping loop outlined in [the second article of this series](http://gamedev.tutsplus.com/tutorials/implementation/how-to-create-a-custom-2d-physics-engine-the-core-engine/).

Querying a point or AABB involves checking to see which objects actually collide with either a pointer or AABB within the scene. This makes it easy for gameplay-related logic to see how things are placed within the world.

## Jump Table

We need an easy way to pick out which collision function should be called, based on the type of two different objects.

In C++ there are two major ways that I am aware of: double dispatch and a 2D jump table. In my own personal tests I found the 2D jump table to superior, so I'll go into detail about how to implement that. If you're planning to use a language other than C or C++ I am sure an array of functions or functor objects can be constructed similarly to a table of function pointers (which is another reason I chose to talk about jump tables rather than other options that are more specific to C++).

A jump table in C or C++ is a table of function pointers. Indices representing arbitrary names or constants are used to index into the table and call a specific function. The usage could look something like this for a 1D jump table:

```
enum Animal
{
 Rabbit
 Duck
 Lion
};

const void (*talk)( void )[] = {
 RabbitTalk,
 DuckTalk,
 LionTalk,
};

// Call a function from the table with 1D virtual dispatch
talk[Rabbit]( ) // calls the RabbitTalk function
```

The above code actually mimics what the C++ language itself implements with `virtual function calls and inheritance`. However, C++ only implements single dimensional virtual calls. A 2D table can be constructed by hand.

Here is some psuedocode for a 2D jump table to call collision routines:

```
collisionCallbackArray = {
 AABBvsAABB
 AABBvsCircle
 CirclevsAABB
 CirclevsCircle
}

// Call a collsion routine for collision detection between A and B
// two colliders without knowing their exact collider type
// type can be of either AABB or Circle
collisionCallbackArray[A->type][B->type]( A, B )
```

And there we have it! The actual types of each collider can be used to index into a 2D array and pick a function to resolve collision.

Note, however, that `AABBvsCircle` and `CirclevsAABB` are almost duplicates. This is necessary! The normal needs to be flipped for one of these two functions, and that is the only difference between them. This allows for consistent collision resolution, no matter the combination of objects to resolve.

## Conclusion

By now we have covered a huge amount of topics in setting up a custom rigid body physics engine entirely from scratch! Collision resolution, friction, and engine architecture are all the topics that have been covered thus far. An entirely successful physics engine suitable for many production-level two dimensional games can be constructed with the knowledge presented in this series so far.

Looking ahead into the future, I plan to write one more article devoted entirely to a very desirable feature: rotation and orientation. Oriented objects are exceedingly attractive to watch interact with one another, and are the final piece that our custom physics engine requires.

Resolution of rotation turns out to be quite simple, though collision detection takes a hit in complexity. Good luck until next time, and please do ask questions or post comments below!
