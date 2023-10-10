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

# Lake Model

## Background

In this section, we will see vertical one-dimentional ecological model, in which advection and dispersion differential equations are applied. Advection and dispersion terms included in all of mass balance equations for water quality items of interest are summarized as $F(X)$, where $X$ is a water quality item. Let's see how each term of $F(X)$ is derived. Symbols that appear in equations are listed in tables. For advections in the horizontal and vertical directions, we describe them as:

$$
u_{in}(Bdy)X_{in}-u_{out}(Bdy)X
$$

$$
VX\vert_{y}A-VX\vert_{y+dy}A
$$
We can use Taylor series expansion: $X\vert_{y+dy} = X\vert_{y} + \frac{\partial X}{\partial y}dy$ and the vertical advection term is going to be: $-VA\frac{\partial X}{\partial y}dy$. We describe a diffusion term, introducing $J$ as a diffusion flux, which is $(\alpha_c + D_c)\frac{\partial X}{\partial y}$:

$$
AJ\vert_{y} - AJ\vert_{y} 
$$
Again, we use Taylor series expansion and the above term is going to be $A\frac{\partial}{\partial y}((\alpha_c + D_c)\frac{\partial X}{\partial y})\partial y$. Summing the three terms, we finally get:

$$
Bdy(u_{in}X_{in}-u_{out}X) - VA\frac{\partial X}{\partial y}\partial y + A\frac{\partial}{\partial y}((\alpha_c + D_c)\frac{\partial X}{\partial y})\partial y
$$

Divided by $A\partial y$, the above equation can be rearranged as:

$$
F(X) = \frac{B}{A}(u_{in}X_{in}-u_{out}X)-\frac{1}{A}\frac{\partial VAX}{\partial y} +\frac{(\alpha_c + D_c)}{A}\frac{\partial}{\partial y}(A\frac{\partial X}{\partial y})
$$


| Symbol | Parameter | Input values | Unit | 
|----------------|-------------|-------------|-------------|
| $A$     |   Upper surface area of each layer   | Depending on the depth    | $m^{2}$ |
|  $B$     |   Width of each layer   | $800.0$    | $m$ |
| $X_{in}$     |  Influent concentration   | From monitoring data    | $mg/L$ | 
| $u_{in}$     |  Inlet velocity   | Calculated from monitoring data     | $m/d$ |
| $u_{out}$     |   Outlet velocity   | Calculated from monitoring data    | $m/d$ |
| $V$     |   Vertical velocity   | Calculated from monitoring data    | $m/d$ |
| $\alpha_c$     |  Eddy diffusion coefficient   | $9 \times 10^{-4}$    | $m^2/d$ |
| $D_c$     |   Molecular diffusion coefficient   | $9 \times 10^{-5}$     | $m^2/d$ |


Vertical velocities are calculated by:

$$
V_{j,j+1} = \frac{A_{j-1,j}V_{j-1,j} - B_j\Delta y (u_{i,j} - u_{o,j})}{A_{j,j+1}}
$$

Horizontal velocities are estimated by:

$$
u_{i,j} = Q_i U (\frac{y-y_{in}}{\delta_{in}})
$$

$$
u_{o,j} = Q_o U (\frac{y-y_{out}}{\delta_{out}})
$$

where, $Q_i$ and $Q_o$ are flow rates of inflow and outflow to the reservoir, respectively. $y$, $y_{in}$, and $y_{out}$ are vertical positions relative to the reservoir bottom for a specific layer, inflow and outflow, respectively. $\delta_{in}$ and $\delta_{out}$ are intrusion and withdrawal layer thickness. A function, $U(\psi)$, is given as the following depending on $\xi$. When $\xi$ is smaller than $0.5$, $U(\psi)$ is given by:

$$
U(\xi) = \frac{e^{-\frac{1}{2}(\frac{\xi}{3.92})^2}}{\int_{-\frac{1}{2}}^{\frac{1}{2}} {B\left\lbrace y(\xi_1)\right\rbrace }e^{-\frac{1}{2}(\frac{\xi}{3.92})^2}d\xi_1}
$$

Otherwise, 

$$
U(\xi) = 0
$$

$\delta_{in}$ and $\delta_{out}$ are estimated by:

$$
\delta_{in} = F_i^{-\frac{1}{2}}(\frac{Q_{in}}{B_i \sqrt{\varepsilon_i g}})^{\frac{1}{2}}
$$

$$
\delta_{out} = G^{-\frac{1}{3}}(\frac{Q_{out}}{\theta \sqrt{\varepsilon_o g}})^{\frac{1}{3}}
$$

where, $F$ and $G$ are internal Froude numbers and empirically set at $0.25$ and $0.134$. $\varepsilon$ is a density gradient and can be calculated by $-\frac{1}{\rho}\frac{d\rho}{dy}$. $\theta$ is an angle of the outflow mouth.

Now, let's consider a term regarding bio-chemical reactions. Growth and endogenous respiration rates of algae as well as decomposition rates of TOC and oxygen consumption rates in the sediment are listed below.

$$
G_{\gamma} = G_{\gamma max}\theta^{T-20}\frac{TP}{K_P +TP}\left\lbrace \frac{\phi}{I_s}exp(1-\frac{\phi}{I_s})\right\rbrace
$$

$$
R_{\gamma} = R_{\gamma max}\theta^{T-20}
$$

$$
K_{c} = K_{c20}\theta^{T-20}
$$

$$
K_{b} = K_{b20}\theta^{T-20}
$$

| Symbol | Parameter | Input values | Unit | 
|----------------|-------------|-------------|-------------|
| $G_{\gamma max}$     |  Maximum specific growth rate of algae at 20$^\circ C$   | 2.0    | $1/d$ |
| $R_{\gamma max}$     |  Endogenous respiration rate of algae at 20$^\circ C$   | 0.09    | $1/d$ |
| $K_{C20}$     |   Decomposition rate constant of TOC at 20$^\circ C$   | 0.01    | $1/d$ |
| $\theta$     |  Temperature coefficient   | 1.07     | - |
| $K_{P}$     |   Half saturation coefficient of algae   | 26.0    | $mg\:P/m^{3}$ |
| $I_{S}$     |   Optimum light intensity   | 300.0    | $kcal/m^{2}/d$ |
| $\beta$     | Adsorption coefficient at water surface      | 0.5     | - |
| $\alpha_{r}$     | Reflection coefficient at water surface   | $0.06$    | - | 
| $\phi_{n}$     | Light intensity    | From monitoring data     | $kcal/m^2/d$ | 
| $\eta$     | Light absorption coefficient      | $0.4$  | $1/m$ | 
| $y_{0}$     | Elevation from the lake bottom  | $80$ | $m$ |


Mass balance equations for Chl.a and SS

$$
\frac{\partial Chl.a}{\partial t} = (G_\gamma - R_\gamma)Chl.a + W_0 \frac{\partial Chl.a}{\partial y} + F(Chl.a) 
$$

$$
\frac{\partial SS}{\partial t} = W_0 \frac{\partial SS}{\partial y} + F(SS) 
$$

Derivation of the sedimentation term can be done as the following with Taylor series expansion of $X\vert_{y+dy}$, which is $X\vert_{y} + \frac{\partial X}{\partial y}dy$:

$$
\frac{1}{A\partial y}((-W_0)AX\vert_{y} - (-W_0)AX\vert_{y+dy})) = \frac{1}{A \partial y} W_0 A\frac{\partial X}{\partial y}\partial y
$$

| Symbol | Parameter | Input values | Unit |
|----------------|-------------|-------------|-------------|
| $W_0$     | Sedimentation rate     | 0.4     | $m/d$ |
| $\sigma_{C\gamma}$     | Ratio of biological release of TOC from algae     | $4.8 \times 10^{-2}$    | $mg\:TOC/mg\:Chl.a$ | 
| $\sigma_{P\gamma}$     | Coefficient of biological utilization of TP from algae      | $1.25$    | $mg\:P/mg\:Chl.a$ | 
| $\sigma_{N\gamma}$     | Coefficient of biological utilization of TN from algae      | $17.0$    | $mg\:N/mg\:Chl.a$ | 
| $\beta_{\gamma}$     | Yield coefficient of TN and TP   | $0.6$    | - | 
| $\sigma_{O\gamma}$     | Coefficient of biological production of $O_2$ for algae   | $0.13$    | $mg\:O_2/mg\:Chl.a$ |
| $\sigma_{OC}$     | Oxygen demand exerted by TOC decomposition   | $2.7$     | $mg\:O_2/mg\:TOC$ |


Mass balance equations for TOC, Tp, TN and DO are given as follows:

$$
\frac{\partial TOC}{\partial t} = \sigma_{C\gamma}R_\gamma Chl.a - K_C TOC + F(TOC) 
$$

$$
\frac{\partial TP}{\partial t} = \sigma_{P\gamma}(-G_\gamma +\beta_\gamma R_\gamma)Chl.a + F(TP) 
$$

$$
\frac{\partial TN}{\partial t} = \sigma_{N\gamma}(-G_\gamma + \beta_\gamma R_\gamma)Chl.a + F(TN)
$$

$$
\frac{\partial DO}{\partial t} = \sigma_{O\gamma}(G_\gamma - R_\gamma)Chl.a - \sigma_{OC} K_C TOC - \frac{K_b}{A}\frac{\partial A}{\partial y} + F(DO)
$$

Derivation of the third term in the right-hand side of the DO mass balance equation can be done as the following with Taylor series expansion of $A\vert_{y+dy}$, which is $A\vert_{y} + \frac{\partial A}{\partial y}dy$:

$$
-\frac{1}{A\partial y}K_b(A\vert_{y+dy} - A\vert_{y}) = -\frac{K_b}{A\partial y}\frac{\partial A}{\partial y}\partial y
$$

As we saw in the Streeter-Phelps model, a flux of oxygen dissolution from the atmosphere is described as:

$$
 \begin{split}
    J& = -D_m\frac{C_{DO}}{dz} \\
    & = D_m\frac{C_{DO}^* - C_{DO}}{\delta} \\
    & = \frac{D_m}{\delta}(C_{DO}^* - C_{DO})
 \end{split}
$$

In case that a wind velocity, $V_w$, is larger and equal to $V_{w0}$, which is a reference wind velocity: 

$$
\frac{\delta}{\delta_0} = (\frac{V_w}{V_w0})^{-2} 
$$

Otherwise,

$$
\frac{\delta}{\delta_0} = 1 
$$

where, $\delta_0 = 440\:\mu m $ and $V_{w0} = 2\:m/s$.

Energy balance equation is given:

$$
\frac{\partial T}{\partial t} = \frac{B}{A}(u_{in}T_{in}-u_{out}T)-\frac{1}{A}\frac{\partial VAX}{\partial y} + \frac{(\alpha_T + D_T)}{A}\frac{\partial}{\partial y}(A\frac{\partial T}{\partial y}) - \frac{1}{\rho AC}\frac{\partial (A\phi)}{\partial y}
$$

| Symbol | Parameter | Input values | Unit |
|----------------|-------------|-------------|-------------|
|  $T_{in}$     |  Influent temperature   | From monitoring data    | $mg/L$ |
|  $\alpha_{T}$     |  Thermal eddy diffusion coefficient   | $9 \times 10^{-4}$    | $m^2/d$ |
| $D_{T}$     |   Heat diffusion coefficient  | $9 \times 10^{-5}$     | $m^2/d$ |
| $\rho$     |  Water density  | $1.0$     | $t/m^3$ |
| $C$     |   Specific heat  | $1.0$     | $kcal/kg/^\circ C $ |

Derivation of the last term can be done as the following with Taylor series expansion of $(A\phi)\vert_{y+dy}$, which is $(A\phi)\vert_{y} + \frac{\partial (A\phi)}{\partial y}\partial y$:
Let's look at how to derive the last term:

$$
\frac{1}{A\partial y}\frac{1}{\rho C}((A\phi)\vert_y - (A\phi)\vert_{y+dy})) = \frac{1}{\rho AC}\frac{\partial (A\phi)}{\partial y}
$$

## Implementation
Explicit time advance is used as a numerical scheme. Time-step and space step are set at $0.2\:d$ and $2\:m$, respectively. The followings are inputs:

- Irradiation($\phi_n$)
- Wind velocity ($V_w$)
- Influent and effluent flow rates ($Q_i$ and $Q_o$)
- Water temperature ($T_{in}$) and quality ($X_{in}$) of influent flow 

```Fortran
! Monitoring data - daily average
 if (mod(j, count_day) == 0) then
   day = day + 1
    
   read (99,*) MonitoringDataDaily(day, 1), MonitoringDataDaily(day, 2), MonitoringDataDaily(day, 3), MonitoringDataDaily(day, 4), MonitoringDataDaily(day, 5)
    
   Q_in = MonitoringDataDaily(day,1)              ! Inlet flow rate [m^3/d]
   Q_out = MonitoringDataDaily(day,2)             ! Outlet flow rate [m^3/d]         
   phi_n = MonitoringDataDaily(day,3)             ! Light intensity [kcal/m^3/d]
   Vw = MonitoringDataDaily(day,4)                ! Wind velocity [m/s]
   month_c = MonitoringDataDaily(day, 5)
    
   ! Monitoring data for inlet - montyly average
   if (month == int(month_c)) then
     read (98,*) MonitoringDataMonth(month, 1),  MonitoringDataMonth(month, 2), MonitoringDataMonth(month, 3), MonitoringDataMonth(month, 4), MonitoringDataMonth(month, 5), MonitoringDataMonth(month, 6), MonitoringDataMonth(month, 7)
    
     T_in = MonitoringDataMonth(month, 1)             ! Inlet water temperature 
     Chla_in = MonitoringDataMonth(month, 2)          ! Inlet chlorophyl concentration
     TOC_in = MonitoringDataMonth(month, 3)           ! Inlet TOC concentration
     TP_in = MonitoringDataMonth(month, 4)            ! Inlet TP concentration
     TN_in = MonitoringDataMonth(month, 5)            ! Inlet TN concentration
     DO_in = MonitoringDataMonth(month, 6)            ! Inlet DO concentration
     SS_in = MonitoringDataMonth(month, 7)            ! Inlet SS concentration
  else     
    month = month + 1
    
    read (98,*) MonitoringDataMonth(month, 1), MonitoringDataMonth(month, 2), MonitoringDataMonth(month, 3), MonitoringDataMonth(month, 4), MonitoringDataMonth(month, 5), MonitoringDataMonth(month, 6), MonitoringDataMonth(month, 7)
    
    T_in = MonitoringDataMonth(month, 1)          
    Chla_in = MonitoringDataMonth(month, 2)         
    TOC_in = MonitoringDataMonth(month, 3)           
    TP_in = MonitoringDataMonth(month, 4)            
    TN_in = MonitoringDataMonth(month, 5)            
    DO_in = MonitoringDataMonth(month, 6)            
    SS_in = MonitoringDataMonth(month, 7)            
    
  end if
 end if

```

```Fortran
cDO(i, j+1) = cDO(i, j) + dt*gamma_oy*(Gy - Ry)*cChla(i,j) - dt*gamma_oc*kc*cTOC(i, j) - dt*kb/A*dAdy + dt*B/A*(u_in*DO_in - u_out*cDO(i, j)) - V*dt/dz/2*(cDO(i+1, j)- cDO(i-1, j)) + (alpha + diff)*dt/dz**2*(cDO(i+1, j) - 2*cDO(i,j) + cDO(i-1, j))
    
 if (cDO(i, j+1) < 0 ) then
    cDO(i, j+1) = 0
 endif
    
 cDO(N, j+1)= cDO(N-1, j+1) + dt*aeration(Vw, cDO(N,j), dz, t(N,j))
    
```

```Fortran
function aeration (Vw, cDO, dz, wt) result(retval)
  real(real64), intent (in) :: Vw, cDO, dz, wt
  real(real64) :: Vw0 = 2.0, &                  
  Lx0 = 440.0, &                   
  Lx, DO_sat, x, mdo
    
  if (Vw >= Vw0) then
    Lx = Lx0*(Vw/Vw0)**(-2)
  else 
    Lx = Lx0
  end if 
    
  DO_sat = 14.652-0.41022*wt+0.007991*wt**2-0.000077774*wt**3
  x = 0.024716*(wt - 20.0)
    
  ! molecular diffusion coefficients - m2/s 
  mdo = 2*exp(x)/1000000000           
    
  retval = mdo/(Lx*dz)*(DO_sat - cDO)
    
 end function aeration
    
```