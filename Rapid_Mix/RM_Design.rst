.. _title_Rapid_Mix_Design:

*******************
Rapid Mix Design
*******************

As of 2018 the design for AguaClara rapid mix units has been based on the goal of achieving a target energy dissipation rate. This in turn was based on the assumption that it was important to rapidly mix the coagulant with the water, perhaps to minimize the self-aggregation of coagulant nanoparticles. We don't yet have any experimental evidence that rapid mixing is important and it is quite likely that the energy dissipation rate found in the hydraulic flocculator is sufficient to provide the required mixing.

The design requirements for fluid mixing of the coagulant is an area that needs research. If the goal is to reduce the amount of self-aggregation of coagulant nanoparticles, then it is possible that an investment in energy to mix faster could be offset by a reduction in the chemical demand. This tradeoff needs a full economic and resource-use analysis. Regardless of the outcome of that analysis, it is essential that we evaluate options to reduce the energy input required to reduce the mixing time. One excellent option for reducing the energy required is to reduce the length scale of the largest eddies that are required for mixing.

The mixing time is dominated by the turnover time of the largest eddies. Given an energy dissipation rate (or a velocity gradient) the mixing time is reduced if the required length scale of the mixing is reduced.

.. math::

    t_{eddy} \approx \left( \frac{L_{eddy}^2}{ \bar\varepsilon }\right)^\frac{1}{3}

We will design a rapid mix unit that has an array of injection ports and couple that with flow expansions that will generate eddies at that same length scale. The reason for creating flow contractions at the same length scale as the distance between injection ports is to immediately create eddies of the right size rather than creating much larger eddies and then waiting for the turbulence cascade of eddies sizes to create the smaller eddies. By creating eddies with dimensions similar to the injection port separation we will dissipate the energy as quickly as possible (at that smaller length scale) and thus decrease the mixing time.

We can use the center to center distance of the contractions (and injection ports) as the eddy length scale. This is because the eddies will need to mix over the length scale of the spacing of the injection ports.

The relationship between head loss and energy dissipation rate is based on conservation of energy with :math:`\theta` representing the time required for most of the energy to be dissipated.

.. math::

    g h_e = \theta \bar\varepsilon

We will assume that the time for the energy to dissipate, :math:`\theta`, is :math:`t_{eddy}`. Eliminate the unknown :math:`\bar\varepsilon`.

.. math::

    t_{eddy} \approx \left( \frac{L_{eddy}^2 t_{eddy}}{ g h_e }\right)^\frac{1}{3}

where :math:`t_{eddy}` is the mixing time and :math:`h_e` is the head loss that will be used to obtain the mixing. Solve for :math:`L_{eddy}` which is the spacing of the contractions and the spacing of the coagulant injection points.

.. math::

    L_{eddy} \approx  t_{eddy} \sqrt{g h_e }

The raw water flow rate per injection port is equal to the water velocity upstream from the contractions in the channel or pipe multipled by the area of flow dedicated to one injection port.

.. math::

    Q_mixing_zone = L_{eddy}^2 \bar v_{exp}

Substituting the previous equation for the eddy length scale, :math:`L_{eddy}`, we obtain

.. math::

    Q_{mixer} = g h_e t_{eddy}^2 \bar v_{exp}

The flow rate that can be served with a single injection port is a function of how much energy we use for the mixing process.

.. code:: python

  n_points = 50
  h_e_graph = np.logspace(-1,1,n_points) * u.m

  v_channel = 0.45 * u.m/u.s
  def Q_per_mixer(t_eddy):
  return (u.gravity * h_e_graph * t_eddy**2 * v_channel).to(u.L/u.s)

  plt.plot(h_e_graph,Q_per_mixer(1*u.s),linewidth=3)
  plt.plot(h_e_graph,Q_per_mixer(0.3*u.s),linewidth=3)
  plt.plot(h_e_graph,Q_per_mixer(0.1*u.s),linewidth=3)
  plt.xscale("log")
  plt.yscale("log")
  plt.xlabel('Head loss (m)')
  plt.ylabel('Flow rate (L/s)')
  plt.grid(which='both')
  plt.legend(['1 s','0.3 s','0.1 s'])
  plt.gca().yaxis.set_major_formatter(ticker.ScalarFormatter())
  plt.gca().xaxis.set_major_formatter(ticker.ScalarFormatter())

The flow rate per mixing zone increases rapidly as the mixing time allowed is increased and as the energy input is increased.

.. _figure_flow_per_mixing_zone:

.. figure::    Images/Flow_per_mixing_zone.png
    :width: 700px
    :align: center
    :alt: Flow per mixing zone

    The flow per mixing zone increases with the amount of energy used. The amount of energy used can be decreased by a factor of 100 if multiple injection ports are used.

The rapid mix unit will be created by placing round cylinders vertically in the inlet channel or pipe. The goal is to minimize the number of chemical injection points and thus to use as large a spacing of the cylinders, :math:`L_{eddy}`, as possible.

The dimensions of the opening between cylinders and the diameter of the cylinders can be obtained by analyzing the head loss through a flow expansion.

.. math::

    h_e = \left(\frac{A_{exp}}{A_{con}} -1 \right)^2 \, \frac{\bar  v_{exp}^2}{2g}

where con = contracted control surface and exp = expanded control surface. We can solve the head loss equation for the dimensions of the contractions. First, solve for the area ratio

.. math::

   \frac{A_{exp}}{A_{con}}=\frac{\sqrt{2gh_e}}{\bar  v_{exp}} + 1

Here the area ratio is also equal to the width ratio because the depth of flow is the other dimension. We assume here that the depth of flow is large compared with the head loss.

.. math::

   \frac{A_{exp}}{A_{con}} = \frac{\bar v_{con}}{\bar v_{exp}}



.. math::

   \frac{W_{con}}{W_{exp}} = \frac{A_{con}}{A_{exp}}

The width of the expanded flow, :math:`W_{exp}`, is equal to the large eddy length scale, :math:`L_{eddy}`.

.. math::

    W_{con} = L_{eddy}\frac{A_{con}}{A_{exp}}

The diameter of the cylinders is equal to

.. math::

    D_{cylinder}=W_{exp} - W_{con}

Below is an example design for a rapid mix unit that uses 20 cm of head loss and achieves mixing in 0.3 seconds.

.. code:: python

  Head_loss_max = 20 * u.cm
  t_eddy = 0.3 * u.s
  L_eddy = (t_eddy * np.sqrt(u.gravity * Head_loss_max)).to(u.m)
  print('The spacing between injection ports is',L_eddy)


  #expanded velocity
  v_channel = 0.45 * u.m/u.s
  Q_per_mixer = (v_channel * L_eddy**2).to(u.L/u.s)
  print('The flow rate of raw water per chemical injection point is',Q_per_mixer)

  Q=20000/7 * u.L/u.s #Fairmont design
  A_channel = Q/v_channel
  n_ports = (A_channel/L_eddy**2).to(u.dimensionless)
  print('The number of injection ports is',(np.round(n_ports)).magnitude)

  Pi_A = np.sqrt(2*u.gravity*Head_loss_max)/v_channel + 1
  print('The expansion ratio is',Pi_A)
  v_jet = v_channel * Pi_A
  w_contraction = L_eddy/Pi_A
  print('The width of the contractions is',w_contraction)

  D_cyl = L_eddy - w_contraction
  print('The diameter of the cylinder is',D_cyl)

* The spacing between injection ports is 0.4201 meter
* The flow rate of raw water per chemical injection point is 79.43 liter / second
* The number of injection ports is 36.0
* The expansion ratio is 5.401 dimensionless
* The width of the contractions is 0.07779 meter
* The diameter of the cylinder is 0.3424 meter


.. todo:: Add a section on conventional design for a comparison.
