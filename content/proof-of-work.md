+++
title = "Proof of work"
date = 2023-04-09
weight = 2
+++
Showing off something cool!

# Kepler's Equation
Johannes Kepler was a 16th century astronomer (among many other things) from Germany. He is well known for establishing the way planets moved around the sun with his laws of planetary motion. As it turns out, these laws can be applied to many non-planet celestial objects, and can accurately predict the position of objects in orbits. 

This simulation was programmed using Kepler's laws. Click anywhere in the black box bellow to interact with the simulation. If you can't see it, grab a .exe version [here](https://github.com/Syst3mz/kepler_sim/releases/tag/1.0.0).

<canvas id="glcanvas" tabindex='1' width="800" height="600"></canvas>
<script src="https://not-fl3.github.io/miniquad-samples/mq_js_bundle.js"></script>
<script>load("./../kepler_sim.wasm");</script>

Use J to reduce the timescale, K to reset it to 10, L to increase the timescale.

Holding shift will multiply the change by 10, holding z will multiply the change by 100, holding both will multiply the change by 1000.

You can note that as the red "distance" numbers increase, the speed of the planet decreases. This shows that my simulation is abiding by Kepler's laws which would predict that objects further from the body they are orbiting must be going slower.

# More information About Orbits
A great video about orbits, which helped serve as the foundation of this project can be found [here](https://www.youtube.com/watch?v=t89De819YMA) It goes in to significant depth about the techniques used to simulate orbits, as well as being very well animated and voiced. They take this project much further than I have.

Another great video is [this](https://www.youtube.com/watch?v=7axImc1sxa0) one from Sebastian Lague

And I would of course be remiss not to include the [Wikipedia](https://en.wikipedia.org/wiki/Kepler%27s_laws_of_planetary_motion) page for Kepler's laws. 

# Technical Information About the Project
I wrote this simulation in the Rust programming language because Rust is fast and portable. This simulation is not the most efficient simulation of planetary orbits, and the rendering of orbital paths is particularly in-efficient. The next task to improve this project would be to speed up the line drawing significantly. For graphics, I am using the [Macroquad](https://macroquad.rs/) crate, which was perfect for this project because of its ability to compile to [WASM](https://webassembly.org/) allowing easy deployment to modern web browsers.   
