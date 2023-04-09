+++
title = "Proof of work"
date = 2023-04-09
weight = 2
+++
Showing off something cool!

# Kepler's Equation
Johannes Kepler was a 16th century astronomer (among many other things) from Germany. He is well known for establishing the way planets moved around the sun with his laws of planetary motion. As it turns out, these laws can be applied to many non-planet celestial objects, and can accurately predict the position of objects in orbits. 

This simulation was programmed using Kepler's laws. Click anywhere in the black box bellow to interact with the simulation.

<canvas id="glcanvas" tabindex='1' width="800" height="600"></canvas>
<script src="https://not-fl3.github.io/miniquad-samples/mq_js_bundle.js"></script>
<script>load("./../kepler_sim.wasm");</script>

Use J to reduce the timescale, K to reset it to 10, L to increase the timescale.

Holding shift will multiply the change by 10, holding z will multiply the change by 100, holding both will multiply the change by 1000.