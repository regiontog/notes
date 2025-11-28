---
title: "Fix Your Timestep!"
source: "https://gafferongames.com/post/fix_your_timestep/"
author:
  - "[[Gaffer On Games]]"
published: 2004-06-10
created: 2025-11-28
description: "Hello readers, I’m no longer posting new content on gafferongames.comPlease check out my new blog at mas-bandwidth.com! Introduction Hi, I’m Glenn Fiedler and welcome to Game Physics.In the previous article we discussed how to integrate the equations of motion using a numerical integrator. Integration sounds complicated, but it’s just a way to advance the your physics simulation forward by some small amount of time called “delta time” (or dt for short)."
tags:
  - "clippings"
---
## Free the physics

Now let’s take it one step further. What if you want exact reproducibility from one run to the next given the same inputs? This comes in handy when trying to network your physics simulation using deterministic lockstep, but it’s also generally a nice thing to know that your simulation behaves exactly the same from one run to the next without any potential for different behavior depending on the render framerate.

But you ask why is it necessary to have fully fixed delta time to do this? Surely the semi-fixed delta time with the small remainder step is “good enough”? And yes, you are right. It is *good enough* in most cases but it is not *exactly the same* due to to the limited precision of floating point arithmetic.

What we want then is the best of both worlds: a fixed delta time value for the simulation plus the ability to render at different framerates. These two things seem completely at odds, and they are - unless we can find a way to decouple the simulation and rendering framerates.

Here’s how to do it. Advance the physics simulation ahead in fixed dt time steps while also making sure that it keeps up with the timer values coming from the renderer so that the simulation advances at the correct rate. For example, if the display framerate is 50fps and the simulation runs at 100fps then we need to take two physics steps every display update. Easy.

What if the display framerate is 200fps? Well in this case it we need to take half a physics step each display update, but we can’t do that, we must advance with constant dt. So we take one physics step every two display updates.

Even trickier, what if the display framerate is 60fps, but we want our simulation to run at 100fps? There is no easy multiple. What if VSYNC is disabled and the display frame rate fluctuates from frame to frame?

If you head just exploded don’t worry, all that is needed to solve this is to change your point of view. Instead of thinking that you have a certain amount of frame time you must simulate before rendering, flip your viewpoint upside down and think of it like this: the renderer **produces time** and the simulation **consumes it** in discrete dt sized steps.

For example:

```
double t = 0.0;
    const double dt = 0.01;

    double currentTime = hires_time_in_seconds();
    double accumulator = 0.0;

    while ( !quit )
    {
        double newTime = hires_time_in_seconds();
        double frameTime = newTime - currentTime;
        currentTime = newTime;

        accumulator += frameTime;

        while ( accumulator >= dt )
        {
            integrate( state, t, dt );
            accumulator -= dt;
            t += dt;
        }

        render( state );
    }
```

Notice that unlike the semi-fixed timestep we only ever integrate with steps sized dt so it follows that in the common case we have some unsimulated time left over at the end of each frame. This left over time is passed on to the next frame via the accumulator variable and is not thrown away.

## The final touch

But what do to with this remaining time? It seems incorrect doesn’t it?

To understand what is going on consider a situation where the display framerate is 60fps and the physics is running at 50fps. There is no nice multiple so the accumulator causes the simulation to alternate between mostly taking one and occasionally two physics steps per-frame when the remainders “accumulate” above dt.

Now consider that the majority of render frames will have some small remainder of frame time left in the accumulator that cannot be simulated because it is less than dt. This means we’re displaying the state of the physics simulation at a time slightly different from the render time, causing a subtle but visually unpleasant stuttering of the physics simulation on the screen.

One solution to this problem is to interpolate between the previous and current physics state based on how much time is left in the accumulator:

```
double t = 0.0;
    double dt = 0.01;

    double currentTime = hires_time_in_seconds();
    double accumulator = 0.0;

    State previous;
    State current;

    while ( !quit )
    {
        double newTime = time();
        double frameTime = newTime - currentTime;
        if ( frameTime > 0.25 )
            frameTime = 0.25;
        currentTime = newTime;

        accumulator += frameTime;

        while ( accumulator >= dt )
        {
            previousState = currentState;
            integrate( currentState, t, dt );
            t += dt;
            accumulator -= dt;
        }

        const double alpha = accumulator / dt;

        State state = currentState * alpha + 
            previousState * ( 1.0 - alpha );

        render( state );
    }
```

This *looks* complicated but here is a simple way to think about it. Any remainder in the accumulator is effectively a measure of just how much more time is required before another whole physics step can be taken. For example, a remainder of dt/2 means that we are currently halfway between the current physics step and the next. A remainder of dt\*0.1 means that the update is 1/10th of the way between the current and the next state.

We can use this remainder value to get a blending factor between the previous and current physics state simply by dividing by dt. This gives an alpha value in the range \[0,1\] which is used to perform a linear interpolation between the two physics states to get the current state to render. This interpolation is easy to do for single values and for vector state values. You can even use it with full 3D rigid body dynamics if you store your orientation as a quaternion and use a spherical linear interpolation (slerp) to blend between the previous and current orientations.