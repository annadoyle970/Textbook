.. _title_Sedimentation_Examples:

***************************************
Sedimentation Examples
***************************************

These are a few short examples of calculations required for sedimentation tank design. More examples can be found in the :ref:`Sedimentation Design Solution <heading_Sed_Design_Challenge_Solution>`.

Tube Settler Design
===============================

1. Design a tube settler for a laboratory scale sedimentation tank. The vertical section of the sedimentation tank, :math:`v_{z_{fb}}`, has a net upflow velocity of 3 mm/s. This velocity is maintained in the tube settler, :math:`v_{\alpha}`. The target capture velocity is 0.2 mm/s. The tube settler diameter is 2.54 cm.

.. math:: \frac{\bar v_{z_{fb}}}{\bar v_c} = \frac{L}{D} \cos \alpha \sin \alpha + \sin ^2 \alpha

.. math:: \bar v_{z_{fb}} = \bar v_\alpha\sin \alpha

Solve for the length of the tube settler.

.. math:: L = \frac{D}{\cos \alpha}\left(\frac{\bar v_\alpha}{\bar v_c} - \sin \alpha\right)


.. code:: python

  from aguaclara.core.units import unit_registry as u

  import numpy as np

  v_alpha = 3 * u.mm/u.s
  v_c = 1 * u.mm/u.s
  D = 2.54 * u.cm
  alpha = 60 * u.deg

  def L_settler(D,alpha,v_alpha,v_c):
   return D/np.cos(alpha)*(v_alpha/v_c - np.sin(alpha))

  print(L_settler(D,alpha,v_alpha,1*u.mm/u.s))
  print(L_settler(D,alpha,v_alpha,0.2*u.mm/u.s))

The tube settler above the floc hopper needs to be 72 cm long. The tube settler should provide a capture velocity of at least 1 mm/s prior to the floc hopper. Thus there should be 11 cm below the floc hopper.

.. _heading_flow_thru_diffuser:

Determining flow through a diffuser
====================================

1. What is the flow rate of a single diffuser in the bottom of the sedimentation tank? Consider a sedimentation tank that is 6 m long, 1 m wide and 2 m deep, with an upflow velocity of 1 mm/s and a diffuser spacing of 5 cm.

What is this question really asking? This question is asking us to understand that each diffuser "serves" a specific cross-sectional area of the sedimentation tank; all of the diffusers together serve the entire area of the sedimentation tank. So, let's imagine a single diffuser serving a slice of a sedimentation tank. With this in mind, we can easily solve this using :math:`Q = \bar vA`. The area, :math:`A`, is the slice of the sedimentation tank that we are serving. We are told that the tank is 1 m wide, so :math:`W_{tank} = 1` m. The length of the slice is dictated by the spacing of the diffusers, :math:`B_{diff}`, so :math:`B_{diff} = 5` cm.

.. math:: A = B_{diff}W_{tank}

.. math:: A = 5 cm * 1 m

.. math:: A = 50,000 mm^2

The problem statement includes that :math:`\bar v_{z_{fb}} = 1` mm/s. Plugging into our flow equation,

.. math:: Q_{diff} = \bar v_{z_{fb}}A

.. math:: Q_{diff} = (1 \frac{mm}{s})(50,000mm^2)

.. math:: Q_{diff} = 50,000 \frac{mm^3}{s}

.. math:: Q_{diff} = 50 \frac{mL}{s}

The flow rate of each diffuser is :math:`50 \frac{mL}{s}`.

Identify Failure Modes from Old Design
==================================================

Look at a proposed design for the bottom of the sedimentation tank, shown in :numref:`figure_failure_mode_example`. This design has an influent manifold at the bottom of the tank. Water flows upwards from the influent manifold. At one end of the influent manifold, there is a drain port. Above the influent manifold, there is a single slot that extends the length of the sedimentation tank. There are no diffusers in this design.

.. _figure_failure_mode_example:

.. figure:: Images/failure_mode_example.png
    :height: 300px
    :align: center
    :alt: Proposed sedimentation tank design.

    Proposed sedimentation tank design.

1. What are the failure modes for this design?

Some issues are:

- flocs can settle in the influent manifold, specifically at the end of the influent manifold pipe;
- the upflow line jet may be impacted and bent by settling flocs, allowing for floc settling on one side of the tank;
- without diffusers, there may not be uniform flow distribution from one end of the sedimentation tank to the other;
- without diffusers, there will be large flow circulations inside the sedimentation tank.

This design has never been built and never will be. Understanding what the problems are with this design will help us design better in the future.

Diffuser and Jet Reverser Design
================================

1. Calculate the maximum velocity of water leaving the diffuser based on the maximum head loss. Assume that the majority of head loss is the kinetic energy of the flow exiting the diffuser slot (this assumption will be checked later). Assume K=1.

Given parameters:

.. code:: python

  from aguaclara.core.units import unit_registry as u
  from aguaclara.core import utility as ut

  import numpy as np

  #given sedimentation inlet maximum headloss
  headloss_sed_inlet_max = 1 * u.cm
  #given sedimentation tank up flow velocity
  V_sed_up = 1 * u.mm/u.s
  #given sedimentation tank width
  W_sed = 42 * u.inch

To find the maximum velocity based on maximum headloss we will use the minor loss equation.

.. math:: h_{e,inlet} = K \frac{\bar v_{jet}^2}{2g}

To find the minimum width based on the maximum velocity through the diffuser, we will use conservation of mass. Since it is an incompressible fluid the flow rate entering from the diffuser line jet must be equal to the flow rate up through the sedimentation tank.

.. math:: \bar v_{jet}W_{diff} L_{sed} = \bar v_{z_{fb}}W_{sed}L_{sed}

.. code:: python


  g = 9.81 * u.m / u.s**2
  # minor loss equation with K=1
  V_diffuser_max = (np.sqrt((2 * g * headloss_sed_inlet_max))).to(u.m / u.s)
  print('The maximum velocity of the sed tank diffusers is',V_diffuser_max)

  # mass conservation
  W_diffuser_inner_min = ((V_sed_up / V_diffuser_max) * W_sed).to(u.mm)
  print('The minimum width of the sed tank diffusers is',W_diffuser_inner_min)


**Answer:** The maximum velocity of the sedimentation tank diffusers is 0.4429 meters / second.
The minimum width of the sedimentation tank diffusers is 2.409 millimeter.

2. Calculate the minimum inner width of the diffuser. Assume that the diffuser slot is continuous over the entire length of the sedimentation tank to get an initial estimate (it isn't actually continuous because it is made from many flattened diffuser pipes).

Diffusers are made by deforming PVC pipe. Softened PVC pipe is forced onto a mold that shapes it into the rectangular shape of the diffuser. (link to sedimentation chapter)

What metal plate thickness should be used to make the mold for the diffusers? This value will be the minimum diffuser width. Metal plates are available in 1/16" increments of thickness. The minimum thickness of plate that is strong enough for a mold is 1/16".
The `ceil_nearest` function defined in utility.py can take in a parameter and an array and it will find the closest value in the array that is at least as big and the parameter. For our problem we will use this to find the plate size that is available and at least as big as the minimum width defined above.

.. code:: python

  W_diffuser = ut.ceil_nearest(W_diffuser_inner_min, np.arange(1/16,1/4,1/16)*u.inch)

  print('The width of sed tank diffuser is',W_diffuser.to(u.cm))

**Answer:** The width of sedimentation tank diffuser is 0.3175 centimeter

The PVC pipe that forms the diffusers changes in shape and wall thickness during the molding process. The inner width of the rectangle is created by forcing the pipe over a rectangular wedge that is the thickness you calculated above. During the molding process, PVC pipe wall cross-sectional area is conserved. The pipe wall is stretched in total length approximately 20%. Another way to think about this is that the thickness of the wall is reduced by a factor of 1/1.2 because the mass of PVC is conserved and the density is unchanged. Thus, volume and cross-sectional area are conserved.


Area is given using the following Equation :math:`A_{PVC}=2\left (B_{diffuser}+W_{diffuser} \right)T_{diff}`

3. Use the equation for :math:`A_{PVC}` to calculate the following

- the outer length of the rectangular diffuser slot, :math:`B_{diffuser}`.
- the inner length of the rectangular diffuser slot, :math:`W_{diffuser}`.


.. code:: python

  from aguaclara.core import pipes as pipe
  SDR=26

  # Assumed stretch of the PVC pipes as they are heated and molded:
  Pi_PVC_stretch = 1.2

  # Nominal diameter of the sed tank diffuser
  ND_sed_diffuser = 1 * u.inch
  #The cross-sectional area of the pipe wall is:
  area_PVC = (np.pi/4) * ((pipe.OD(ND_sed_diffuser)**2) - (pipe.ID_SDR(ND_sed_diffuser,SDR)**2))

  #The thickness of the wall is reduced by the stretch factor:
  thickness_sed_diffuser_wall = ((pipe.OD(ND_sed_diffuser) - pipe.ID_SDR(ND_sed_diffuser,SDR)) / (2 * Pi_PVC_stretch))

  # From geometry of the rectangular diffuser opening (assuming perfectly square corners) we have:
  B_diffuser = ((area_PVC / (2 * thickness_sed_diffuser_wall)) - W_diffuser).to(u.cm)

  print("Sed diffuser outer length:", B_diffuser)

  S_diffuser = B_diffuser - (2 * thickness_sed_diffuser_wall)
  print("Sed diffuser inner length:", S_diffuser)


**Answer:** The sedimentation diffuser outer length: 5.736 centimeter

Sedimentation diffuser inner length: 5.522 centimeter

Each diffuser serves a certain width and length of the sedimentation tank. Assume that the diffusers are installed so that they touch each other.

4. Determine the flow through each diffuser.
:math:`Q_{max,diff} = \bar v_{z_{fb}} A`

:math:`A = W_{sed}  B_{diff}`

5. Determine the velocity through each diffuser.
:math:`\bar v_{diff} = \frac{Q_{max,diff}}{W_{diff} * S_{diff}}`


.. code:: python

  flow_max_diffuser = V_sed_up * W_sed * B_diffuser
  V_diffuser = (flow_max_diffuser / (W_diffuser * S_diffuser)).to(u.m / u.s)
  print('The flow of water leaving a sed tank diffuser is',flow_max_diffuser.to(u.ml/u.s))
  print('The velocity of water leaving the sed tank diffuser is',V_diffuser)

**Answer:** The flow of water leaving a sed tank diffuser is 61.19 milliliter / second
The velocity of water leaving the sed tank diffuser is 0.349 meter / second

6. What is the Reynolds number of the jet exiting the diffusers at the design temperature of 15 degrees Celsius?

Recall the formula for Reynold's number:

:math:`Re = \frac{\bar v D}{\nu}` The D is actually just representative of the length scale so we can replace this with the width of the diffuser.
:math:`Re = \frac{\bar v_{diff}*W_{diff}}{\nu}`

.. code:: python

  from aguaclara.core import physchem as pc
  T_design = 15 * u.degC
  Re_diffuser_jet = ((W_diffuser * V_diffuser) / pc.viscosity_kinematic(T_design)).to(u.dimensionless)
  print('The Reynolds number for this jet is',Re_diffuser_jet)

**Answer:** The Reynolds number for this jet is 974.6 dimensionless

7. What is the Reynolds number of the vertical flow up through the top of the floc blanket?

The same principle as above can be applied to this question except the length scale is the width of the sedimentation tank and the velocity is the upwards velocity in the tank.

:math:`Re = \frac{\bar v_{z_{fb}} W_{sed}}{\nu}`

.. code:: python

  Re_sed = ((W_sed * V_sed_up) / pc.viscosity_kinematic(T_design)).to(u.dimensionless)
  print('Reynolds number through floc is',Re_sed)

**Answer:** Reynolds number through floc is 938.2 dimensionless. These two Reynold's numbers are similar because conservation of mass requires for a constant length that :math:`\bar v_{1}*W_{1} = \bar v_{2}*W_{2}`. The slight difference in the numbers is due to that fact that diffusers are not a continuous line jet but rather broken up by two times the thickness of the pipe wall between the diffusers.

Next, we want to determine the energy dissipation rate for the flow leaving the jet reverser. For this process, you can assume that the jet remains laminar. The flow spreads to fill the gaps created by the walls of the diffuser tubes by the time it traverses the jet reverser. Jet velocity and flow rate are conserved as the jet changes direction in the jet reverser.

8. Calculate the thickness of the jet after it does the 180 degree bend of the jet reverser. The change in thickness of the jet after the 180 degree bend is due to the flow spreading out to fill in the gaps created by the diffuser pipe walls.
:math:`W_{jet} * \bar v_{diff} = W_{sed} * \bar v_{z_{fb}}`

9. Calculate the maximum energy dissipation rate for the flow leaving the jet reverser. See Equation :eq:`EDR_JetPlane` for the maximum energy dissipation rate in a plane jet and see :numref:`table_EDR_G_equations` for the value of :math:`\Pi_{JetPlane}`.


.. code:: python

  Pi_jet_plane = 0.0124
  W_jet_reversed = W_sed * V_sed_up / V_diffuser
  EDR_inlet_jet = Pi_jet_plane* ((V_diffuser**3)/ W_jet_reversed).to(u.mW / u.kg)
  print('The energy dissipation rate for inlet jet is', EDR_inlet_jet)

The energy dissipation rate for inlet jet is 158.5 milliwatt / kilogram

In designing AguaClara plants, it is critical to account for all forms of significant head loss. In the sedimentation tank, effluent launders provide about 4 cm of head loss. We want to calculate the exit head loss for water leaving the diffusers to determine whether it is a significant addition to the total head loss through the sedimentation tank.

10. Calculate this diffuser exit head loss in two ways.

First, calculate the head loss making sure to account for the upflow velocity in the sed tank.

:math:`h_e = \frac{\left( {{\bar v_{diff}} - {\bar v_{z_{fb}}}} \right)^2}{2g}`

Second, calculate the head loss but assume that the upflow velocity is negligible.

:math:`h_e = \frac{\ {\bar v_{diff}}^2}{2g}`

11. Is it reasonable to neglect the upflow velocity in the sed tank when calculating this head loss?

.. code:: python

  hl_sed_diffuser_exit1 = (((V_diffuser - V_sed_up) ** 2) / (2 *g)).to(u.cm)
  hl_sed_diffuser_exit2 = (((V_diffuser) ** 2) / (2 *g)).to(u.cm)
  hl_sed_diffuser_error = (hl_sed_diffuser_exit2-hl_sed_diffuser_exit1)/hl_sed_diffuser_exit1

  print('The best estimate of the exit head loss for the diffuser is', hl_sed_diffuser_exit1)
  print('The 2nd estimate of the exit head loss for the diffuser ignoring the upflow velocity is', hl_sed_diffuser_exit2)
  print('It is reasonable to neglect the effect of the upflow velocity. The error is',hl_sed_diffuser_error)

**Answer:** The best estimate of the exit head loss for the diffuser is 0.6176 centimeter. The 2nd estimate of the exit head loss for the diffuser ignoring the upflow velocity is 0.6211 centimeter. It is reasonable to neglect the effect of the upflow velocity. The error is 0.005755 dimensionless

Manifolds and Launders
=======================
Flow distribution between and within sedimentation tanks is an important design component to ensure good sedimentation performance. We need to distribute flow uniformly between sedimentation tanks and also between diffusers on the inlet manifolds.

The following variable definitions and equations will be useful in answering later questions.

:math:`{h}_{L,ParallelPath}` is the head loss (flow resistance) in the parallel paths leaving the manifold. The head loss in the parallel path is the total head loss from where the flow leaves the manifold to the point where the parallel flows reunite.

:math:`\Delta{H}_{Manifold}` is the variability in piezometric head in the manifold that is driving the flow through the parallel paths.

The ratio of minimum (first diffuser port) to maximum (last diffuser port) flow is given by:

.. math:: \Pi_{DiffuserFlow} = \sqrt{\frac{h_{L,parallelpath} - \frac{\Delta{H}_{Manifold}}{2}}{h_{L,parallelpath} + \frac{\Delta{H}_{Manifold}}{2}}}

The change in piezometric head is given by: :math:`\Delta{H}_{Manifold} = \frac{{v_{manifold}}^{2}}{2g}`

The maximum allowable velocity in the manifold is given by:

.. math:: {\Pi_{DiffuserFlow}}^{2} * \left(h_{L,parallelpath} + \frac{\Delta{H}_{Manifold}}{2} \right) = h_{L,parallelpath} - \frac{\Delta{H}_{Manifold}}{2}

.. math:: \left({\Pi_{DiffuserFlow}}^{2} - 1 \right) h_{L,parallelpath} + \left({\Pi{DiffuserFlow}}^{2} + 1 \right) \frac{\Delta{H}_{Manifold}}{2} = 0

.. math:: \left(\frac{1 - {\Pi_{DiffuserFlow}}^{2}}{{\Pi_{DiffuserFlow}}^{2} + 1} \right) h_{L,parallelpath} = \frac{\Delta{H}_{Manifold}}{2}

.. math:: \left(\frac{1 - {\Pi_{DiffuserFlow}}^{2}}{{\Pi_{DiffuserFlow}}^{2} + 1} \right) h_{L,parallelpath} = \frac{{v_{manifold}}^{2}}{4g}

Now, we want to find the maximum velocity for an inlet manifold which is dependent on the given flow distribution constraint, :math:`\Pi_{DiffuserFlow}`, and the head loss in the parallel paths, :math:`h_{L,ParallelPath}`.

1. Determine the relationship between diffuser exit velocity and the head loss in the parallel paths.
2. Determine an equation for maximum velocity for an inlet manifold in terms of diffuser exit velocity and the flow distribution constraint.**

Write a function for maximum velocity for an inlet manifold using the equations you just found.

Exit losses from the diffusers dominate the head loss because the velocity in the diffuser slots is much higher than the velocity at the entrance to the diffuser pipes. Using the insight from the previous problem, it is reasonable to neglect the effect of the upflow velocity when calculating the exit head loss for the manifold diffusers.

.. code:: python

  def Vel_sed_manifold_max(Pi_diffuser_flow, V_diffuser):
    return (V_diffuser * np.sqrt(2 * ((1-(Pi_diffuser_flow**2)) / ((Pi_diffuser_flow**2)+1))))

Head loss in the sedimentation tank is impacted by multiple forms of head loss, inlcuding head loss through the effluent launder and diffusers. Head loss through the effluent launder is about 4 cm. You found head loss through the diffusers in Problem 9.

3. Which form of head loss (effluent launder or diffuser) is in the parallel path, :math:`{h}_{L,ParallelPath}`?

.. code:: python

    Pi_sed_manifold_flow = 0.8
    V_sed_manifold_max = Vel_sed_manifold_max(Pi_sed_manifold_flow, V_diffuser)
    print('The maximum velocity in the sedimentation tank manifold is',V_sed_manifold_max)

**Answer:** Only the diffuser head loss is in the parallel paths. The maximum velocity in the sedimentation tank manifold is 0.2313 meter / second.

The ratio of manifold pipe cross-sectional area to total diffuser cross-sectional area determines the flow distribution between diffusers.

4. Calculate the ratio of manifold pipe cross-sectional area to total diffuser cross-sectional area. You can use the velocities of the manifold and the diffusers to calculate the areas.

Since the sedimentation tank has a constant volume, the flow rate into the tank is equal to the flow rate out of the tank:
:math:`Q_{manifold,pipe} = Q_{diff}`

:math:`v_{manifold} * A_{manifold} = v{diff} * A_{diff}`

:math:`\frac{A_{manifold}}{A_{diff}} = \frac{v_{diff}}{v_{manifold}}`

5. What is the significance of the flow area ratio that you found? What does it tell you about the relative areas?

.. code:: python

  print('The flow area ratio of manifold pipe to diffusers is',(V_diffuser / V_sed_manifold_max).to(u.dimensionless))

**Answer:** The flow area ratio of manifold pipe to diffusers is 1.509 dimensionless. This means that the manifold flow area is larger than the total diffuser area. The flow distribution is more uniform because the diffuser velocity is higher than the manifold velocity.

The maximum sed tank flow rate is currently set by the constraint of using a single length of pipe for the manifold and launder. The maximum length of the upflow region of the sedimentation tank is 5.8 m, as given below.

6. What is the corresponding sedimentation tank flow rate? This can be solved using :math:`Q = \bar v A`.

.. code:: python

  L_sed_upflow_max = 5.8 * u.m
  flow_sed_max = (L_sed_upflow_max * V_sed_up * W_sed).to(u.L / u.s)
  print("The maximum flow rate in one sedimentation tank is",flow_sed_max)

**Answer:** The maximum flow rate in one sedimentation tank is 6.187 liter / second.

The maximum sed tank flow rate dictates the required pipe diameter for the manifold and launder.

7. What is the minimum inner diameter of the sedimentation tank manifold?
:math:`Q = \frac{\bar v*\pi*D^2}{4}`

8. What is the required nominal pipe diameter given this flow rate?
The function from the pipe database can return the nominal diameter from the diameter and SDR.

.. code:: python

  D_sed_manifold_min= pc.diam_circle(flow_sed_max / V_sed_manifold_max)
  ND_sed_manifold = pipe.ND_SDR_available(D_sed_manifold_min, SDR)
  print('The minimum inner diameter of the sedimentation tank manifold is',D_sed_manifold_min.to(u.inch))
  print('The nominal diameter of the sedimentation tank manifold is',ND_sed_manifold)

**Answer:** The minimum inner diameter of the sedimentation tank manifold is 7.266 inch. The nominal diameter of the sedimentation tank manifold is 8 inch.

Sedimentation Tank Bays and Number of Diffusers
===============================================
The design will be for a 60 liter per second plant.
1. What is the total required plan area for the sedimentation tanks?

.. code:: python

  flow_plant = 60 * u.L / u.s
  A_sed_flocblanket_total = (flow_plant / V_sed_up).to(u.m**2)
  print('The plant view area of the floc blanket is',A_sed_flocblanket_total)

**Answer:** The plant view area of the floc blanket is 60 square meters.

2. What is the total length of the floc blanket zone for all tanks?

.. code:: python

  L_sed_flocblanket_total = (A_sed_flocblanket_total / W_sed).to(u.m)
  print(L_sed_flocblanket_total)

**Answer:** The total length of the floc blanket zone for all tanks is 56.24 meters.

3. How many sedimentation tanks are required to treat the total plant flow?  The plant flow rate is the basis of design and the maximum sed tank flow rate is based on the manifold diameter.

.. code:: python

  N_sed_tanks = int(np.ceil(flow_plant / flow_sed_max))
  print('The required number of sedimentation tanks is',N_sed_tanks)

**Answer:** The required number of sedimentation tanks is 10.

4. How much water (in L/s) can all of the sedimentation tanks for the plant treat?

.. code:: python

  flow_sed_tanks_total = flow_sed_max * N_sed_tanks
  print(flow_sed_tanks_total)

**Answer:** The total amount of water this plant could treat is 61.87 liter / second. It is slightly larger than the basis of design due to the needs for an integer number of sedimentation tanks.

5. How many diffusers are required in each tank? Assume the maximum length of the upflow region of the sedimentation tank is used.

.. code:: python

  N_sed_tank_diffusers = int(np.floor(((L_sed_flocblanket_total/N_sed_tanks) / B_diffuser).to(u.dimensionless)))
  print('The number of diffuser pipes per sed tank is',N_sed_tank_diffusers)

**Answer:** The number of diffuser pipes per sedimentation tank is 98.

Plate Settler Design
=====================

We will assume that the active area of the sedimentation tank is equal to the top area of the floc blanket zone. This isn't quite right because of the geometric constraints from the floc hopper, inlet channel, settled water channel, and angled plates. However, it is a good approximation for these long tanks.

1. What is the required length of the plate settlers?

The equation for this problem can be found in :ref:`Sedimentation Derivations<heading_Sed_Tank_Plate_Settlers>`.

.. code:: python

  L_sed_plate = ((s_sed_plate * ((V_sed_up/V_sed_capture)-1) + thickness_sed_plate * (V_sed_up/V_sed_capture)) / (np.sin(angle_sed_plate) * np.cos(angle_sed_plate))).to(u.m)
  print('The minimum length of the plate settlers is',L_sed_plate)

**Answer:** The minimum length of the plate settlers is 0.4619 meters.

2. What is the horizontal spacing (center to center) of the plate settlers?

The equation for this problem can be found in :ref:`Sedimentation Derivations<heading_Sed_Tank_Plate_Settlers>`.

.. code:: python

  B_sed_plate_horizontal = ((thickness_sed_plate + s_sed_plate)/ np.sin(angle_sed_plate)).to(u.cm)
  print('The horizontal center to center spacing of the plate settlers is',B_sed_plate_horizontal)

**Answer:** The horizontal center to center spacing of the plate settlers is 3.118 centimeter.

3. Approximately how many plate settlers spaces are needed in each sedimentation tank? Assume the maximum length of the upflow region of the sedimentation tank is used. Neglect the lost space at the end of the sedimentation tank due to the angle of the plate settlers.

.. code:: python

  N_sed_plates_pertank = int(round((((L_sed_flocblanket_total/N_sed_tanks) / B_sed_plate_horizontal)).to(u.dimensionless)))
  print('The number of plate settlers per sedimentation tank is',N_sed_plates_pertank)

**Answer:** The number of plate settlers per sedimentation tank is 180.


Comments, Corrections, or Questions
====================================

This textbook is an ever-evolving project. If you find any errors while you are reading, or if you find something unclear, please let the authors know. Write your comment in `this Github issue <https://github.com/AguaClara/Textbook/issues/86>`_ and it will be addressed as soon as possible. Please look at other comments before writing your own to avoid duplicate comments.
