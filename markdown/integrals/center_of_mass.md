# Center of Mass


````
CalculusWithJulia.WeaveSupport.ImageFile("figures/seesaw.png", L"
A silhouette of two children on a seesaw. The seesaw can be balanced
only if the distance from the central point for each child reflects
their relative weights, or masses, through the formula $d_1m_1 = d_2
m_2$. This means if the two children weigh the same the balance will
tip in favor of the child farther away, and if both are the same
distance, the balance will tip in favor of the heavier.
")
````





The game of seesaw is one where children earn an early appreciation for the effects of distance and relative weight. For children with equal weights, the seesaw will balance if they sit an equal distance from the center (on opposite sides, of course). However, with unequal weights that isn't the case. If one child weighs twice as much, the other must sit twice as far.

The key relationship is that $d_1 m_1 = d_2 m_2$. This come from physics, where the moment about a point is defined by the mass times the distance. This balance relationship says the overall moment balances out.  When this is the case, then the *center of mass* is at the fulcrum point, so there is no impetus to move.

The [center](http://en.wikipedia.org/wiki/Center_of_mass) of mass is an old concept that often allows a possibly complicated relationship involving weights to be reduced to a single point. The seesaw is an example: if the center of mass is at the fulcrum the seesaw can balance.

In general, we use position of the mass, rather than use distance from some fixed fulcrum. With this, the center of mass  for a finite set of point masses distributed on the real line, is defined by:


$$~
\bar{\text{cm}} = \frac{m_1 x_1 + m_2 x_2 + \cdots + m_n x_n}{m_1 + m_2 + \cdots + m_n}.
~$$

Writing $w_i = m_i / (m_1 + m_2 + \cdots + m_n)$, we get the center of mass is just a weighted sum: $w_1 x_1 + \cdots + w_n x_n$, where the $w_i$ are the relative weights.

With some rearrangment, we can see that the center of mass satisfies the equation:

$$~
w_1 \cdot (x_1 - \bar{\text{cm}}) + w_2 \cdot (x_2 - \bar{\text{cm}}) + \cdots + w_n \cdot (x_n - \bar{\text{cm}}) = 0.
~$$

The center of mass is a balance of the weighted signed distances. This property of the center of mass being a balancing point makes it of intrinsic interest and can be - in the case of sufficient symmetry - easy to find.

##### Example

A set of weights sits on a dumbbell rack. They are spaced 1 foot apart starting with the 5, then the 10-, 15-, 25-, and 35-pound weights. Where is the center of mass?

We begin by letting $m_1=5$, $m_2=10$, $m_3=15$, $m_4=25$ and $m_5=35$. Our positions will be labeled $x_i = i-1$, so the five-pound weight is at position $0$ and the $35$-pound one at $4$. The center of mass is then given by:

$$~
\frac{5\cdot 0 + 10\cdot 1 + 15 \cdot 2 + 25 \cdot 3 + 35\cdot 4}{5 + 10 + 15 + 25 + 35} = \frac{255}{90} = 2.833
~$$

If the $25$-pound weight is removed, how does the center of mass shift?

We could add the terms again, or just reduce from our sum:

$$~
\frac{255 - 25\cdot 3}{90 - 25} = \frac{180}{65} = 2.769...
~$$

The center of mass shifts slightly, but since the removed weight was already close to the center of mass, the movement wasn't much.

## Center of mass of figures

Consider now a more general problem, the center of mass of a solid figure. We will restrict our attention to figures that can be represented by functions in the $x-y$ plane which are two dimensional. For example, consider the region in the plane bounded by the $x$ axis and the function $1 - \lvert x \rvert$. This is triangle with vertices $(-1,0)$, $(0,1)$, and $(1,0)$.

This graph shows that the figure is symmetric:

````julia

using CalculusWithJulia  # loads `Plots`, `Roots`, `QuadGK`, `SymPy`
using Plots
f(x) = 1 - abs(x)
a, b = -1.5, 1.5
plot(f, a, b)
plot!(zero, a, b)
````


````
Plot{Plots.PlotlyBackend() n=2}
````





As the center of mass should be a balancing value, we would guess intuitively that the center of mass in the $x$ direction will be $x=0$.

But what should the center of mass formula be?

As with many formulas that will end up involving a derived integral, we start with a sum approximation. If the region is described as the area under the graph of $f(x)$ between $a$ and $b$, then we can form a Riemann sum approximation, that is a choice of $a = x_0 < x_1 < x_2 \cdots < x_n = b$ and points $c_1$, $\dots$, $c_n$. If all the rectangles are made up of a material of uniform density, say $\rho$, then the mass of each rectangle will be the area times $\rho$, or $\rho f(c_i) \cdot (x_i - x_{i-1})$, for $i = 1, \dots , n$.

````
Plot{Plots.PlotlyBackend() n=41}
````





The figure shows the approximating rectangles and circles representing their masses for $n=20$.


Generalizing from this figure shows the center of mass for such an approximation will be:

$$~
\frac{\rho f(c_1) (x_1 - x_0) \cdot x_1 + \rho f(c_2) (x_2 - x_1) \cdot x_1 + \cdots + \rho f(c_n) (x_n- x_{n-1}) \cdot x_{n-1}}{\rho f(c_1) (x_1 - x_0)  + \rho f(c_2) (x_2 - x_1)  + \cdots + \rho f(c_n) (x_n- x_{n-1})} =
\frac{f(c_1) (x_1 - x_0) \cdot x_1 + f(c_2) (x_2 - x_1) \cdot x_1 + \cdots + f(c_n) (x_n- x_{n-1}) \cdot x_{n-1}}{f(c_1) (x_1 - x_0)  + f(c_2) (x_2 - x_1)  + \cdots + f(c_n) (x_n- x_{n-1})}.
~$$

But the top part is an approximation to the integral $\int_a^b x f(x) dx$ and the bottom part the integral $\int_a^b f(x) dx$. The ratio of these defines the center of mass.

> **Center of Mass**: The center of mass (in the $x$ direction) of a region in the $x-y$ plane described by
> the area under a (positive) function $f(x)$ between $a$ and $b$ is given by
>
> $$~
> \text{Center of mass} = \text{cm}_x = \frac{\int_a^b xf(x) dx}{\int_a^b f(x) dx}.
> ~$$
>
> For regions described by a more complicated set of equations, the center of mass is found from the same formula where $f(x)$ is the total height in the $x$ direction for a given $x$.

For the triangular shape, we have by the fact that $f(x) = 1 - \lvert x \rvert$ is an even function that $xf(x)$ will be odd, so the integral around $-1,1$ will be $0$. So the center of mass formula applied to this problem agrees with our expectation.

##### Example

What about the center of mass of the triangle formed by the line $x=-1$, the $x$ axis and $(1-x)/2$? This too is defined between $a=-1$ and $b=-1$, but the center of mass will be negative, as a graph shows more mass to the left of $0$ than the right:

````julia

f(x) = (1-x)/2
plot(f, -1, 1)
plot!(zero, -1, 1)
````


````
Plot{Plots.PlotlyBackend() n=2}
````





The formulas give:

$$~
\int_{-1}^1 xf(x) dx = \int_{-1}^1 x\cdot (1-x)/2 = (\frac{x^2}{4} - \frac{x^3}{6})\big|_{-1}^1 = -\frac{1}{3}.
~$$

The bottom integral is just the area (or total mass if the $\rho$ were not canceled) and by geometry is $1/2 (1)(2) = 1$. So $\text{cm}_x = -1/3$.

##### Example

Find the center of mass formed by the intersection of the parabolas $y=1 - x^2$ and $y=(x-1)^2 - 2$.

The center of mass (in the $x$ direction) can be seen to be close to $x=1/2$:

````julia

f(x) = 1 - x^2
g(x) = (x-1)^2 -2
plot(f, -3, 3)
plot!(g, -3, 3)
````


````
Plot{Plots.PlotlyBackend() n=2}
````





To find it, we need to find the intersection points, then integrate. We do so numerically.

````julia

h(x) = f(x) - g(x)
a,b = find_zeros(h, -3, 3)
top, err = quadgk(x -> x*h(x), a, b)
bottom, err = quadgk(h, a, b)
cm = top / bottom
````


````
0.5000000000000001
````





Our guess from the diagram proves correct.

````
CalculusWithJulia.WeaveSupport.Alert("\nIt proves convenient to use the `->
` notation for an anonymous function above, as our function `h` is not what
 is being integrated all the time, but some simple modification. If this is
n't palatable, a new function could be defined and passed along to `quadgk`
.\n\n", Dict{Any,Any}(:class => "info"))
````





##### Example

Consider a region bounded by a probability density function. (These functions are non-negative, and integrate to $1$.) The center of mass formula simplifies to $\int xf(x) dx$, as the denominator will be $1$, and the answer is called the *mean*, and often denoted by the Greek letter $\mu$.

For the probability density $f(x) = e^{-x}$ for $x\geq 0$ and $0$ otherwise, find the mean.

We need to compute $\int_{-\infty}^\infty xf(x) dx$, but in this case since $f$ is $0$ to the left of the origin, we just have:

$$~
\mu = \int_0^\infty x e^{-x} dx = -(1+x) \cdot e^{-x} \big|_0^\infty = 1
~$$

For fun, we compare this to the median, which is the value $M$ so that the total area is split in half. That is,  the following formula is satisfied: $\int_0^M f(x) dx = 1/2$. To compute, we have:

$$~
\int_0^M e^{-x} dx = -e^{-x} \big|_0^M = 1 - e^{-M}.
~$$

Solving $1/2 = 1 - e^{-M}$ gives $M=\log(2) = 0.69...$, The median is to the left of the mean in this example.

````
CalculusWithJulia.WeaveSupport.Alert("\nIn this example, we used an infinit
e region, so the idea of \"balancing\" may be a bit unrealistic, nonetheles
s, this intuitive interpretation is still a good one to keep this in mind. 
The point of comparing to the median is that the balancing point is to the 
right of where the area splits in half. Basically, the center of mass follo
ws in the direction of the area far to the right of the median, as this are
a is skewed in that direction.\n\n", Dict{Any,Any}(:class => "info"))
````





##### Example

A figure is formed by transformations of the function $\phi(u) = e^{2(k-1)} - e^{2(k-u)}$, for some fixed $k$, as follows:

````julia

k = 3
phi(u) = exp(2(k-1)) - exp(2(k-u))
f(u) = max(0, phi(u))
g(u) = min(f(u+1), f(k))

plot(f, 0, k)
plot!(g, 0, k)
plot!(zero, 0, k)
````


````
Plot{Plots.PlotlyBackend() n=3}
````





(This is basically the graph of $\phi(u)$ and the graph of its shifted value $\phi(u+1)$, only truncated on the top and bottom.)

The center of mass of this figure is found with:

````julia

h(x) = g(x) - f(x)
top, _ = quadgk(x -> x*h(x), 0, k)
bottom, _ = quadgk(h, 0, k)
top/bottom
````


````
0.9626852772498595
````





This figure has constant slices of length $1$ for fixed values of $y$. If we were to approximate the values with blocks of height $1$, then the center of mass would be to the left of $1$ - for any $k$, but the top most block would have an overhang to the right of $1$ - out to a value of $k$. That is, this figure should balance:

````
Plot{Plots.PlotlyBackend() n=56}
````





See this [paper](https://math.dartmouth.edu/~pw/papers/maxover.pdf) and its references for some background on this example and its extensions.

## The $y$ direction.

We can talk about the center of mass in the $y$ direction too. The approximating picture uses horizontal rectangles - not vertical ones - and if we describe them by $f(y)$, then the corresponding formulas would be

$$~
\text{center of mass} = \text{cm}_y = \frac{\int_a^b y f(y) dy}{\int_a^b f(y) dy}.
~$$

For example, consider, again, the triangle bounded by the line $x=-1$, the $x$ axis, and the line $y=(1-x)/2$. In terms of describing this in $y$, the function $f(y)=2 -2y$ gives the total length of the horizontal slice (which comes from solving $y=(x-1)/2$for $x$, the general method to find an inverse function, and subtracting $-1$) and the interval is $y=0$ to $y=1$. Thus our center of mass in the $y$ direction will be

$$~
\text{cm}_y = \frac{\int_0^1 y (2 - 2y) dy}{\int_0^1 (2 - 2y) dy} = \frac{(2y^2/2 - 2y^3/3)\big|_0^1}{1} = \frac{1}{3}.
~$$

Here the center of mass is below $1/2$ as the bulk of the area is. (The bottom area is just $1$, as known from the area of a triangle.)

As seen, the computation of the center of mass in the $y$ direction has an identical formula, though may be more involved if an inverse function must be computed.

##### Example

More generally, consider a right triangle with vertices $(0,0)$, $(0,a)$, and $(b,0)$. The center of mass of this can be computed with the help of the equation for the line that forms the hypotenuse: $x/b + y/a = 1$. We find the center of mass symbolically in the $y$ variable by solving for $x$ in terms of $y$, then integrating from $0$ to $a$:

````julia

@vars a b x y
eqn = x/b + y/a - 1
fy = solve(eqn, x)[1]
integrate(y*fy, (y, 0, a)) / integrate(fy, (y, 0, a))
````


````
a
─
3
````





The answer involves $a$ linearly, but not $b$. If we find the center of mass in $x$, we *could* do something similar:

````julia

fx = solve(eqn, y)[1]
integrate(x*fx, (x, 0, b)) / integrate(fx, (x, 0, b))
````


````
b
─
3
````





But really, we should have just noted that simply by switching the labels $a$ and $b$ in the diagram we could have discovered this formula.

````
CalculusWithJulia.WeaveSupport.Alert(L"
The [centroid](http://en.wikipedia.org/wiki/Centroid) of a region in the pl
ane is just $(\text{cm}_x, \text{cm}_y)$. This last fact says the centroid 
of the right triangle  is just $(b/3, a/3)$. The centroid can be found by o
ther geometric means. The link shows the plumb line method. For triangles, 
the centroid is also the intersection point of the medians, the lines that 
connect a vertex with its opposite midpoint.

", Dict{Any,Any}(:class => "info"))
````





##### Example

Compute the $x$ and $y$ values of the center of mass of the half circle described by the area below the function $f(x) = \sqrt{1 - x^2}$ and above the $x$-axis.

A plot shows the value of cm$_x$ will be $0$ by symmetry:

````julia

f(x) = sqrt(1 - x^2)
plot(f, -1, 1)
````


````
Plot{Plots.PlotlyBackend() n=1}
````





($f(x)$ is even, so $xf(x)$ will be odd.)

However, the value for cm$_y$ will - like the last problem - be around $1/3$. The exact value comes from noting that our slices in the $y$ direction come from solving for $x$ in $y=\sqrt{1-x^2}$, or $x = \pm \sqrt{1-y^2}$, if $f(y) = 2\sqrt{1 - y^2}$. The value is then:

$$~
\text{cm}_y = \frac{\int_{0}^1 y 2 \sqrt{1 - y^2}dy}{\int_{0}^1 2\sqrt{1-y^2}} =
\frac{-(1-x^2)^{3/2}/3\big|_0^1}{\pi/2} = \frac{1}{3}.
~$$

In fact it is exactly $1/3$. The top calculation is done by $u$-substitution, the bottom by using the area formula for a half circle, $\pi r^2/2$.

##### Example

A disc of radius $2$ is centered at the origin, as a disc of radius $1$ is bored out between $y=0$ and $y=1$. Find the resulting center of mass.

A picture shows that this could be complicated, especially for $y > 0$, as we need to describe the length of the red lines below for $-2 < y < 2$:

````
Plot{Plots.PlotlyBackend() n=5}
````





We can see that cm$_x = 0$, by symmetry, but to compute cm$_y$ we need
to find $f(y)$, which will depend on the value of $y$ between $-2$ and
$2$. The outer circle is $x^2 + y^2 = 4$, the inner circle $x^2 +
(y-1)^2 = 1$.  When $y < 0$, $f(y)$ is the distance across the outer
circle or, $2\sqrt{4 - y^2}$. When $y \geq 0$, $f(y)$ is *twice* the
distance from the bigger circle to the smaller, of $2(\sqrt{4 - y^2} -
\sqrt{1 - (y-1)^2})$.

We use this to compute:

````julia

f(y) = y < 0 ? 2*sqrt(4 - y^2) : 2* (sqrt(4 - y^2)- sqrt(1 - (y-1)^2))
top, _ = quadgk( y -> y * f(y), -2, 2)
bottom, _ = quadgk( f, -2, 2)
top/bottom
````


````
-0.3333333333594305
````





The nice answer of $-1/3$ makes us think there may be a different way to visualize this. Were we to rearrange the top integral, we could write it as $\int_{-2}^2 y 2 \sqrt{4 -y^2}dy - \int_0^2 2y\sqrt{1 - (y-1)^2}dy$. Call this $A - B$.  The left term, $A$, is part of the center of mass formula for the big circle (which is this value divided by $M=4\pi$), and the right term, $B$, is part of the center of mass formula for the (drilled out) smaller circle (which is this value divided by $m=\pi$. These values are weighted according to $(AM - Bm)/(M-m)$. In this case $A=0$, $B=1$ and $M=4m$, so the answer is $-1/3$.

## Questions

###### Question

Find the center of mass in the $x$ variable for the region bounded by parabola $x=4 - y^2$  and the $y$ axis.

````
CalculusWithJulia.WeaveSupport.Numericq(1.5999999998855376, 0.001, "", "[1.
599, 1.601]", 1.5989999998855378, 1.6009999998855375, "", "")
````






###### Question

Find the center of mass in the $x$ variable of the region in the first and fourth quadrants bounded by the ellipse $(x/2)^2 + (y/3)^2 = 1$.

````
CalculusWithJulia.WeaveSupport.Numericq(0.8488263630519628, 0.001, "", "[0.
84783, 0.84983]", 0.8478263630519628, 0.8498263630519628, "", "")
````





###### Question

Find the center of mass of the region in the first quadrant bounded by the function $f(x) = x^3(1-x)^4$.

````
CalculusWithJulia.WeaveSupport.Numericq(0.4444444444444445, 0.001, "", "[0.
44344, 0.44544]", 0.4434444444444445, 0.4454444444444445, "", "")
````





###### Question

Let $k$ and $\lambda$ be parameters in $(0, \infty)$. The [Weibull](http://en.wikipedia.org/wiki/Weibull_distribution) density is a probability density on $[0, \infty)$ (meaning it is $0$ when $x < 0$ satisfying:

$$~
f(x) = \frac{k}{\lambda}(\frac{x}{\lambda})^{k-1} e^{-(x/\lambda)^k}
~$$

For $k=2$ and $\lambda = 2$, compute the mean. (The center of mass, assuming the total area is $1$.)

````
CalculusWithJulia.WeaveSupport.Numericq(1.7724538509055145, 0.001, "", "[1.
77145, 1.77345]", 1.7714538509055147, 1.7734538509055144, "", "")
````





###### Question

The [logistic](http://en.wikipedia.org/wiki/Logistic_distribution) density depends on two parameters $m$ and $s$ and is given by:

$$~
f(x) = \frac{1}{4s} \text{sech}(\frac{x-\mu}{2s})^2, \quad -\infty < x < \infty.
~$$

(Where $\text{sech}$ is the hyperbolic secant, implemented in `julia` through `sech`.)

For $m=2$ and $s=4$ compute the mean, or center of mass, of this density.

````
CalculusWithJulia.WeaveSupport.Numericq(2.0000000000002287, 0.001, "", "[1.
999, 2.001]", 1.9990000000002288, 2.0010000000002286, "", "")
````





###### Question

A region is formed by intersecting the area bounded by the circle $x^2 + y^2 = 1$ that lies above the line $y=3/4$. Find the center of mass in the $y$ direction (that of the $x$ direction is $0$ by symmetry).

````
CalculusWithJulia.WeaveSupport.Numericq(0.8511553682895965, 0.001, "", "[0.
85016, 0.85216]", 0.8501553682895965, 0.8521553682895965, "", "")
````





###### Question

Find the center of mass in the $y$ direction of the area bounded by the cosine curve and the $x$ axis between $-\pi/2$ and $\pi/2$.

````
CalculusWithJulia.WeaveSupport.Numericq(0.392699081675986, 0.001, "", "[0.3
917, 0.3937]", 0.391699081675986, 0.393699081675986, "", "")
````





###### Question

A penny, nickel, dime and quarter are stacked so that their right most edges align and are centered so that the center of mass in the $y$ direction is $0$. Find the center of mass in the $x$ direction.

````
Plot{Plots.PlotlyBackend() n=4}
````





You will need some specifications, such as these from the [US Mint](http://www.usmint.gov/about_the_mint/?action=coin_specifications)

```
         diameter(in)   weight(gms)
penny     0.750           2.500
nickel    0.835           5.000
dime      0.705           2.268
quarter   0.955           5.670

```

(Hint: Though this could be done with integration, it is easier to treat each coin as a single point (its centroid) with the given mass and then apply the formula for sums.)

````
CalculusWithJulia.WeaveSupport.Numericq(0.05439499935224769, 0.001, "", "[0
.05339, 0.05539]", 0.05339499935224769, 0.05539499935224769, "", "")
````

