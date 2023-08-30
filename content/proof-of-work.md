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

Another great video by Sebastian Lague can be found [here](https://www.youtube.com/watch?v=7axImc1sxa0).

And I would of course be remiss not to include the [Wikipedia](https://en.wikipedia.org/wiki/Kepler%27s_laws_of_planetary_motion) page for Kepler's laws. 

# Technical Information About the Project
I wrote this simulation in the Rust programming language because Rust is fast and portable. This simulation is not the most efficient simulation of planetary orbits, and the rendering of orbital paths is particularly in-efficient. The next task to improve this project would be to speed up the line drawing significantly. For graphics, I am using the [Macroquad](https://macroquad.rs/) crate, which was perfect for this project because of its ability to compile to [WASM](https://webassembly.org/) allowing easy deployment to modern web browsers.

## How does the simulation work?
The simulation is really a fancy equation solver. The equation I try to solve is $M = E - esin(E)$ Where $M$ is the mean anomaly, $E$ is the eccentric anomaly, and $e$ is the eccentricity. For a fantastic explanation of what these values mean, see [here](https://youtu.be/t89De819YMA?t=386). Once $M$ is found, I can use a little trigonometry ($\sin$ and $\cos$, my old enemies) and a planets semi-major and minor axes to find that planet's position in space. From there its just a bit of rendering math that Macroquad spared me from to get planets going in a ellipse.


The major issue of this approach is finding E. Kepler himself doubted the existence of closed form solution to his equation, and to this day no such solution has been found. Right now, the best we can do is use a variety of root finding algorithms to approach a true answer, but we can't ever guarantee that we have found a "perfect" solution. I chose to use Newton's method to solve this equation because I knew it from Calculus and it is relatively simple to implement. 

Here is the full equation solver at the heart of the simulator:
```rust
const EPSILON: f64 = 0.000001;
fn newtons_method<F, G>(x0: f64, fx: F, dx: G) -> f64
    where F: Fn(f64) -> f64, G: Fn(f64) -> f64 {
    let mut root = x0;

    loop {
        let next_root = root - (fx(root) / dx(root));
        let delta = f64::abs(next_root - root);
        root = next_root;

        if delta <= EPSILON {
            break;
        }
    }

    root
}
```

Lets walk through it line by line:
```rust
const EPSILON: f64 = 0.000001;
```
For reasons unknown to me, epsilon is chosen to mean: "some very small value". I will not break mathematical tradition so epsilon is some very small value, in this case $\frac{1}{1,000,000}$. Due to how Newton's method converges on a solution, when the changes to the guess start to be very small, we can be highly confident we are near a root. Epsilon controls how small my program considers "small enough".

```rust
fn newtons_method<F, G>(x0: f64, fx: F, dx: G) -> f64
```
This is Rust for: "Define a function called `newtons_method`. It accepts three arguments called `x0`, `fx`, `dx` returns a decimal number." The `<F, G>` tells Rust, that `newtons_method` accepts arguments of any type and should call those types `F` and `G`.

```rust
where F: Fn(f64) -> f64, G: Fn(f64) -> f64 {
```
This restricts what data type can be put into `F` and `G` to be: "Any function which takes a decimal number as input, returns a decimal number as output."
```rust
let mut root = x0;
```
Make a variable called `root`, assign `x0` to it. Rust does not let variables change by default, so the `mut` before `root` lets `root` change. Newton's method requires an initial guess (often called $x_0$) the better this initial guess is, the faster Newton's method will converge to a root of the function.
```rust
loop {
```
The `loop` keyword in rust, says to do what comes after the `{` forever.
```rust
let next_root = root - (fx(root) / dx(root));
```
this is the Newton's method "part" of the algorithm. Newtons method is defined as $$x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}$$ In English, the next guess is equal to the current guess minus $f$ of the current guess over the derivative of $f$ of the current guess.
```rust
let delta = f64::abs(next_root - root);
```
Compute how far the new guess is from the previous guess. The abs is there because we only care about the distance on the number line.
```rust
root = next_root;
```
set `root` to the next guess (`next_root`).
```rust
if delta <= EPSILON {
    break;
}
```
I grouped these three lines, because they all act as one "thing". If the change (`delta`) is less than or equal to epsilon, stop the infinite loop.

```rust
root
```
spit out what ever root was calculated.


In brief, this function can compute roots of any differentiable equation...eventually. Since Kepler's Equation is (in fact) an equation, Newton's method works for it!
Once we have this, solving for $E$ is as simple as: 
```rust
fn solve_keplers(mean_anomaly: f64, eccentricity: f64) -> f64 {
        let eccentric_anomaly = if eccentricity > 0.8 { f64::PI() } else {mean_anomaly};
        newtons_method(
            eccentric_anomaly,
            |eccentric_anomaly| Self::keplers_equation(mean_anomaly, eccentric_anomaly, eccentricity),
            |eccentric_anomaly| Self::keplers_equation_derivation(eccentric_anomaly, eccentricity)
        )
    }
```
You might note at the top of the function, I guess that eccentric anomaly is either $\pi$ or what ever the mean anomaly. This is the seed value for Newton's method ($x_0$). These specific starting values are recommended by Anderson, and have worked well for me.