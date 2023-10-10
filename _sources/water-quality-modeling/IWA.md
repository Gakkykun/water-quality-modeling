---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.14.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# IWA River Water Quality Model

## Background
The river water quality model no.1 (RWQM1) was developed to facilitate practical application of river water quality modeling {cite}`reichert2001river`. It offers a suitable description of nutrient and community dynamics and represents flows of carbon, hydrogen, oxygen, nitrogen and phosphorus through an aquatic “food web” {cite}`broekhuizen2012modification`. The followings are assumptions of this model:



- The elemental composition (e.g., C, H, O, N, and P) of all compounds and organisms is constant.
- The stoichiometry of all processes is constant.
- No adaptation of specific organisms takes place.
- Changes in the composition within organism classes are neglected.

Although there are a number of biological and chemical water quality transformation processes occurring in real nature, identification of main processes is an important step in the modeling. Kinetics and stoichiometry relevant to those processes are incorporated into mass balance equations: 

$$
\frac{\partial C_i}{\partial t} = r_i = \sum\nu_{i,j}\rho_j
$$

where $r_i$ is a reaction rate, $\nu_{i,j}$ is a stoichiometry, and $\rho_j$ is a process rate. The IWA model deals with dissolved (denoted as $S$) and particulate (denoted as $X$) materials as shown below.


|Symbol | Dissolved component | Unit |
|---------------------------|-------------|-------------|
| $S_S$ | Dissolved organic substrate for heterotrophic growth | g COD $m^{-3}$ |
|  $S_I$ | Dissolved inert substrate for heterotrophic growth | g COD $m^{-3}$ |
| $S_{NH_4}$, $S_{NH_3}$ | Ammonium and ammonia | g N $m^{-3}$ |
| $S_{NO_2}$, $S_{NO_3}$ | Nitrite- and nitrate-NAmmonium and ammonia | g N $m^{-3}$ |
| $S_{HPO_4}$,$S_{H_2PO_4}$ | Two components of dissolved inorganic P | g P $m^{-3}$ |
| $S_{O_2}$ | Dissolved oxygen | g $O_2$ $m^{-3}$ |
| $S_{CO_2}$,$S_{HCO_3}$,$S_{H_2CO_3}$ | Components of total dissolved inorganic carbon | g C $m^{-3}$ |
| $S_{H}$ | Hydrogen ion | g H $m^{-3}$ |
| $S_{OH}$ | Hydroxyl-ion | g H $m^{-3}$ |
| $S_{Ca}$ | Calcium ion | g Ca $m^{-3}$ |
| $X_H$ | Heterotrophic osmotrophs | g COD $m^{-3}$ |
| $X_{N1}$ | First-stage nitrifiers: oxidizing ammonia to nitrite | g COD $m^{-3}$ |
| $X_{N2}$ | Second-stage nitrifiers: oxidizing nitrite to nitrate | g COD $m^{-3}$ |
| $X_{ALG}$ | Photosynthetic organisms | g COD $m^{-3}$ |
| $X_{CON}$ | Consumer organisms | g COD $m^{-3}$ |
| $X_{S}$ | Non-refratory particulate organic matter | g COD $m^{-3}$ |
| $X_{I}$ | Refratory particulate organic matter | g COD $m^{-3}$ |
| $X_{P}$ | Phosphate adsorbed to particles | g COD $m^{-3}$ |


Before looking at the IWA model, let's first consider the very simple system in which there are just three components: bacteria, substrate and dissolved oxygen. Process kinetics and stoichiometry for heterotrophic bacterial growth and decay in an aerobic environment are summarized in a matrix form. The column represents the three components, whereas the row does processes involved in this sytem, which are growth and decay. There is an additional column for process rates.

Using this matrix, we can mechanistically construct reaction rates for each component, simply multiplying a stoichiometry by a process rate in the same row and sum them in the column direction. Reaction rates for bacteria biomass ($X_B$), soluble substrate($S_s$), dissolved oxygen($S_o$) are given by:

For biomass:

$$
r_{X_B} = 1\cdot{\frac{\hat{\mu}S_s}{K_s + S_S}}X_B - 1\cdot{bX_B}
$$

For substrate:

$$
r_{S_s} = -\frac{1}{Y}\frac{\hat{\mu}S_s}{K_s + S_S}X_B
$$

For dissolved oxygen

$$
r_{S_o} = -\frac{1-Y}{Y}\frac{\hat{\mu}S_s}{K_s + S_S}X_B - 1\cdot{bX_B}
$$

Now, let's move on to how bacteria population is formulated, which is referred to as Monod kinetics. A specific growth rate, which has a unit of $\frac{1}{d}$, represents a ratio of biomass variation ($\frac{dX}{dt}$) to the biomass ($X$):

$$
\mu \equiv \frac{1}{X}\frac{dX}{dt}
$$

In Monod equation, $\mu$ is represented by:

$$
\mu = \mu_{max}\frac{S}{K_S+S}
$$

where $\mu_{max}$ is a maximum specific growth rate and $K_S$ is a half-saturation constant, which is equivalent to a substrate concentration achieving a half of the maximum specific growth rate as can be seen in Figure \ref{fig:monod}. Combining the above two equations, we can get:

$$
\frac{dX}{dt}\vert_{growth} = \mu X = \mu_{max} \frac{S}{K_S+S} X
$$

When a substrate concentration is much larger than $K_S$, a specific growth rate does not vary as the substrate concentration increases and it is zero-order, while it is first order when the substrate concentration is much smaller than $K_S$.

We saw the inhibitory effect of substrate on growth above, but there are other inhibitory effects to be considered, which are temperature and light intensity (only considered for $X_{ALG}$ in the model). 

Process rates covered in the IWA model have a common components, which are a rate constant, a temperature correction ($e^{\beta (T - T_0)}$), Monod-type fomulas ($\frac{S}{K + S}$),  and particulate concentration ($X$). Light intensity effect ($\frac{I}{K_I}exp(1 - \frac{I}{K_I})$) is only considered for algae. For Monod-type formulas, when a concentration is very low, a switching function is used: $\frac{K}{K+S}$, thereby this term is going to be nearly 1 to avoid making the process rate zero. 

Form the perspectives of substrate decomposition, we introduce a yield coefficient, which is a sort of conversion factor, indicating the increase in biomass with a unit of mg per 1 mg of substrate. The value is typically in the range of 0.4 to 0.8 for aerobic heterotophic bacteria. 

$$
\frac{dS}{dt}\vert_{decomposition} = \frac{1}{Y_{X/S}} \frac{dX}{dt}\vert_{growth}
$$

Table lists stoichiometric parameters used in the IWA model. Values included in this matrix are calculated from stoichiometric parameters such as yield coefficients.

| Symbol | Description | Unit |
|---------------------------|-------------|-------------|
| $Y_{H,aer}$ | Yield for aerobic heterotrophic growth | $gX_H/gS_s$  |
| $Y_{H,anox, NO_3}$ | Yield for anoxic heterotrophic growth with nitrate | $gX_H/gS_s$  |
| $Y_{H,anox, NO_2}$ | Yield for anoxic heterotrophic growth with nitrite | $gX_H/gS_s$  |
| $Y_{N_1}$ | Yield for growth of 1st step nitrifiers | $gX_{N1}/gS_{NH_4-N}$  |
| $Y_{N_2}$ | Yield for growth of 2nd step nitrifiers | $gX_{N2}/gS_{NO_2-N}$  |
| $Y_{CON}$ | Yield for grazing | $gX_{CON}/gX_{ALG}$  |

Let's look at how to construct a reaction rate for nitrite as an example. There are four stoichiometric coefficients (i.e., 1.1, -1.6, 4.7, -21) as you can see in Figure \ref{fig:stoichi-matrix}. We multiply them by process rates in corresponding rows (i.e., 3a, 3b, 5 and 7), and add all of them.

$$
r_{NO_2} = 1.1\cdot\rho_{3a} - 1.6\cdot \rho_{3b} + 4.7\cdot \rho_{5} -21\cdot \rho_{7}
$$

where $\rho_{3a}$, $\rho_{3b}$, $\rho_{5}$ and $\rho_{7}$ are given below:

$$
  \begin{split}
    \rho_{3a}& = k_{gro,H,anox, T_0} \cdot e^{\beta_H(T-T_0)} \\
    & \times \frac{S_s}{K_{S,H,anox}+S_S}\frac{K_{O2,H,aer}}{K_{O2,H,aer}+S_{O2}} \\
    & \times \frac{S_{NO3}}{K_{NO3,H,anox}+S_{NO3}}\frac{S_{HPO4}+S_{H2PO4}}{K_{HPO4,H,anox} + S_{HPO4}+S_{H2PO4}}X_H
  \end{split}
$$

$$
  \begin{split}
    \rho_{3b}& = k_{gro,H,anox, T_0} \cdot e^{\beta_H(T-T_0)} \\
    & \times \frac{S_s}{K_{S,H,anox}+S_S}\frac{K_{O2,H,aer}}{K_{O2,H,aer}+S_{O2}} \\
    & \times \frac{S_{NO2}}{K_{NO2,H,anox}+S_{NO2}}\frac{S_{HPO4}+S_{H2PO4}}{K_{HPO4,H,anox} + S_{HPO4}+S_{H2PO4}}X_H
  \end{split}
$$

$$
  \begin{split}
    \rho_{5}& = k_{gro,N1,anox, T_0} \cdot e^{\beta_{N1}(T-T_0)} \\
    & \times\frac{S_{O2}}{K_{O2,N1}+S_{O2}}\frac{S_{NH4}+S_{NH3}}{K_{NH4,N1}+S_{NH4}+S_{NH3}} \\
    & \times \frac{S_{HPO4}+S_{H2PO4}}{K_{HPO4,H,anox} + S_{HPO4}+S_{H2PO4}}X_{N1}
  \end{split}
$$

$$
  \begin{split}
    \rho_{7}& = k_{gro,N2, T_0} \cdot e^{\beta_{N2}(T-T_0)} \\
    & \times\frac{S_{O2}}{K_{O2,N2}+S_{O2}}\frac{S_{NO2}}{K_{NO2,N2}+S_{NO2}} \\
    & \times \frac{S_{HPO4}+S_{H2PO4}}{K_{HPO4,H,anox} + S_{HPO4}+S_{H2PO4}}X_{N2}
  \end{split}
$$

## Implementation
We adapt a tanks-in-series model to simplify the complex system of a real river. A river branch is segmented into conceptual reservoirs based on the locations of interest, boundary locations and network set-up~\cite{keupers2017development}.

$$
V_i \frac{dC_i}{dt} = Q(C_{i-1} - C_i) + r_i V_i
$$

In the simulation, we need to determine the target area. The covered part is a river stream between Jugo and Kuki, which is 2750 m long. A river receives rainwater as well as wastewater discharged from households at several points. There are three mixing points (named Jugo, Kyugo, and Chubu) to be considered in this simulation as shown in Table. At each mixing point, we update values of a concentration, a flow velocity, and the number of reservoirs in the downstream. Concentrations after mixing are used as the new initial conditions. In between Chubu and Kuki, wastewater is directly discharged from households into the river. Those nonpoint sources are introduced as constant model inputs for each segment. Concentrations of water quality parameters such as BOD, DO, $NH_4$, $NO_2$+$NO_3$ are monitored monthly at Chubu and Kuki.


| Point | Distance (m) | Flow of wastewater ($m^3$) | Covered land area ($m^{2}$)  | 
|---------------------------|-------------|-------------|-------------|
| Jugo    |   0   | 135.0    | 926,000  |
| Kyugo     |   390   | 52.7     | 860,000 |
| Chubu     |   1260   | 78.9    | 1,521,000 |
| Kuki     |  2750   | 421.4     | 688,000 |

Inputs of the model are monthly average water temperature, irradiation, and precipitation. A flow rate after mixing ($Q_{all}$) is the sum of the river flow ($Q_{river}$), a discharge due to precipitation ($ Q_{precipitation}$), an amount of wastewater discharged from septic tanks ($Q_{discharge}$):


$$
Q_{all} =  Q_{river} + Q_{discharge} + Q_{precipitation}
$$  

$$
Q_{precipitation} [m^3/d] = 0.74 \times Land\:area [m^2] \times \frac{Precipitation\:[mm/month]}{(1000 \times 30)} 
$$

$$
Q_{discharge} [m^3/d] = 250 [L] \times Population \times \frac{1}{1000}
$$


$Q_{river}$ at Jugo is estimated like $Q_{precipitation}$; in other locations, $Q_{river}$ is a previous state of $Q_{all}$ in a loop of the simulation. After mixing, a horizontal flow velocity ($u$) can be calculated using the following two equations. Equating the right-hand sides of two equations allows us to calculate a depth ($H$) using a iterative method which is compiled as a function as shown below.

$$
u = \frac{Q_{all}}{B \times H} 
$$

$$
u = \frac{1}{n} \times R^{2/3} \times I^{1/2}
$$

where, $B$ is the width, $n$ is a Manning roughness coefficient, $R$ is a hydraulic radius and $I$ is a slope. While values for $B$, $n$ and $I$ are fixed during simulation, $R$ is calculated by:

$$
R = \frac{B \times H}{2H + B}
$$

```Fortran
function depth_calc(ALL_River_B, ALLRiver_I, ALLRiver_n, ALL_River_Q) result(ALL_RIver_H)

 real(real64), intent(in) :: ALL_River_B, ALLRiver_I, ALLRiver_n, ALL_River_Q

 integer i
 real(real64) ALL_RIver_H, f, fp, ALL_River_Q_sec

 !initial guess
 ALL_RIver_H = 0.2d0
 ALL_River_Q_sec = ALL_River_Q/dble(86400)

 do i = 1, 5
   f = (ALL_River_B * ALLRiver_I**0.5)/(ALLRiver_n * ALL_River_Q_sec)* ALL_RIver_H**1.67 - (2.0d0*ALL_RIver_H/ALL_River_B  + 1)**0.67

   fp = 5.0d0/3.0d0 * (ALL_River_B  * ALLRiver_I**0.5) /(ALLRiver_n * ALL_River_Q_sec) * ALL_RIver_H**0.67 - 2.0d0/3.0d0 * (2.0d0*ALL_RIver_H/ALL_River_B  + 1)**(-0.33) * (2.0d0/ALL_River_B)
  
   ALL_RIver_H  = ALL_RIver_H  - f/fp
 end do
 end function depth_calc
```

Dispersion coefficient ($E$) and division number ($Div$) are estimated by the following equations:

$$
E = 2 \times \sqrt{gRI} \times H \times (\frac{B}{H})^{1.5}
$$

$$
Div = \frac{Pe}{2} + 1 = \frac{u \times L}{2E} + 1
$$

where $Pe$ is a Peclet number and calculated by $\frac{u\times L}{E}$. $L$ is a length of a river reach between mixing points. We use the 4th-order Runge-Kutta method to solve the differential equation for a tanks-in-series model presented at the begining of this chapter:

$$
V \frac{dC}{dt} = Q(C_{in} - C) + rV
$$

First, we discretize the time derivative.

$$
V\frac{C_t - C_{t-1}}{\Delta t} = Q(C_{in} - C) + rV
$$

After rearranging the above equation, we get the following equation, introducing $k$. Please note that $r$ is a function of $C$.

$$
\begin{split}
  C_t& = C_{t-1} + k \Delta t \\
  & = C_{t-1} + \frac{k_1 + 2k_2 + 2k_3 + k_4}{6} \Delta t
\end{split}
$$

where,

$$
k_1 = \left\lbrace \frac{Q}{V}(C_{t-1,x-1} - C_{t-1,x}) + r(C_{t-1,x}) \right\rbrace
$$

$$
k_2 = \left\lbrace \frac{Q}{V}(C_{t-1,x-1} - (C_{t-1,x} + \frac{k_1}{2} dt)) + r(C_{t-1,x} + \frac{k_1}{2} dt))\right\rbrace 
$$

$$
k_3 = \left\lbrace \frac{Q}{V}(C_{t-1,x-1} - (C_{t-1,x} + \frac{k_2}{2} dt)) + r(C_{t-1,x} + \frac{k_2}{2} dt))\right\rbrace 
$$

$$
k_4 = \left\lbrace \frac{Q}{V}(C_{t-1,x-1} - (C_{t-1,x} + k_3 dt)) + r(C_{t-1,x} + k_3 dt)\right\rbrace 
$$

We use three $\verb|do loop|$s as can be seen below. The most inner loop is for calculation of $\verb|k|$. After this loop, we will estimate concentrations at the next time level using the 4th-order Runge-Kutta method. Below is the excepted code for one of items. We prepare a 2-D array for each water quality item (e.g., $\verb|ALLRiver_SS|$ for $\verb|SS|$).

```Fortran
 do Time_Pointer = 2, Time_Total
   do Loc_Pointer = 2, Div_Total
     do Runge_Pointer = 1, 4
       RungeK(1, Runge_Pointer) = ...
     end do !Runge_Pointer

     ALLRiver_SS(Time_Pointer,Loc_Pointer) = ALLRiver_SS(Time_Pointer-1,Loc_Pointer) + ((RungeK(1,1) + 2.0d0*RungeK(1,2) + 2.0d0*RungeK(1,3) + RungeK(1,4))*deltaT/6.0d0
```

$\verb|RungeK|$ is a 2-D array and corresponds to $k_1$ to $k_4$. The second dimension of $\verb|RungeK|$ is assigned for \verb|Runge_Pointer|. As previously explained, in the area between Chubu and Kuki, wastewater discharged from each household directly enters the stream. A reservoir in the reach receive not only inflow from the previous reservoir as well as the direct discharged wastewater. We assume that the directly discharged wastewater equally flows to each reservoir.

```Fortran
 if (mixingPoint == 3) then
  do i = 1, 14
   RungeK(i, Runge_Pointer) = (1/ ALLRiver_TankV) * (ALLRiver_Q * Tempval_Inf(i) + (SepticDischarge(4)/Div_Total) * (Direct_DischargeWQ(i)/SepticDischarge(4)) - (ALLRiver_Q + SepticDischarge(4)/Div_Total) * TempPointer(i)) +  ReactionTerm(i, 1)
  end do 
 else 
  do i = 1, 14
   RungeK(i, Runge_Pointer) = (ALLRiver_Q / ALLRiver_TankV) * (Tempval_Inf(i) - TempPointer(i)) + ReactionTerm(i, 1)
  end do 
 end if

```

 Please note that $\verb|Tempval_Inf|$ indicates concentrations in the previous tank so that $\verb|Loc_Pointer-1|$ is allocated.

 ```Fortran
Tempval_Inf(1) = ALLRiver_SS(Time_Pointer-1, Loc_Pointer-1)
 ```
$\verb|TempPointer|$ (corresponds to $C_{t-1,x-1}$), and $\verb|reactionTerm|$ are different depending on $\verb|Runge_Pointer|$.

```Fortran
if (Runge_Pointer == 1) then
   TempPointer(1) = ALLRiver_SS(Time_Pointer-1,Loc_Pointer)
 else if (Runge_Pointer == 2) then
   TempPointer(1) = ALLRiver_SS(Time_Pointer-1,Loc_Pointer) + (RungeK(1,1) * deltaT / 2.0d0)
 else if (Runge_Pointer == 3) then
   TempPointer(1) = ALLRiver_SS(Time_Pointer-1,Loc_Pointer) + (RungeK(1,2)  * deltaT / 2.0d0)
 else if (Runge_Pointer == 4) then
   TempPointer(1) = ALLRiver_SS(Time_Pointer-1,Loc_Pointer) + RungeK(1,3)  * deltaT
 end if
```

As we saw in the background sub-section, a reaction rate, which corresponds to $\verb|reactionTerm|$, is calculated using a matrix operation.

$$
r_i = \sum\nu_{i,j}\rho_j
$$

Here is the best occasion to use $\verb|matmul|$, a built-in function for matrix multiplication. $\verb|Chemsp|$ and $\verb|ProcessMat|$ are 2-D arrays, storing stoichiometry values and process rate equations, respectively.

```Fortran
reactionTerm = matmul(transpose(Chemsp), ProcessMat)
```

Model variables in the IWA river water quality model does not necessarily match routinely-monitored water quality items in the field. We need to convert data to compare simulation outputs with observed data. As we have data of BOD, SS, ammonium, nitrate, nitrite and DO, we prepare five additional arrays as the following:

```Fortran
 if (mod(Time_Pointer, print_count).eq. 0) then
    ALLRiver_BOD(:) = ALLRiver_SS(Time_Pointer, :) + ALLRiver_XS(Time_Pointer, :)
    
    ALLRiver_SSolid(:) = ALLRiver_XH(Time_Pointer, :) + ALLRiver_XN1(Time_Pointer, :) + ALLRiver_XN2(Time_Pointer, :) + ALLRiver_XALG(Time_Pointer, :) + ALLRiver_XCON(Time_Pointer, :) + ALLRiver_XS(Time_Pointer, :) + ALLRiver_XI(Time_Pointer, :)
    
    ALLRiver_NOX(:) = ALLRiver_SNO2(Time_Pointer, :) + ALLRiver_SNO3(Time_Pointer, :) 
    
    ALLRiver_NH4(:) = ALLRiver_SNH4(Time_Pointer, :) 
    
    ALLRIver_O2(:) = ALLRiver_SO2(Time_Pointer, :)

    write(11, '(*(g0:,","))') ALLRiver_BOD(:)
    write(12, '(*(g0:,","))') ALLRiver_SSolid(:)
    write(13, '(*(g0:,","))') ALLRiver_NOX(:)
    write(14, '(*(g0:,","))') ALLRiver_NH4(:)
    write(15, '(*(g0:,","))') ALLRiver_O2(:)
  end if
```
