=====================================
Operations
=====================================

Convex.jl currently supports the following functions.
These functions may be composed according to the `DCP <http://dcp.stanford.edu>`_ composition rules to form new convex, concave, or affine expressions.
Convex.jl transforms each problem into an equivalent `cone program <http://mathprogbasejl.readthedocs.org/en/latest/conic.html>`_ in order to pass the problem to a specialized solver.
Depending on the types of functions used in the problem, the conic constraints may include linear, second-order, exponential, or semidefinite constraints, as well as any binary or integer constraints placed on the variables.
Below, we list each function available in Convex.jl organized by the (most complex) type of cone used to represent that function,
and indicate which solvers may be used to solve problems with those cones.
Problems mixing many different conic constraints can be solved by any solver that supports every kind of cone present in the problem.

In the notes column in the tables below, we denote implicit constraints imposed on the arguments to the function by IC,
and parameter restrictions that the arguments must obey by PR.
(Convex.jl will automatically impose ICs; the user must make sure to satisfy PRs.)
Elementwise means that the function operates elementwise on vector arguments, returning
a vector of the same size.

Linear Program Representable Functions
**************************************

An optimization problem using only these functions can be solved by any LP solver.

+---------------------------+----------------------------+------------+---------------+---------------------------------+
|operation                  | description                | vexity     | slope         | notes                           |
+===========================+============================+============+===============+=================================+
|:code:`x+y` or :code:`x.+y`| addition                   | affine     |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`x-y` or :code:`x.-y`| subtraction                | affine     |increasing in  | none                            |
|                           |                            |            |:math:`x`      |                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |decreasing in  | none                            |
|                           |                            |            |:math:`y`      |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`x*y`                | multiplication             | affine     |increasing if  | PR: one argument is constant    |
|                           |                            |            |               |                                 |
|                           |                            |            |constant term  |                                 |
|                           |                            |            |:math:`\ge 0`  |                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |decreasing if  |                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |constant term  |                                 |
|                           |                            |            |:math:`\le 0`  |                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |not monotonic  |                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |otherwise      |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`x/y`                | division                   | affine     |increasing     | PR: :math:`y` is scalar constant|
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`dot(*)(x, y)`       | elementwise multiplication | affine     |increasing     | PR: one argument is constant    |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`dot(/)(x, y)`       | elementwise division       | affine     |increasing     | PR: one argument is constant    |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`x[1:4, 2:3]`        | indexing and slicing       | affine     |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`diag(x, k)`         | :math:`k`-th diagonal of   | affine     |increasing     | none                            |
|                           | a matrix                   |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`diagm(x)`           | construct diagonal         | affine     |increasing     | PR: :math:`x` is a vector       |
|                           | matrix                     |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`x'`                 | transpose                  | affine     |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`vec(x)`             | vector representation      | affine     |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`dot(x,y)`           | :math:`\sum_i x_i y_i`     | affine     |increasing     | PR: one argument is constant    |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`kron(x,y)`          | Kronecker product          | affine     |increasing     | PR: one argument is constant    |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`vecdot(x,y)`        |:code:`dot(vec(x),vec(y))`  | affine     |increasing     | PR: one argument is constant    |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`sum(x)`             | :math:`\sum_{ij} x_{ij}`   | affine     |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`sum(x, k)`          | sum elements across        | affine     |increasing     | none                            |
|                           |                            |            |               |                                 |
|                           | dimension :math:`k`        |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`sumlargest(x, k)`   | sum of :math:`k` largest   | convex     |increasing     | none                            |
|                           |                            |            |               |                                 |
|                           | elements of :math:`x`      |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`sumsmallest(x, k)`  |sum of :math:`k` smallest   | concave    |increasing     | none                            |
|                           |                            |            |               |                                 |
|                           |elements of :math:`x`       |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`dotsort(a, b)`      |:code:`dot(sort(a),sort(b))`| convex     |increasing     | PR: one argument is constant    |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`reshape(x, m, n)`   | reshape into               | affine     |increasing     | none                            |
|                           | :math:`m \times n`         |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`minimum(x)`         | :math:`\min(x)`            | concave    |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`maximum(x)`         | :math:`\max(x)`            | convex     |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`[x y]` or `[x; y]`  | stacking                   | affine     |increasing     | none                            |
|                           |                            |            |               |                                 |
|:code:`hcat(x, y)` or      |                            |            |               |                                 |
|                           |                            |            |               |                                 |
|:code:`vcat(x, y)`         |                            |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`trace(x)`           | :math:`\mathrm{tr}         | affine     |increasing     | none                            |
|                           | \left(X \right)`           |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|partialtrace(x,sys,dims)   | Partial trace              | affine     |increasing     | none                            |
|                           |                            |            |               |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`conv(h,x)`          |:math:`h \in                | affine     |increasing if  | PR: :math:`h` is constant       |
|                           |\mathbb{R}^m`               |            |:math:`h\ge 0` |                                 |
|                           |                            |            |               |                                 |
|                           |:math:`x \in                |            |               |                                 |
|                           |\mathbb{R}^m`               |            |               |                                 |
|                           |                            |            |               |                                 |
|                           |:math:`h*x                  |            |               |                                 |
|                           |\in \mathbb{R}^{m+n-1}`     |            |               |                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |               |                                 |
|                           |entry :math:`i` is          |            |decreasing if  |                                 |
|                           |given by                    |            |:math:`h\le 0` |                                 |
|                           |                            |            |               |                                 |
|                           |:math:`\sum_{j=1}^m         |            |               |                                 |
|                           |h_jx_{i-j}`                 |            |not monotonic  |                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |otherwise      |                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`min(x,y)`           | :math:`\min(x,y)`          | concave    |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`max(x,y)`           | :math:`\max(x,y)`          | convex     |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`pos(x)`             | :math:`\max(x,0)`          | convex     |increasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`neg(x)`             | :math:`\max(-x,0)`         | convex     |decreasing     | none                            |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`invpos(x)`          | :math:`1/x`                | convex     |decreasing     | IC: :math:`x>0`                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+
|:code:`abs(x)`             | :math:`\left|x\right|`     | convex     |increasing on  | none                            |
|                           |                            |            |:math:`x \ge 0`|                                 |
|                           |                            |            |               |                                 |
|                           |                            |            |decreasing on  |                                 |
|                           |                            |            |:math:`x \le 0`|                                 |
+---------------------------+----------------------------+------------+---------------+---------------------------------+


Second-Order Cone Representable Functions
*****************************************

An optimization problem using these functions can be solved by any SOCP solver (including ECOS, SCS, Mosek, Gurobi, and CPLEX).
Of course, if an optimization problem has both LP and SOCP representable functions, then any solver that can solve both LPs and SOCPs can solve the problem.


+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|operation                   | description                         | vexity     | slope         | notes                    |
+============================+=====================================+============+===============+==========================+
|:code:`norm(x, p)`          | :math:`(\sum x_i^p)^{1/p}`          | convex     |increasing on  | PR: :code:`p >= 1`       |
|                            |                                     |            |:math:`x \ge 0`|                          |
|                            |                                     |            |               |                          |
|                            |                                     |            |decreasing on  |                          |
|                            |                                     |            |:math:`x \le 0`|                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`vecnorm(x, p)`       | :math:`(\sum x_{ij}^p)^{1/p}`       | convex     |increasing on  | PR: :code:`p >= 1`       |
|                            |                                     |            |:math:`x \ge 0`|                          |
|                            |                                     |            |               |                          |
|                            |                                     |            |decreasing on  |                          |
|                            |                                     |            |:math:`x \le 0`|                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`quadform(x, P)`      | :math:`x^T P x`                     | convex in  |increasing on  | PR: either :math:`x` or  |
|                            |                                     | :math:`x`  |:math:`x \ge 0`| :math:`P`                |
|                            |                                     |            |               |                          |
|                            |                                     | affine in  |decreasing on  | must be constant;        |
|                            |                                     | :math:`P`  |:math:`x \le 0`| if :math:`x` is not      |
|                            |                                     |            |               | constant, then :math:`P` |
|                            |                                     |            |increasing in  | must be symmetric and    |
|                            |                                     |            |:math:`P`      | positive semidefinite    |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`quadoverlin(x, y)`   | :math:`x^T x/y`                     | convex     |increasing on  |                          |
|                            |                                     |            |:math:`x \ge 0`| IC: :math:`y > 0`        |
|                            |                                     |            |               |                          |
|                            |                                     |            |decreasing on  |                          |
|                            |                                     |            |:math:`x \le 0`|                          |
|                            |                                     |            |               |                          |
|                            |                                     |            |decreasing in  |                          |
|                            |                                     |            |:math:`y`      |                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`sumsquares(x)`       | :math:`\sum x_i^2`                  | convex     |increasing on  | none                     |
|                            |                                     |            |:math:`x \ge 0`|                          |
|                            |                                     |            |               |                          |
|                            |                                     |            |decreasing on  |                          |
|                            |                                     |            |:math:`x \le 0`|                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`sqrt(x)`             | :math:`\sqrt{x}`                    | concave    |decreasing     | IC: :math:`x>0`          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`square(x), x^2`      | :math:`x^2`                         | convex     |increasing on  | PR : :math:`x` is scalar |
|                            |                                     |            |:math:`x \ge 0`|                          |
|                            |                                     |            |               |                          |
|                            |                                     |            |decreasing on  |                          |
|                            |                                     |            |:math:`x \le 0`|                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`dot(^)(x,2)`         | :math:`x.^2`                        | convex     |increasing on  | elementwise              |
|                            |                                     |            |:math:`x \ge 0`|                          |
|                            |                                     |            |decreasing on  |                          |
|                            |                                     |            |:math:`x \le 0`|                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`geomean(x, y)`       | :math:`\sqrt{xy}`                   | concave    |increasing     | IC: :math:`x\ge0`,       |
|                            |                                     |            |               | :math:`y\ge0`            |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`huber(x, M=1)`       | :math:`\begin{cases}                | convex     |increasing on  | PR: :math:`M>=1`         |
|                            | x^2 &|x| \leq                       |            |:math:`x \ge 0`|                          |
|                            | M  \\                               |            |               |                          |
|                            | 2M|x| - M^2                         |            |               |                          |
|                            | &|x| >  M                           |            |decreasing on  |                          |
|                            | \end{cases}`                        |            |:math:`x \le 0`|                          |
|                            |                                     |            |               |                          |
|                            |                                     |            |               |                          |
|                            |                                     |            |               |                          |
|                            |                                     |            |               |                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+


Exponential Cone  Representable Functions
******************************************

An optimization problem using these functions can be solved by any exponential cone solver (SCS).

+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|operation                   | description                         | vexity     | slope         | notes                    |
+============================+=====================================+============+===============+==========================+
|:code:`logsumexp(x)`        | :math:`\log(\sum_i \exp(x_i))`      | convex     |increasing     |none                      |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`exp(x)`              | :math:`\exp(x)`                     | convex     |increasing     | none                     |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`log(x)`              | :math:`\log(x)`                     | concave    |increasing     | IC: :math:`x>0`          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`entropy(x)`          | :math:`\sum_{ij}                    | concave    |not monotonic  | IC: :math:`x>0`          |
|                            | -x_{ij} \log (x_{ij})`              |            |               |                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+
|:code:`logisticloss(x)`     | :math:`\log(1 + \exp(x_i))`         | convex     |increasing     | none                     |
|                            |                                     |            |               |                          |
+----------------------------+-------------------------------------+------------+---------------+--------------------------+


Semidefinite Program Representable Functions
********************************************

An optimization problem using these functions can be solved by any SDP solver (including SCS and Mosek).

+---------------------------+-------------------------------------+------------+---------------+------------------------------+
|operation                  | description                         | vexity     | slope         | notes                        |
+===========================+=====================================+============+===============+==============================+
|:code:`nuclearnorm(x)`     | sum of singular values of :math:`x` | convex     |not monotonic  | none                         |
+---------------------------+-------------------------------------+------------+---------------+------------------------------+
|:code:`operatornorm(x)`    | max of singular values of :math:`x` | convex     |not monotonic  | none                         |
+---------------------------+-------------------------------------+------------+---------------+------------------------------+
|:code:`lambdamax(x)`       | max eigenvalue of :math:`x`         | convex     |not monotonic  | none                         |
+---------------------------+-------------------------------------+------------+---------------+------------------------------+
|:code:`lambdamin(x)`       | min eigenvalue of :math:`x`         | concave    |not monotonic  | none                         |
+---------------------------+-------------------------------------+------------+---------------+------------------------------+
|:code:`matrixfrac(x, P)`   | :math:`x^TP^{-1}x`                  | convex     |not monotonic  |IC: P is positive semidefinite|
+---------------------------+-------------------------------------+------------+---------------+------------------------------+

Exponential + SDP representable Functions
********************************************

An optimization problem using these functions can be solved by any solver that supports exponential constraints *and* semidefinite constraints simultaneously (SCS).

+----------------------------+-------------------------------------+------------+---------------+------------------------------+
|operation                   | description                         | vexity     | slope         | notes                        |
+============================+=====================================+============+===============+==============================+
|:code:`logdet(x)`           | log of determinant of :math:`x`     | concave    |increasing     |IC: x is positive semidefinite|
+----------------------------+-------------------------------------+------------+---------------+------------------------------+

Promotions
***********

When an atom or constraint is applied to a scalar and a higher dimensional variable, the scalars are promoted. For example, we can do :code:`max(x, 0)` gives an expression with the shape of :code:`x` whose elements are the maximum of the corresponding element of :code:`x` and :code:`0`.
