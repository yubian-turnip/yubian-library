# Macro-Scale Mountain Generation (June 29 2026-INSERT-DATE)
## My Approach to Procedural Generation for Terrain
To me, noise-based procedural generation isn't enough to have high-quality non-infinite maps. 

For this project noise is used for local or sub-local
> eg. textures and small variation in surface

For micro-scale terrain L-Systems should be used
> eg. cliffs, hills, overhangs, caves, paths, etc.

For macro-scale terrain (this sprint) "terrain spines" make the most sense
> Mountains have a more jagged shape, never need overhangs, and smooth details are on a smaller scale not large mountain scale

## Goal for this Sprint
Get a working, non-broken mesh of the large scale that is adjustable based on constraints such as map size, etc.

## Day 0 (Prototype / Existing Work)
![Mountain Spine Prototype](https://github.com/yubian-turnip/yubian-library/blob/master/komorebi/design/images/mountain-spine-prototype.png?raw=true)
> Figure 1: A completely random procedural mountain spine

The terrain shown above uses **Unity's debug Gizmos** to connect a list of high, med-high, med-low, and low points with basic logic. Currently nothing renders yet in the game view. 

What happens:
* On Start: Clear, Generate, and Connect the graph
* Per Frame: Draw the Gizmos
* On Space Pressed: clear, generate, and connect new points 

As this is just a prototype it was mostly just to see if what I was doing is realistic and work-able.

## Day 1 (June 29 2026)
My idea idea for the spines is a lot different that the random approach in the prototype. It goes like this:
1. Trace* a noise map for the highest values.
2. Trace* a noise map for the lowest values.
3. Connect the two values with a connection algorithm

> *we'll come back to this

This should allow there to be more clearly defined highs and lows without having to having to make sense of random points in the previous algorithm. 

---
You might be asking, "why not just that same noise map to generate a mesh directly?" 
#### Smoothness
>Noise maps are too smooth unless you do a bunch of unnecessary, expensive math to get them not smooth. Noise is good for hills, not mountains. 
#### Triangle count
>Also generating a dynamic mesh based on these spines uses a lot less triangles from the start and doesn't need to be simplified like a purely noise based map would to get it to work for our spines and layers approach.
#### Squareness 
>It also doesn't have ridged lines that tend to form from the square nature of reading off a noise map.

It also has the added benefit of defining a path for streams and rivers to follow when we get to the micro and local sections.

## Day 2 (June 30 2026)
### Tracing a Noise Map
1. Group points into $n$ amount of groups from the radius of a center point ($C$)  
2. Take the average of the points in those groups for each group: $p= [x_1,x_2,x_3...x_n] , \bar{p}=\frac{1}{g}\sum_{i = 1}^{g}p_i$
	> $g$ = number of points in group 
3. Multiply the averages of the groups by normalized rays pointing in the direction of the groups from the center point
4. Find the total of the rays: $P= [\bar{p}_1,\bar{p}_2,\bar{p}_3...\bar{p}_n] , P_{total} =\sum_{i = 1}^{n}P_i$
	> Rather than taking the average here it is much better to just Sum them all up instead because dividing by 1/n makes the vector way to small. Adding vectors in a circle pretty much already averages them in a way so adding an extra step gets you the difference. 
5. Start the process over again where $P_{total}+C=C_{new}$ 

We also need a process for getting the actual points from the radius.

1. Find a square AABB around where the circle could be
2. For each point in that AABB find the distance from the circle and check if its in the circle using the circle function:  
	> $x^2+y^2 \leq r^2$
3. Add it to the queue if if 
<!--stackedit_data:
eyJoaXN0b3J5IjpbODE2OTU0NjMxLDE1MDg1Njc3MCwtMTYwNT
cxNDcxMCwxMzE5MTUwNjI2LDE5ODIyNjkxMjQsLTEyMjU4NjQy
MywxOTY0MjM0NTg3LDE2OTc4MzY1MTAsMTU2Njg5OTM0MCw5ND
Y1MTE3MzQsLTUyMzg0MTQ2MSwxNjQ5NDYzMzU0XX0=
-->