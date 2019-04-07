.. Convex.jl documentation master file, created by
  sphinx-quickstart on Fri Jan  2 03:59:02 2015.
  You can adapt this file completely to your liking, but it should at least
  contain the root `toctree` directive.

=========================================
Convex.jl - Convex Optimization in Julia
=========================================

Convex.jl is a Julia package for `Disciplined Convex Programming <http://dcp.stanford.edu/>`_ (DCP).
Convex.jl makes it easy to describe optimization problems in a natural, mathematical syntax,
and to solve those problems using a variety of different (commercial and open-source) solvers.
Convex.jl can solve

  * linear programs
  * mixed-integer linear programs and mixed-integer second-order cone programs
  * dcp-compliant convex programs including

    - second-order cone programs (SOCP)
    - exponential cone programs
    - semidefinite programs (SDP)

Convex.jl supports many solvers, including `Mosek <https://github.com/JuliaOpt/Mosek.jl>`_, `Gurobi <https://github.com/JuliaOpt/gurobi.jl>`_, `ECOS <https://github.com/JuliaOpt/ECOS.jl>`_, `SCS <https://github.com/karanveerm/SCS.jl>`_ and `GLPK <https://github.com/JuliaOpt/GLPK.jl>`_, through the `MathProgBase <http://mathprogbasejl.readthedocs.org/en/latest/>`_ interface.

Note that Convex.jl was previously called CVX.jl. This package is under active development; we welcome bug reports and feature requests. For usage questions, please contact us via the `JuliaOpt mailing list <https://groups.google.com/forum/#!forum/julia-opt>`_.

In Depth Documentation:
#########################

.. toctree::
  :maxdepth: 2

  Installation <installation>
  Quick Tutorial <quick_tutorial>
  Basic Types <types>
  Supported Operations <operations>
  Examples <examples>
  Complex-domain Optimization <complex-domain_optimization>
  Solvers <solvers>
  FAQ <faq>
  Optimizing in a Loop <loop>
  Advanced <advanced>
  Contributing <contributing>
  Credits <credits>
