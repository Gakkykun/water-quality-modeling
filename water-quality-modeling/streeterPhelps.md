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

# Streeter Phelps Model

## Background
A pioneering work by Streeter and Phelps {cite}`phelps1958study` characterized basic reaction kinetics in Ohio River. Here are assumptions of the Streeter-Phelps model:
- Constant depth ($z$) and Width ($w$)
- Constant cross-sectional area ($A=z\times w$)
- Constant horizontal flow velocity ($u$)
- Uniform concentrations of BOD and DO at a segment (control volume) of river
- Concurrent occurrence of purification of BOD and consumption of DO

We construct governing equations for BOD and DO based on mass balance. Before that, let's consider a flux, $J$. Flux represents a transfer rate per unit area. In the mass balance equation, we need to multiply $J$ by a cross-sectional area, $A$. In the left-hand side, an accumulation term is placed, while in the right-hand side, input, output and reaction terms are placed.

$$
(Adx)\frac{dC}{dt} = A(J\vert_x - J\vert_{x+dx}) + (Adx)R
$$

The Streeter-Phelps model considers a first-order purification by aerobic microorganisms as degradation of BOD is described as:

$$
R = \frac{dC_{BOD}}{dt} = -k_B C_{BOD}
$$

where $k_B$ is a first-order reaction rate constant. This is incorporated into the mass balance equation of BOD. We replace $J$ with $C_{BOD}(u)$.

$$
(Adx)\frac{\ C_{BOD}}{\partial t} = C_{BOD}\vert_x (uA) - C_{BOD}\vert_{x+dx}(uA) - k_B C_{BOD} (Adx)
$$

Using Taylor expansion, we can replace $C_{BOD}\vert_{x+dx}$  with $C_{BOD}\vert_x + \frac{dC_{BOD}}{dx}dx$. Also, we consider a steady-state condition: $\frac{\partial C_{BOD}}{\partial t} = 0$ so that the right-hand side becomes 0. After rearranging the equation, we get:

$$
u\frac{dC_{BOD}}{dx} + k_BC_{BOD} = 0
$$

An analytical solution of the differential equation for BOD is given by:

$$
C_{BOD} = C_{BOD_0}exp(-\frac{k_B}{u}x)
$$

where we set a boundary condition at $x=0$: $C_{BOD} = C_{BOD_0}$.

For DO, we first consider oxygen dissolution from atmosphere.

$$
  \begin{split}
    J& = -D_m\frac{dC_{DO}}{dz} \\
    & = D_m\frac{C_{DO}^* - C_{DO}}{\delta} \\
    & = \frac{D_m}{\delta}(C_{DO}^* - C_{DO})
  \end{split}
$$

$\frac{D_m}{\delta}$ is replaced with $k_a$, which is called mass transfer coefficient. Please keep in mind that we need to multiply the flux of oxygen dissolution by $wdx$, which corresponds to the surface faced with atmosphere. Mass Balance of DO is constructed as the following:

$$
C_{DO}\vert_x (uA) - C_{DO}\vert_{x+dx}(uA) + k_a(wdx)(C_{DO}^* - C_{DO}) - k_B BOD (Adx) = (Adx)\frac{\ C_{DO}}{\partial t}
$$

Again using Taylor expansion, we can replace $C_{DO}\vert_{x+dx}$ with $C_{DO}\vert_x + \frac{dC_{DO}}{dx}dx$ and consider a steady-state condition: $\frac{\partial C_{DO}}{\partial t} = 0$. After rearranging the equation, we get:

$$
u\frac{dC_{DO}}{dx} + k_B C_{BOD} - k_a^* (C_{DO}^* - C_{DO}) = 0
$$

$k_a^*$ is called re-aeration coefficient and is calculated by $\frac{k_a}{z}$. Finally, An analytical solution of the differential equation for BOD is given by:

$$
C_{DO} = C_{DO}^* - \frac{k_B}{k_a^* - k_B}BOD_0 [exp(-\frac{k_B}{u}x) - exp(-\frac{k_a^*}{u}x)] - (C_{DO}^* - C_{DO_0})exp(-\frac{k_a^*}{u}x)
$$

where $C_{DO_0}$ is a concentration at $x=0$.

## Implementation
The fourth-order Runge-Kutta method is applied to solve the following two differential equations:

$$
\frac{dC_{BOD}}{dx} = -\frac{k_B}{u}C_{BOD}
$$


$$
\frac{dC_{DO}}{dx} = -\frac{k_B}{u}C_{BOD} + \frac{ka_u^*}{u}(C_{DO}^* - C_{DO})
$$

We list parameters used for the calculation. The reaeration coefficients ($k_a^*$) and 1st-order BOD purification rate ($k_B$) are set at $1.0\:d^{-1}$ and $0.5\:d^{-1}$, respectively. The flow rate ($Q_f$) and BOD and DO concentrations of the river before mixing are $5 \times 10^5\:m^3/d$, $0$ and $10\:g/m^3$, respectively. On the other hand, the flow rate of wastewater and BOD and DO concentrations are $5 \times 10^4\:m^3/d$, $200\:g/m^3$ and $0\:g/m^3$, respectively. DO saturation concentration ($C_{DO}^*$) is set at $10\:g/m^3$.

The flow velocity is calculated by:

$$
u = \frac{Q_{in} + Q_f}{A}
$$

Mass balance equations for BOD and DO at $x=0$ are as follows:

$$
Q_f C_{BOD_f} + Q_in C_{BOD_{in}} = (Q_f + Q_{in}) C_{BOD_0}
$$

$$
Q_f C_{DO_f} + Q_in C_{DO_{in}} = (Q_f + Q_{in}) C_{DO_0}
$$

We plug parameter values provided into the above equations to calculate a flow velocity and BOD and DO concentrations at the mixed point
- $u = 27500\:m/d$ 
- $C_{BOD_0} = 18.18\:mg/L$
- $C_{DO_0} = 9.09\:mg/L$

We want to estimate concentrations of BOD and DO at 200 km down stream, setting $\Delta x$ at 0.1 km.

```Fortran
real, dimension(1,2) :: MonitoringData

 open(unit=99, file='input.csv', status='old')
 read(99, '()') !Excluding a header
 read(99,*) MonitoringData(1,1), MonitoringData(1,2)

 conc_bod(0) = MonitoringData(1, 1)
 conc_do(0) = MonitoringData(1, 2)

conc_bod(0) = MonitoringData(1, 1)
 conc_do(0) = MonitoringData(1, 2)

```


We prepare two functions to calculate slopes for differential equations of BOD and DO, which are $\verb|func_bod|$ and $\verb|func_do|$.

```Fortran
contains
 function func_bod(conc) result(retval)
 real(real64), intent(in) :: conc
 real(real64) retval
 real(real64) :: K_B = 0.5d0, U = 27500.d0

  retval = -K_B/U*conc

 end function func_bod

 function func_do(conc) result(retval)
  real(real64), intent(in) :: conc
  real(real64) retval
  real(real64) :: K_a = 1.0d0, U = 27500.d0, DO_S = 10.0d0

  retval = K_a/U*(DO_S - conc)

 end function func_do

```

Here is the main program.
```Fortran
do i=0, nsteps-1
  k1B = func_bod(conc_bod(i))
  k2B = func_bod(conc_bod(i)+0.5*l*k1B)
  k3B = func_bod(conc_bod(i)+0.5*l*k2B)
  k4B = func_bod(conc_bod(i)+l*k3B)
 
  k1D = k1B + func_do(conc_do(i))
  k2D = k2B + func_do(conc_do(i)+0.5*l*k1D)
  k3D = k3B + func_do(conc_do(i)+0.5*l*k2D)
  k4D = k4B + func_do(conc_do(i)+l*k3D)
   
  conc_bod(i+1) = conc_bod(i)+l/6.0*(k1B+2.0*k2B+2.0*k3B+k4B)
  conc_do(i+1) = conc_do(i)+l/6.0*(k1D+2.0*k2D+2.0*k3D+k4D)
   
 end do

```

We output results, which is eventually displayed in ParaView. We do not need all data, but get data at every 10 step, which is equivalent to every 1 km.

```Fortran
open(unit=10, file='concentration.csv')
 
 write(10, *) 'x, bod, do'
 
 do j = 0, nsteps
  if (mod(j, 10) .eq. 0) then
   write(10, '(*(f6.2:, ","))') j*dx, conc_bod(j), conc_do(j)
  end if
 end do
```


Now. let's compare numerial results with analytical solutions. This is one way to verify numerical simulations. If errors between those two are very small, we can be confident in the numerical results. Here, we introduce a criteria to evaluate that error, which is mean absolute error (MAE): $\sum_{i=1}^{N}\frac{Analytical_i - Numerical_i}{N}$. First, let's create functions to calculate analytical solutions for both BOD and DO as the following:

```Fortran
function bod_analytical(distance) result(retval)
  real(real64), intent(in) :: distance 
  real(real64), parameter :: K_B = 0.5, U = 27500., Initial_bod = 18.18 
  real(real64) retval

  retval = Initial_bod*exp(-K_B/U*distance)
  end function bod_analytical

 function do_analytical(distance) result(retval)
  real(real64), intent(in) :: distance
  real(real64), parameter :: K_B = 0.5d0, U = 27500.d0, K_a = 1.0d0, DO_S = 10.0d0, Initial_bod = 18.18d0, Initial_do = 9.09d0
  real(real64) retval
  retval = DO_S - K_B/(K_a - K_B)*Initial_bod*(exp(-K_B/U*distance)-exp(-K_a/U*distance)) &
        - (DO_S - Initial_do)*exp(-K_a/U*distance)
 end function do_analytical 
```

Meanwhile, we add new arrays to store analytical solutions as well as erros and MAEs.

```Fortran
 real(real64) error_bod, error_do, mae_bod, mae_do
 real(real64) conc_bod_a(0:2001), conc_do_a(0:2001)
```


We set errors at 0 initially and update them inside the do loop by taking absolute values of difference between analytical solutions and numerical results. After the do loop, we calculate MAE by dividing the sum of error by the number of data, which is equal to $\verb|nsteps|$.


```Fortran
error_bod = 0.d0
 error_do = 0.d0

 do i=0, nsteps-1
   conc_bod_a(i+1) = bod_analytical(l*(i+1))
   conc_do_a(i+1) = do_analytical(l*(i+1))

   error_bod = error_bod + abs(conc_bod_a(i+1)-conc_bod(i+1))
   error_do = error_do + abs(conc_do_a(i+1)-conc_do(i+1))
 end do

 mae_bod = error_bod / nsteps
 mae_do = error_do / nsteps

 print*, 'Mean absolute error for BOD =', mae_bod
 print*, 'Mean absolute error for DO =', mae_do

```