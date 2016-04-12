Concepts and Tools for Radiation Shielding Analysis
====================================================

Over recent years, the set of concepts, methodologies and tools that are
available for the analysis of radiation transport and effects in shielding
systems has grown dramatically.  Here we attempt to describe the map of these
capabilities with mention of specific tools that have gained, or are gaining,
broader application.

Fundamental Concepts
--------------------

The fundamental analysis need for any radiation shielding problem is
**radiation transport**, for which two primary approaches are in widespread
use: Monte Carlo radiation transport and discrete ordinates radiation
transport.  Many of the effects of radiation can be calculated directly by the
tools that implement these capabilities, but determining the induced
radioactivity caused by neutron irradiation requires **activation analysis**
to be used in conjunction with radiation transport.

Monte Carlo Radiation Transport
++++++++++++++++++++++++++++++++

Monte Carlo radiation transport directly simulated the stochastic behavior of
particles that leave a source and travel around the engineered system.  If
enough particles are simulated using independent sequences of pseudo-random
numbers, then their mean behavior can be taken as representative of the real
system.  The single biggest advantage of Monte Carlo radiation transport is
its ability to model the entire phase space of radiation transport (space,
direction and energy) as continuous variables.  Continuous modeling of space
allows for
arbitrarily complex geometric models with no need for approximation.  Taken
together with continuous treatment of direction, this also means that
following particles through complex streaming paths is also possible with
these methods.  Finally, continuous treatment of energy allows less
approximation of resonance behaviors for particles that are slowing down
through that energy region.  This continuous modeling capability is the
primary reason that Monte Carlo radiation transport is widespread for this
kind of analysis.

The biggest drawback of Monte Carlo techniques is in the modeling of deep
penetration problems with substantial shielding.  Since such systems are
designed to prevent most particles from reaching the far side of such shields,
and Monte Carlo radiation transport essentially simulates the behavior of
these particles, then very few simulated particles will penetrate the shield.
This, in turn, results in very low quality results behind thick shields, since
the quality of statistical results relies on a substantial number of
contributions.  While it is possible to simply increase the total number of
simulated particles, a number of schemes have been introduced, collectively
known as *variance reduction* techniques to help improve this situation.

The most widely used tool for Monte Carlo radiation transport is:

* MCNP (Monte Carlo N-Particle), versions 5 and 6, written & maintained by Los
  Alamos National Laboratory.

While other tools exist, their user-base is dwarfed by that of MCNP: Fluka (CERN)
Geant4 (CERN), Monaco (ORNL), SHIFT (ORNL), Tripoli (CEA).

Discrete Ordinates Radiation Transport
+++++++++++++++++++++++++++++++++++++++

While a variety of so-called deterministic methods exist, the discrete
ordinates method is the most commonly used in radiation shielding analysis.
This method discretizes space, direction and energy, and then solves a
difference formulation of the radiation transport equation.  The most
important advantage of deterministic methods is their ability to find global
solutions with uniform quality.  Discretization in space introduces some
approximations that can be largely overcome by refining the spatial
resolution.  Discretization in direction, however, results in two related
effects that can undermine the quality of results.  First, for transport in
large (near) vacuum regions, particles are transported only along a discrete
set of directions introducing so-called *ray effects*.  Furthermore, for
arbitrarily oriented streaming paths and/or dog-legs, there may be no discrete
angle that is aligned with such a path, inhibiting accurate streaming
solutions.

The biggest benefit of discrete ordinates techniques is that the quality of
the solutions is independent of the thickness of the shielding region, and
results can generally be found much more quickly than with Monte Carlo
radiation transport.

Use of discrete ordinates software is less wide-spread with less
concentration in any single tool.  Some tools with user communities that
extend beyond their developer communities include:

* PARTISN (LANL)
* DORT/TORT (ORNL)
* Attila (Varian)
* Exnihilo (ORNL)

Activation Analysis
+++++++++++++++++++++

Activation analysis generally combines the results of neutron radiation
transport with cross-section data to determine the transmutation rates of all
the nuclides in a material.  The resultant nuclides may also undergo
transmutation or decay, and so on.  The equations that describe the evolution
of the isotopic composition are a relatively simple set of first order ODEs,
often referred to as the Bateman equations.  However, this is generally a very
stiff set of equations, as governed by the transmutation and decay rates that
have time constants that span from milliseconds to millions of years.  There a
variety of mathematical and computational approaches to solving matrix
exponential that arises from this set of equations.

The majority of tools available for isotopic inventory calculations are
derived from fission burnup codes, solving the inventory problem at a single
point in space, that is, a single material with single neutron flux spectrum.
They generally employ different computational approaches.  Some commonly used
tools are ORIGEN (ORNL), FISPACT (CCFE), and CINDER (LANL).  ALARA (UW) is a
special purpose activation tool that solves for multiple points in space
simultaneously.

Advanced Capabilities
---------------------

In recent years, attempts to analyze the most challenging radiation shielding problems have resulted in more advanced capabilities beyond the fundamental concepts and tools above.  In some cases, the details of complex shielding goeomtries emerged as the most complex aspect of assessing shielding performance, necessitating support for CAD-based analysis.  The reliance on Monte Carlo modeling for thick shielding problems also cast a focus on variance reduction techniques, and in conjunction with complex gometries, it became necessary to automate the process of generating paramters for variance reduction.


Geometry Representations and CAD-based Analysis
+++++++++++++++++++++++++++++++++++++++++++++++

Since the early 2000's, a number of different teams have pursued the ability
to perform radiation transport and activation on geometries that are based on
computer-aided designs (CAD).  For Monte Carlo radiation transport, the most
common approach (Germany/KIT/McCAD, China/FDS/MCAM, USA/Raytheon/TopAct, etc)
is to simplify the CAD models to include only the same geometric surfaces that
can be represented in the constructive/combinatorial geometry representations
that are native to the standard Monte Carlo tools, decompose the CAD regions
into more simpler regions, and generate an input file in the standard format
for those tools.  An alternative approach (USA/University of Wisconsin/DAGMC)
is to modify the Monte Carlo software itself to perform its geometry queries
directly on CAD-based representation of the geometry.  

For discrete ordinates radiation transport, all tools but Attila rely on
structured orthogonal grids for their geometry representation.  This requires
some degree of approximation, either by choosing a dominant material for each
voxel in that mesh or by mixing the materials in each voxel according to their
volume fraction in that voxel.  CAD-based geometries can be queried according
to either of these approaches to populate a structured grid representation of
the geometry.

Attila implements the discrete ordinates method using a finite element
approach on a tetrahedral mesh, allowing the mesh to conform to arbitrarily
complex shapes of the geometry and for each mesh element to contain a single
material.  Attila is able to directly import CAD-based geometry in order to
generate mesh for its transport algorithm.

Frequently, the CAD models available for a given system have been generated
without engineering analysis in mind.  As such, these models include details
that are unnecessary for nuclear analysis as well as overlaps of different
components.  The cleaning and repair of CAD-based geometries is currently not
automatable and benefits greatly from experience working with previous models
for this purpose.

Automated Variance Reduction Techniques
+++++++++++++++++++++++++++++++++++++++

As indicated above, Monte Carlo radiation transport can be challenged by
problems that involve deep penetration of thick shields, a characteristic of
most shielding problems.  A number of techniques, generally known as variance
reduction techniques, exist to accelerate these calculations for a specific
response.  However, as the problems become more complex, it becomes difficult
to manually configure those techniques for maximum benefit.  Over the last
15-20 years, a number of approaches have been developed to automate the
configuration of variance reduction.  Some approaches (MCNP weight window
generator, MAGIC) use iteration, where one round of Monte Carlo simulation is
used to improve a guess for the variance reduction parameters for the
successive round.

An alternative approach is to use the results of a deterministic calculation
to provide the parameters for variance reduction.  This is effective because
the deterministic calculation is generally much faster than a Monte Carlo
calculation with uniform quality of results.  ORNL has implemented the CADIS
and FW-CADIS methodologies in the tool ADVANTG for this purpose, when used in
conjunction with MCNP.  CADIS will optimize variance reduction parameters for
a single response while FW-CADIS can be employed to optimize variance
reduction parameters for many responses, including global solutions.

Types of Analysis
------------------

These tools are used in various combinations to accomplish different types of
analysis.  Depending on the combination of tools, different skills and
expertise are necessary.

Radiation transport, damage, heating, and tritium breeding
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

A basic radiation transport calculation can be configured to provide a variety
of nuclear responses in addition to the neutron flux distribution.  These
responses must be linear combinations of the neutron fluxes, typically based
on cross-sections or similar nuclear data.  It is common to estimate radiation
damage, in terms of displacements per atom as well as 
hydrogen (H) and helium (He) gas production, as well
as heating from neutron and photon radiation.  In fusion systems, it is also
of interest to estimate the number of tritium atoms that are produced in
breeding regions.

Increasingly, these analyses rely on CAD-based geometries to define the
original geometry.  In addition, most of these analyses benefit from the use
of automated variance reduction techniques.

Basic expertise: 

* Monte Carlo radiation transport, usually MCNP5/6.  

Additional expertise: 

* CAD-model preparation when using CAD-based approaches.
* Automated variance reduction when necessary

Shutdown Dose Rate Analysis
++++++++++++++++++++++++++++

Another important consideration in any system with neutrons is the impact of
transmutation caused by those neutrons.  The most common consequence is the
activation of structural components that results in a distributed photon
source that dominates the radiation environment when the main system stops
operating.  The shutdown dose rates that arise from these photons can be
calculated throughout the facility.

The most rigorous approach for this analysis is to first perform neutron
transport to determine the spatial distribution of the multi-group neutron
flux throughout the system.  At each point in space for which the neutron flux
is known, an activation problem is performed to determine the photon source at
that point.  Finally, the superposition of all photon sources is used for a
photon transport problem in which the spatial distribution of the photon dose
is determined.  Because of its separate transport steps for neutrons and
photons, this approach is often referred to as the "rigorous 2-step approach"
(R2S). For very large systems, it may be necessary to use spatial
decomposition of the neutron flux results and photon source results.

In most cases, a large mesh is used for both distributions of neutron flux and
consequent photon sources, and perhaps a different mesh for the photon dose
distribution.  This results in up to 1 million separate activation calculation
points, and thus requires automation to be tractable.  An automated R2S
approach has been implemented robustly as part of the PyNE toolkit, coupling
MCNP to ALARA, with support for CAD-based geometries.

Basic expertise: 

* Monte Carlo radiation transport, usually MCNP5/6.  
* Activation analysis using FISPACT, ALARA, or ORIGEN

Additional expertise: 

* CAD-model preparation when using CAD-based approaches.
* Automated variance reduction may be used for the photon dose phase.

Experimental work is underway (ORNL/UW) to determine how to best use automated
variance reduction for the neutron transport step.

Activation of Flowing Systems
+++++++++++++++++++++++++++++

In some cases, acitvation of liquids that flow throughout the system becomes a substantial issue for assessing the radiation environment.  Most commonly, those liquids are coolants used to remove heat, but they can fill other roles.  Analysis of these sytems requires tools and capabilities beyond normal activation analysis.  The flowpath of the coolant must be analyzed to determine the residence time in different irradiation environments.  These neutron fluxes and residence times for each control volume must then be combined in an activation analysis tool that permits the modeling of potentially arbitrary irradiation schedules.

Basic expertise:

* Monte Carlo radiation transport, usually MCNP5/6.  
* Activation analysis using FISPACT, ALARA, or ORIGEN

Additional expertise:

* Activation in complex irradiation histories with varying flux spectrums and
  irradiation times
* Fluid dynamics simulation of flow through complex structures
