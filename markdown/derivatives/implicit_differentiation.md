# Implicit Differentiation




## Graphs of equations


An **equation** in $y$ and $x$ is an algebraic expression involving an
equality with two (or more) variables. An example might be $x^2 + y^2
= 1$.

The **solutions** to an equation in the variables $x$ and $y$ are all
points $(x,y)$ which satisfy the equation.

The **graph** of an equation is just the set of solutions to the equation represented in the Cartesian plane.

With this definition, the graph of a function $f(x)$ is just the graph of the equation $y = f(x)$.

In general, graphing an equation is more complicated than graphing a
function. For a function, we know for a given value of $x$ what
the corresponding value of $f(x)$ is through evaluation of the function. For
equations, we may have 0, 1 or more $y$ values for a given $x$ and
even more problematic is we may have no rule to find these values.


To plot such an equation in `Julia`, we can use the
`ImplicitEquations` package, which is loaded when `CalculusWithJulia` is:

````julia

using CalculusWithJulia 
using Plots
gr()   # better graphics than plotly() here
````


````
Plots.GRBackend()
````





To plot the circle of radius $2$, we would first define a function of *two* variables:

````julia

f(x,y) = x^2 + y^2
````


````
f (generic function with 1 method)
````



````
CalculusWithJulia.WeaveSupport.Alert(L"
This is a function of *two* variables, used here to express one side of an 
equation. `Julia` makes this easy to do - just make sure two variables are 
in the signature of `f` when it is defined. Using functions like this, we c
an express our equation in the form $f(x,y) = c$ or $f(x,y) = g(x,y)$, the 
latter of which can be expressed as $h(x,y) = f(x,y) - g(x,y) = 0$. That is
, only the form $f(x,y)=c$ is needed.

", Dict{Any,Any}(:class => "info"))
````






Then we use one of the logical operations - `Lt`, `Le`, `Eq`, `Ge`, or `Gt` - to construct a predicate to plot. This one describes $x^2 + y^2 = 2^2$:

````julia

r = Eq(f, 2^2)
````


````
ImplicitEquations.Pred(Main.##WeaveSandBox#682.f, ==, 4)
````



````
CalculusWithJulia.WeaveSupport.Alert("There are unicode infix operators for
 each of these which make it\neasier to read at the cost of being harder to
 type in. This predicate\nwould be written as `f ⩵ 2^2` where `⩵` is **not*
* two equals signs,\nbut rather typed with `\\Equal[tab]`.)\n", Dict{Any,An
y}(:class => "info"))
````





These "predicate" objects can be passed to `plot` for visualization:

````julia

plot(r)
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_7_1.png)




Of course, more complicated equations are possible and the steps are
similar - only the function definition is more involved.  For example,
the [Devils
curve](http://www-groups.dcs.st-and.ac.uk/~history/Curves/Devils.html)
has the form

$$~
y^4 - x^4 + ay^2 + bx^2 = 0
~$$

Here we draw the curve for a particular choice of $a$ and $b$. For illustration purposes, a narrower
viewing window than the default of $[-5,5] \times [-5,5]$ is specified below using `xlims` and `ylims`:

````julia

a,b = -1,2
f(x,y) =  y^4 - x^4 + a*y^2 + b*x^2
plot(Eq(f, 0), xlims=(-3,3), ylims=(-3,3))
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_8_1.png)

````
CalculusWithJulia.WeaveSupport.Alert(L"
The rendered plots look \"blocky\" due to the algorithm used to plot the
equations. As there is no rule defining $(x,y)$ pairs to plot, a
search by regions is done. A region is initially labeled
undetermined. If it can be shown that for any value in the region the
equation is true (equations can also be inequalities), the region is
colored black. If it can be shown it will never be true, the region is
dropped. If a black-and-white answer is not clear, the region is
subdivided and each subregion is similarly tested. This continues
until the remaining undecided regions are smaller than some
threshold. Such regions comprise a boundary, and here are also colored
black. Only regions are plotted - not $(x,y)$ pairs - so the results
are blocky. Pass larger values of $N=M$ (with defaults of $8$) to
`plot` to lower the threshold at the cost of longer computation times.

", Dict{Any,Any}(:class => "info"))
````





### The IntervalConstraintProgramming package

The `IntervalConstraintProgramming` package also can be used to graph implicit equations. For certain problem descriptions it is significantly faster and makes better graphs. The usage is slightly more involved:

We specify a problem using the `@constraint` macro. Using a macro
allows expressions to involve free symbols, so the problem is
specified in an equation-like manner:

````julia

using IntervalArithmetic, IntervalConstraintProgramming
S = @constraint x^2 + y^2 <= 2
````


````
Separator:
  - variables: x, y
  - expression: x ^ 2 + y ^ 2 ∈ [-∞, 2]
````





The right hand side must be a number.

The area to plot over must be specified as an `IntervalBox`, basically a pair of intervals. The interval $[a,b]$ is expressed through `a..b`.

````julia

X = IntervalBox(-3..3, -3..3)
````


````
[-3, 3] × [-3, 3]
````



````julia

r = pave(S, X)
````


````
Paving:
- tolerance ϵ = 0.01
- inner approx. of length 1152
- boundary approx. of length 1156
````





We can plot either the boundary, the interior, or both.

````julia

plot(r.inner)       # plot interior; use r.boundary for boundary
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_13_1.png)



## Tangent lines, implicit differentiation


The graph $x^2 + y^2 = 1$ has well-defined tangent lines at all points except
$(-1,0)$ and $(0, 1)$ and even at these two points, we could call the vertical lines
$x=-1$ and $x=1$ tangent lines. However, to recover the slope would
need us to express $y$ as a function of $x$ and then differentiate
that function. Of course, in this example, we would need two functions:
$f(x) = \sqrt{1-x^2}$ and $g(x) = - \sqrt{1-x^2}$ to do this
completely.

In general though, we may not be able to solve for $y$ in terms of $x$. What then?

The idea is to *assume* that $y$ is representable by some function of
$x$. This makes sense, moving on the curve from $(x,y)$ to some nearby
point, means changing $x$ will cause some change in $y$. This
assumption is only made *locally* - basically meaning a complicated
graph is reduced to just a small, well-behaved, section of its graph.


With this assumption, asking what $dy/dx$ is has an obvious meaning -
what is the slope of the tangent line to the graph at $(x,y)$.

The method of implicit differentiation allows this question to be
investigated. It begins by differentiating both sides of the equation
assuming $y$ is a function of $x$ to derive a new equation involving $dy/dx$.

For example,  starting with $x^2 + y^2 = 1$, differentiating both sides in $x$ gives:

$$~
2x + 2y\cdot \frac{dy}{dx} = 0.
~$$

The chain rule was used to find $d/dx(y^2) = 2y \cdot dy/dx$. From this we can solve for $dy/dx$ (the resulting equations are linear in $dy/dx$, so can always be solved explicitly):

$$~
dy/dx = -x/y
~$$

This says the slope of the tangent line depends on the point $(x,y)$ through the formula $-x/y$.


As a check, we compare to what we would have found had we solved for
$y= \sqrt{1 - x^2}$ (for $(x,y)$ with $y \geq 0$). We would have
found: $dy/dx = 1/2 \cdot 1/\sqrt{1 - x^2} \cdot -2x$. Which can be
simplified to $-x/y$. This should show that the method
above - assuming $y$ is a function of $x$ and differentiating - is not
only more general, but can even be easier.

The name - *implicit differentiation* - comes from the assumption that
$y$ is implicitly defined in terms of $x$.  According to the
[Implicit Function Theorem](http://en.wikipedia.org/wiki/Implicit_function_theorem) the
above method will work provided the curve has sufficient smoothness
near the point $(x,y)$.

##### Examples

Consider the [serpentine](http://www-history.mcs.st-and.ac.uk/Curves/Serpentine.html) equation

$$~
x^2y + a\cdot b \cdot y - a^2 \cdot x = 0, \quad a\cdot b > 0.
~$$

For $a = 2, b=1$ we have the graph:

````julia

a, b = 2, 1
f(x,y) = x^2*y + a * b * y - a^2 * x
plot(Eq(f, 0))
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_14_1.png)



We can see that at each point in the viewing window the tangent line
exists due to the smoothness of the curve. Moreover, at a point
$(x,y)$ the tangent will have slope $dy/dx$ satisfying:

$$~
2xy + x^2 \frac{dy}{dx} + a\cdot b \frac{dy}{dx} - a^2 = 0.
~$$

Solving, yields:

$$~
\frac{dy}{dx} = \frac{a^2 - 2xy}{ab + x^2}.
~$$


In particular, the point $(0,0)$ is always on this graph, and the tangent line will have positive slope $a^2/(ab) = a/b$.

----

The [eight](http://www-history.mcs.st-and.ac.uk/Curves/Eight.html) curve has representation

$$~
x^4 = a^2(x^2-y^2), \quad a \neq 0.
~$$


A graph for $a=3$ shows why it has the name it does:

````julia

a = 3
f(x,y) = x^4 - a^2*(x^2 - y^2)
plot(Eq(f, 0))
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_15_1.png)



The tangent line at $(x,y)$ will have slope, $dy/dx$ satisfying:

$$~
4x^3 = a^2 \cdot (2x - 2y \frac{dy}{dx}).
~$$

Solving gives:

$$~
\frac{dy}{dx} = -\frac{4x^3 - a^2 \cdot 2x}{a^2 \cdot 2y}.
~$$

The point $(3,0)$ can be seen to be a solution to the equation and
should have a vertical tangent line. This also is reflected in the
formula, as the denominator is $a^2\cdot 2 y$, which is $0$ at this point, whereas the numerator is not.


##### Example

The quotient rule can be hard to remember, unlike the product rule. No
reason to despair, the product rule plus implicit differentiation can
be used to recover the quotient rule. Suppose $y=f(x)/g(x)$, then we
could also write $y g(x) = f(x)$. Differentiating implicitly gives:

$$~
\frac{dy}{dx} g(x) + y g'(x) = f'(x).
~$$

Solving for $dy/dx$ gives:

$$~
\frac{dy}{dx} = \frac{f'(x) - y g'(x)}{g(x)}.
~$$

Not quite what we expect, perhaps, but substituting in $f(x)/g(x)$ for $y$ gives us the usual formula:


$$~
\frac{dy}{dx} = \frac{f'(x) - \frac{f(x)}{g(x)} g'(x)}{g(x)} = \frac{f'(x) g(x) - f(x) g'(x)}{g(x)^2}.
~$$

````
CalculusWithJulia.WeaveSupport.Alert(L"
In this example we mix notations using $g'(x)$ to
represent a derivative of $g$ with respect to $x$ and $dy/dx$ to
represent the derivative of $y$ with respect to $x$. This is done to
emphasize the value that we are solving for. It is just a convention
though, we could just as well have used the \"prime\" notation for each.

", Dict{Any,Any}(:class => "info"))
````






##### Example: Graphing a tangent line

Let's see how to add a graph of a tangent line to the graph of an
equation. Tangent lines are tangent at a point, so we need a point to
discuss.

Returning to the equation for a circle, $x^2 + y^2 = 1$, let's look at
$(\sqrt{2}/2, - \sqrt{2}/2)$. The derivative is $ -y/x$, so the slope
at this point is $1$. The line itself has equation $y = b + m \cdot
(x-a)$. The following represents this in `Julia`:

````julia

F(x,y) = x^2 + y^2

a,b = sqrt(2)/2, -sqrt(2)/2

m = -a/b
tl(x) = b + m * (x-a)
````


````
tl (generic function with 1 method)
````





Now we want to plot *both* $F ⩵ 1$ and the tangent line. This can be done with two layers:

````julia

f(x,y) = x^2 + y^2
plot(Eq(f, 1), xlims=(-2, 2), ylims=(-2, 2))
plot!(tl)
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_18_1.png)




##### Example

When we assume $y$ is a function of $x$, it may not be feasible to
actually find the function algebraically. However, in many cases one
can be found numerically. Suppose $F(x,y) = c$ describes the
equation. Then for a fixed $x$, $y(x)$ solves $F(x,y(x))) - c = 0$, so
$y(x)$ is a zero of a known function. As long as we can piece together
which $y$ goes with which, we can find the function.

For example, [folium](http://www-history.mcs.st-and.ac.uk/Curves/Foliumd.html) of Descartes has the equation

$$~
x^3 + y^3  = 3axy.
~$$

Setting $a=1$ we have the graph:

````julia

a = 1
F(x,y) = x^3 + y^3 - 3*a*x*y
plot(Eq(F,0))
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_19_1.png)



We can solve for the lower curve, $y$, as a function of $x$, as follows:

````julia

y1(x) = minimum(find_zeros(y->F(x,y), -10, 10))  # find_zeros from `Roots`
````


````
y1 (generic function with 1 method)
````





This gives the lower part of the curve, which we can plot with:

````julia

plot(y1, -5, 5)
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_21_1.png)



Though, in this case, the cubic equation would admit a closed-form solution, the approach illustrated applies more generally.


## Using SymPy for computation

`SymPy` can be used to perform implicit differentiation. The three
steps are similar: we assume $y$ is a function of $x$, *locally*;
differentiate both sides; solve the result for $dy/dx$.


Let's do so for the [Trident of
Newton](http://www-history.mcs.st-and.ac.uk/Curves/Trident.html), which
is represented in Cartesian form as follows:

$$~
xy = cx^3 + dx^2 + ex + h.
~$$



To approach this task in `SymPy`, we begin by defining our symbolic expression. For now, we keep the parameters as symbolic values:

````julia

@vars a b c d x y
ex = x*y - (a*c^3 + b*x^2 + c*x + d)
````


````
3      2                
- a⋅c  - b⋅x  - c⋅x - d + x⋅y
````





To express that `y` is a locally a function of `x`, we use a "symbolic function" object:

````julia

u = SymFunction("u")
````


````
u
````





Defining a symbolic function is done with the command `SymFunction`. (This
command's name is slightly modified from that when using SymPy under `Python`.) The object
`u` is the symbolic function, and `u(x)` a symbolic expression
involving a symbolic function.  This is what we will use to refer to `y`.


Assume $y$ is a function of $x$, called `u(x)`, this substitution is just a renaming:

````julia

ex1 = ex(y => u(x))
````


````
3      2                   
- a⋅c  - b⋅x  - c⋅x - d + x⋅u(x)
````





At this point,  we differentiate both sides in `x`:

````julia

ex2 = diff(ex1, x)
````


````
d              
-2⋅b⋅x - c + x⋅──(u(x)) + u(x)
               dx
````





The next step is solve for $dy/dx$ - the lone answer to the linear equation - which is done as follows:

````julia

dydx = diff(u(x), x)
ex3 = solve(ex2, dydx)[1]    # pull out lone answer with [1] indexing
````


````
2⋅b⋅x + c - u(x)
────────────────
       x
````





As this represents an answer in terms of `u(x)`, we replace that term with the original variable:

````julia

dydx = ex3(u(x) => y)
````


````
2⋅b⋅x + c - y
─────────────
      x
````





If `x` and `y` are the variable names, this function will combine the steps above:

````julia

function dy_dx(eqn)
  u = SymFunction("u")
  eqn1 = eqn(y => u(x))
  eqn2 = solve(diff(eqn1, x), diff(u(x), x))[1]
  eqn2(u(x) => y)
end
````


````
dy_dx (generic function with 1 method)
````






Let $a = b = c = d = 1$, then $(1,4)$ is a point on the curve. We can draw a tangent line to this point with these commands:

````julia

H = ex(a=>1, b=>1, c=>1, d=>1)
x0, y0 = 1, 4
m = dydx(x=>1, y=>4, a=>1, b=>1, c=>1, d=>1)
plot(Eq(lambdify(H), 0), xlims=(-5,5), ylims=(-5,5))
plot!(y0 + m * (x-x0))
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_29_1.png)



Basically all the same steps as if done "by hand." Some effort could have been saved in plotting, had
values for the parameters been substituted initially, but not doing so
shows their dependence in the derivative.

````
CalculusWithJulia.WeaveSupport.Alert("The use of `lambdify` is needed to tu
rn the symbolic expression, `H`, into a function, as `ImplicitEquations` ex
pects functions in its predicates.", Dict{Any,Any}())
````



````
CalculusWithJulia.WeaveSupport.Alert("\nWhile `SymPy` itself has the `plot_
implicit` function for plotting implicit equations,  this works only with `
PyPlot`, not `Plots`, so we use the `ImplicitEquations` package in these ex
amples.\n\n", Dict{Any,Any}(:class => "info"))
````






## Higher order derivatives

Implicit differentiation can be used to find $d^2y/dx^2$ or other higher-order derivatives. At each stage, the same technique is applied. The
only "trick" is that some simplifications can be made.

For example, consider $x^3 - y^3=3$. To find $d^2y/dx^2$, we first find $dy/dx$:

$$~
3x^2 - (3y^2 \frac{dy}{dx}) = 0.
~$$

We could solve for $dy/dx$ at this point - it always appears as a linear factor - to get:

$$~
\frac{dy}{dx} = \frac{3x^2}{3y^2} = \frac{x^2}{y^2}.
~$$

However, we differentiate the first equation, as we generally try to avoid the quotient rule

$$~
6x - (6y \frac{dy}{dx} \cdot \frac{dy}{dx} + 3y^2 \frac{d^2y}{dx^2}) = 0.
~$$

Again, if must be that $d^2y/dx^2$ appears as a linear factor, so we can solve for it:

$$~
\frac{d^2y}{dx^2} = \frac{6x - 6y (\frac{dy}{dx})^2}{3y^2}.
~$$

One last substitution for $dy/dx$ gives:

$$~
\frac{d^2y}{dx^2} = \frac{-6x + 6y (\frac{x^2}{y^2})^2}{3y^2} = -2\frac{x}{y^2} + 2\frac{x^4}{y^5} = 2\frac{x}{y^2}(1 - \frac{x^3}{y^3}) = 2\frac{x}{y^5}(y^3 - x^3) = 2 \frac{x}{y^5}(-3).
~$$

It isn't so pretty, but that's all it takes.



To visualize, we plot implicitly and notice that:

* as we change quadrants from the third to the fourth to the first the
  concavity changes from down to up to down, as the sign of the second
  derivative changes from negative to positive to negative;

* and that at these inflection points, the "tangent" line is vertical
  when $y=0$ and flat when $x=0$.

````julia

F(x,y) = x^3 - y^3
plot(Eq(F, 3),  xlims=(-3, 3), ylims=(-3, 3))
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_32_1.png)




The same problem can be done symbolically. The steps are similar, though the last step (replacing $x^3 - y^3$ with $3$) isn't done without explicitly asking.

````julia

@vars x y
u = SymFunction("u")

eqn    = F(x,y) - 3
eqn1   = eqn(y => u(x))
dydx   = solve(diff(eqn1,x), diff(u(x), x))[1]        # 1 solution
d2ydx2 = solve(diff(eqn1, x, 2), diff(u(x),x, 2))[1]  # 1 solution
eqn2   = d2ydx2(diff(u(x), x) => dydx, u(x) => y)
simplify(eqn2)
````


````
⎛   3    3⎞
2⋅x⋅⎝- x  + y ⎠
───────────────
        5      
       y
````





## Inverse functions

As [mentioned](../precalc/inversefunctions.html), an [inverse](http://en.wikipedia.org/wiki/Inverse_function) function for $f(x)$ is a function $g(x)$ satisfying:
$y = f(x)$ if and only if $g(y) = x$ for all $x$ in the domain of $f$ and $y$ in the range of $f$.

In short, both $f \circ g$ and $g \circ f$ are identify functions on their respective domains.
As inverses are unique, their notation, $f^{-1}(x)$, reflects the name of the related function.
function.

The chain rule can be used to give the derivative of an inverse
function when applied to $f(f^{-1}(x)) = x$. Solving gives,
$[f^{-1}(x)]' = 1 / f'(g(x))$.

This is great - if we can remember the rules. If not, sometimes implicit
differentiation can also help.

Consider the inverse function for the tangent, which exists when the domain of the tangent function is restricted to $(-\pi/2, \pi/2)$. The function solves $y = \tan^{-1}(x)$ or $\tan(y) = x$. Differentiating this yields:

$$~
\sec(y)^2 \frac{dy}{dx} = 1.
~$$

But $\sec(y)^2 = 1 + x^2$, as can be seen by right-triangle trigonometry. This yields the formula $dy/dx = [\tan^{-1}(x)]' = 1 / (1 + x^2)$.

##### Example

For a more complicated example, suppose we have a moving trajectory $(x(t),
y(t))$. The angle it makes with the origin satisfies

$$~
\tan(\theta(t)) = \frac{y(t)}{x(t)}.
~$$

Suppose $\theta(t)$ can be defined in terms of the inverse to some
function ($\tan^{-1}(x)$). We can differentiate implicitly to find $\theta'(t)$ in
terms of derivatives of $y$ and $x$:

$$~
\sec^2(\theta(t)) \cdot \theta'(t) = \frac{y'(t) x(t) - y(t) x'(t)}{x(t))^2}.
~$$

But $\sec^2(\theta(t)) = (r(t)/x(t))^2 = (x(t)^2 + y(t)^2) / x(t)^2$, so moving to the other side the secant term gives an explicit, albeit complicated, expression for the derivative of $\theta$ in terms of the functions $x$ and $y$:

$$~
\theta'(t) = \frac{x^2}{x^2(t) + y^2(t)} \cdot \frac{y'(t) x(t) - y(t) x'(t)}{x(t))^2} = \frac{y'(t) x(t) - y(t) x'(t)}{x^2(t) + y^2(t)}.
~$$

This could have been made easier, had we leveraged the result of the previous example.

## Example from physics

Many problems are best done with implicit derivatives. A video showing
such a problem along with how to do it analytically is
[here](http://ocw.mit.edu/courses/mathematics/18-01sc-single-variable-calculus-fall-2010/unit-2-applications-of-differentiation/part-b-optimization-related-rates-and-newtons-method/session-32-ring-on-a-string/).

This video starts with a simple question:


> If you have a rope and heavy ring, where will the ring position itself
> due to gravity?


Well, suppose you hold the rope in two places, which we can take to be
$(0,0)$ and $(a,b)$. Then let $(x,y)$ be all the possible positions of
the ring that hold the rope taught. Then we have this picture:


````
CalculusWithJulia.WeaveSupport.ImageFile("figures/extrema-ring-string.png",
 "Ring on string figure.")
````






Since the length of the rope does not change, we must have for any admissible $(x,y)$ that:

$$~
L = \sqrt{x^2 + y^2} + \sqrt{(a-x)^2 + (b-y)^2},
~$$

where these terms come from  the two hypotenuses in the figure, as computed through
Pythagorean's theorem.


> If we assume that the ring will minimize the value of y subject to
> this constraint, can we solve for y?


We create a function to represent the equation:

````julia

F(x, y, a, b) = sqrt(x^2 + y^2) + sqrt((a-x)^2 + (b-y)^2)
````


````
F (generic function with 2 methods)
````





To illustrate, we need specific values of $a$, $b$, and $L$:
````julia

a, b, L = 3, 3, 10      # L > sqrt{a^2 + b^2}
F(x, y) = F(x, y, a, b)
````


````
F (generic function with 2 methods)
````





Our values $(x,y)$ must satisfy $f(x,y) = L$. Let's graph:

````julia

plot(Eq(F, L), xlims=(-5, 7), ylims=(-5, 7))
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_37_1.png)



The graph is an ellipse, though slightly tilted.

Okay, now to find the lowest point. This will be when the derivative
is $0$. We solve by assuming $y$ is a function of $x$ called `u`:

````julia

@vars a b L x y
u = SymFunction("u")
````


````
u
````



````julia

F(x,y,a,b) = sqrt(x^2 + y^2) + sqrt((a-x)^2 + (b-y)^2)
eqn = F(x,y,a,b) - L
````


````
_________      _____________________
       ╱  2    2      ╱        2          2 
-L + ╲╱  x  + y   + ╲╱  (a - x)  + (b - y)
````



````julia

eqn1 = diff(eqn(y => u(x)), x)
eqn2 = solve(eqn1, diff(u(x), x))[1]
dydx  = eqn2(u(x) => y)
````


````
_________        _________        _____________________ 
     ╱  2    2        ╱  2    2        ╱        2          2  
 a⋅╲╱  x  + y   - x⋅╲╱  x  + y   - x⋅╲╱  (a - x)  + (b - y)   
──────────────────────────────────────────────────────────────
       _________        _________        _____________________
      ╱  2    2        ╱  2    2        ╱        2          2 
- b⋅╲╱  x  + y   + y⋅╲╱  x  + y   + y⋅╲╱  (a - x)  + (b - y)
````





We are looking for when the tangent line has 0 slope, or when `dydx` is 0:

````julia

cps = solve(dydx, x)
````


````
2-element Array{Sym,1}:
          a*y/b
 a*y/(-b + 2*y)
````





There are two answers, as we could guess from the graph, but we want the one for the smallest value of $y$, which is the second.

The values of dydx depend on any pair (x,y), but our solution must
also satisfy the equation. That is for our value of x, we need to find
the corresponding y. This should be possible by substituting:

````julia

eqn1 = eqn(x => cps[2])
````


````
__________________        ______________________________
          ╱     2  2                ╱                            2 
         ╱     a ⋅y        2       ╱         2   ⎛    a⋅y       ⎞  
-L +    ╱   ─────────── + y   +   ╱   (b - y)  + ⎜- ──────── + a⎟  
       ╱              2         ╲╱               ⎝  -b + 2⋅y    ⎠  
     ╲╱     (-b + 2⋅y)
````





We would try to solve `eqn1` for `y` with `solve(eqn1, y)`, but
`SymPy` can't complete this problem. Instead, we will approach this
numerically using `find_zero` from the `Roots` package. We make the above a function of `y` alone

````julia

eqn2 = eqn1(a=>3, b=>3, L=>10)
ystar = find_zero(eqn2, -3)
````


````
-3.269696007084728
````





Okay, now we need to put this value back into our expression for the `x` value and also substitute in for the parameters:

````julia

xstar = N(cps[2](y => ystar, a =>3, b => 3, L => 3))
````


````
1.0282718234751367
````





Our minimum is at `(xstar, ystar)`, as this graphic shows:

````julia


F(x,y) = F(x,y, 3, 3)
tl(x) = ystar + 0 * (x- xstar)
plot(Eq(F, 10), xlims=(-4, 7), ylims=(-10, 10))
plot!(tl)
````


![](/var/folders/k0/94d1r7xd2xlcw_jkgqq4h57w0000gr/T/implicit_differentiation_45_1.png)





If you watch the video linked to above, you will see that the
surprising fact here is the resting point is such that the angles
formed by the rope are the same. Basically this makes the tension in
both parts of the rope equal, so there is a static position (if not
static, the ring would move and not end in the final position). We can
verify this fact numerically by showing the arctangents of the two
triangles are the same up to a sign (and slight round-off error):

````julia

a0, b0 = 0,0   # the foci of the ellipse are (0,0) and (3,3)
a1, b1 = 3, 3
atan((b0 - ystar)/(a0 - xstar)) + atan((b1 - ystar)/(a1 - xstar)) # 0
````


````
0.0
````







Now, were we lucky and just happened to take $a=3$, $b = 3$ in such a way to
make this work? Well, no. But convince yourself by doing the above for
different values of $b$.

## Questions

###### Question

Is $(1,1)$ on the graph of

$$~
x^2 - 2xy + y^2 = 1?
~$$

````
CalculusWithJulia.WeaveSupport.Radioq(["Yes", "No"], 2, "", nothing, [1, 2]
, ["Yes", "No"], "", false)
````





###### Question

For the equation

$$~
x^2y + 2y - 4 x = 0,
~$$

if $x=4$, what is a value for $y$ such that $(x,y)$ is a point on the graph of the equation?

````
CalculusWithJulia.WeaveSupport.Numericq(0.8888888888888888, 0.001, "", "[0.
88789, 0.88989]", 0.8878888888888888, 0.8898888888888888, "", "")
````






###### Question

For the equation

$$~
(y-5)\cdot \cos(4\cdot \sqrt{(x-4)^2 + y^2)} =  x\cdot\sin(2\sqrt{x^2 + y^2})
~$$


is the point $(5,0)$ a solution?

````
CalculusWithJulia.WeaveSupport.Radioq(["Yes", "No"], 2, "", nothing, [1, 2]
, ["Yes", "No"], "", false)
````





##### Question

Let $(x/3)^2 + (y/2)^2 = 1$. Find the slope of the tangent line at the point $(3/\sqrt{2}, 2/\sqrt{2})$.

````
CalculusWithJulia.WeaveSupport.Numericq(-0.6666666666666666, 0.001, "", "[-
0.66767, -0.66567]", -0.6676666666666666, -0.6656666666666666, "", "")
````






###### Question

The [lame](http://www-history.mcs.st-and.ac.uk/Curves/Lame.html) curves satisfy:

$$~
(\frac{x}{a})^n + (\frac{y}{b})^n = 1.
~$$

An ellipse is when $n=1$. Take $n=3$, $a=1$, and $b=2$.

Find a *positive* value of $y$ when $x=1/2$.

````
CalculusWithJulia.WeaveSupport.Numericq(1.9129311827723892, 0.001, "", "[1.
91193, 1.91393]", 1.9119311827723893, 1.913931182772389, "", "")
````





What expression gives $dy/dx$?

````
CalculusWithJulia.WeaveSupport.Radioq(LaTeXStrings.LaTeXString[L"$-(x/a)^n 
/ (y/b)^n$", L"$b \cdot (1 - (x/a)^n)^{1/n}$", L"$ -(y/x) \cdot (x/a)^n \cd
ot (y/b)^{-n}$"], 3, "", nothing, [1, 2, 3], LaTeXStrings.LaTeXString[L"$-(
x/a)^n / (y/b)^n$", L"$b \cdot (1 - (x/a)^n)^{1/n}$", L"$ -(y/x) \cdot (x/a
)^n \cdot (y/b)^{-n}$"], "", false)
````





###### Question

Let $y - x^2 = -\log(x)$. At the point $(1/2, 0.9431...)$, the graph has a tangent line. Find this line, then find its intersection point with the $y$ axes.

This intersection is:

````
CalculusWithJulia.WeaveSupport.Numericq(1.4431471805599454, 0.001, "", "[1.
44215, 1.44415]", 1.4421471805599455, 1.4441471805599453, "", "")
````







###### Question

The [witch](http://www-history.mcs.st-and.ac.uk/Curves/Witch.html) of [Agnesi](http://www.maa.org/publications/periodicals/convergence/mathematical-treasures-maria-agnesis-analytical-institutions) is the curve given by the equation

$$~
y(x^2 + a^2) = a^3.
~$$

If $a=1$, numerically find a a value of $y$ when $x=2$.

````
CalculusWithJulia.WeaveSupport.Numericq(0.2, 0.001, "", "[0.199, 0.201]", 0
.199, 0.201, "", "")
````






What expression yields $dy/dx$ for this curve:

````
CalculusWithJulia.WeaveSupport.Radioq(LaTeXStrings.LaTeXString[L"$-2xy/(x^2
 + a^2)$", L"$a^3/(x^2 + a^2)$", L"$2xy / (x^2 + a^2)$"], 1, "", nothing, [
1, 2, 3], LaTeXStrings.LaTeXString[L"$-2xy/(x^2 + a^2)$", L"$a^3/(x^2 + a^2
)$", L"$2xy / (x^2 + a^2)$"], "", false)
````





###### Question

````
CalculusWithJulia.WeaveSupport.ImageFile("figures/fcarc-may2016-fig35-350.g
if", L"
Image number 35 from L'Hospitals calculus book (the first). Given a descrip
tion of the curve, identify
the point $E$ which maximizes the height.

")
````






The figure above shows a problem appearing in L'Hospital's first calculus book. Given a function defined implicitly by $x^3 + y^3 = axy$  (with $AP=x$, $AM=y$ and $AB=a$) find the point $E$ that maximizes the height. In the [AMS feature column](http://www.ams.org/samplings/feature-column/fc-2016-05) this problem is illustrated and solved in the historical manner, with the comment that the concept of implicit differentiation wouldn't have occurred to L'Hospital.

Using Implicit differentiation, find when $dy/dx = 0$.

````
CalculusWithJulia.WeaveSupport.Radioq(LaTeXStrings.LaTeXString[L"$y=a/(3x^2
)$", L"$y=3x^2/a$", L"$y^2=a/(3x)$", L"$y^2 = 3x/a$"], 2, "", nothing, [1, 
2, 3, 4], LaTeXStrings.LaTeXString[L"$y=a/(3x^2)$", L"$y=3x^2/a$", L"$y^2=a
/(3x)$", L"$y^2 = 3x/a$"], "", false)
````





Substituting the correct value of $y$, above, into the defining equation gives what value for $x$:

````
CalculusWithJulia.WeaveSupport.Radioq(LaTeXStrings.LaTeXString[L"$x=(1/2) a
^3 3^{1/3}$", L"$x=(1/3) a 2^{1/3}$", L"$x=(1/3) a^2 2^{1/2}$", L"$x=(1/2) 
a 2^{1/2}$"], 2, "", nothing, [1, 2, 3, 4], LaTeXStrings.LaTeXString[L"$x=(
1/2) a^3 3^{1/3}$", L"$x=(1/3) a 2^{1/3}$", L"$x=(1/3) a^2 2^{1/2}$", L"$x=
(1/2) a 2^{1/2}$"], "", false)
````





###### Question

For the equation of an ellipse:

$$~
(\frac{x}{a})^2 + (\frac{y}{b})^2 = 1,
~$$

compute $d^2y/dx^2$. Is this the answer?

$$~
\frac{d^2y}{dx^2} = -\frac{b^2}{a^2\cdot y} - \frac{b^4\cdot x^2}{a^4\cdot y^3} = -\frac{1}{y}\frac{b^2}{a^2}(1 + \frac{b^2 x^2}{a^2 y^2}).
~$$

````
CalculusWithJulia.WeaveSupport.Radioq(["Yes", "No"], 1, "", nothing, [1, 2]
, ["Yes", "No"], "", false)
````





If $y>0$ is the sign positive or negative?

````
CalculusWithJulia.WeaveSupport.Radioq(["positive", "negative", "Can be both
"], 2, "", nothing, [1, 2, 3], ["positive", "negative", "Can be both"], "",
 false)
````





If $x>0$ is the sign positive or negative?


````
CalculusWithJulia.WeaveSupport.Radioq(["positive", "negative", "Can be both
"], 3, "", nothing, [1, 2, 3], ["positive", "negative", "Can be both"], "",
 false)
````





When $x>0$, the graph of the equation is...

````
CalculusWithJulia.WeaveSupport.Radioq(["concave up", "concave down", "both 
concave up and down"], 3, "", nothing, [1, 2, 3], ["concave up", "concave d
own", "both concave up and down"], "", false)
````

