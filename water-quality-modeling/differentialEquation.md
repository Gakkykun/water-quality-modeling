---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    # format_version: 0.13
    # jupytext_version: 1.14.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Differential Equation

## Euler's method
We numerically solve a simple differential equation using the Euler's method and compare results with analytical solutions. Let's briefly see how to derive the Euler's method from a Taylor-series expansion.

$$
y(x_0 + \Delta x) = y(x_0) + \frac{dy}{dx}\lvert_{x=x_0} \Delta x + \frac{d^2y}{dx^2}\lvert_{x=x_0} \frac{\Delta x^2}{2!} + \frac{d^3y}{dx^3}\lvert_{x=x_0} \frac{\Delta x^3}{3!} + ...
$$

We neglect the second and higher derivatives as those terms are very small when $\Delta x$ is very small:

$$
y(x_0 + \Delta x) = y(x_0) + \frac{dy}{dx}\lvert_{x=x_0} \Delta x 
$$

We solve the following differential equation, given that $y(0) = 1$, setting $\Delta t$ at $0.001$. This equation has an analytical solution: $y(t) = e^{-t}$

$$
\frac{dy}{dt} = -y
$$

```Fortran
dt = 0.001d0
 nsteps = ceiling(1.0/ dt)

 ! Initial conditions
 t(0) = 0.0d0
 y(0) = 1.0d0

 do i = 0, nsteps-1
  y(i+1) = y(i) + dt *f(y(i))
  t(i+1) = t(i) + dt
 end do
  


```

We define a function to calculate $\frac{dy}{dt} = -y$. 

```Fortran
 contains
 function f(y) result(retval)
 real(real64), intent(in) :: y
 real(real64) retval
 
   retval = -y
 
 end function f
```

Then we compare the simulation results with analytical solutions. Here we use built-in functions, $\verb|exp|$ and $\verb|abs|$.

```Fortran
  print*,'Numerical solution =', y(nsteps)
  print*,'This compares to the Fortran EXP(X) function result: ', exp(-1.0)
  print*,'The absolute error is:', abs(exp(-1.0)-y(nsteps))
```

## Modified Euler's method

Unlike the Euler's method, the Modified Euler's method requires two slopes to estimate $y_{n+1}$. Increasing the number of slopes used for estimation strengths accuracy. We simply add both slopes and divide the sum by 2. The first slope, $f(x_n,y_n)$, is exactly same with Euler's method, while for the second slope, $f(x_{n+1}, y_{n+1}^*)$, where $y_{n+1}^*$ is estimated from $y_n + \Delta x f_n(x,y)$

$$
\frac{dy}{dx} = f(x, y)
$$

$$
y_{n+1} = y_{n} + \frac{\Delta x}{2}(f(x_n, y_n) + f(x_{n+1}, y_{n+1}^*)
$$


Here is the next example. We solve this differential equation, given that $y(1) = 0$, setting $\Delta t$ at $0.1$. 

$$
\frac{dy}{dx} = y^2 + x^2
$$

```Fortran
 dx = 0.1d0
 nsteps = ceiling(1.0/dx)

 ! Initial conditions
 x(0)=1.0d0
 y(0)=0.0d0

 do i=0, nsteps-1
   k1=f(x(i),y(i))
   k2=f(x(i)+dx, y(i)+ dx *k1)
   y(i+1)=y(i)+ dx /2.*(k1+k2)
   x(i+1)=x(i)+ dx
 end do
```

Here is a function to calculate $\frac{dy}{dx} = y^2 + x^2$.

```Fortran
contains
 function f(x,y) result(retval)
 real(real64), intent(in) :: x, y
 real(real64) retval

   retval = x**2+y**2
   
 end function f

```

## The 4th-order Runge-Kutta method
For the 4th-order Runge-Kutta method, we use four slopes to estimate $y_{n+1}$, two of which are same with Modified Euler's method. We introduce $f(x_{n+1/2}, y_{n+1/2}^*)$, which corresponds to $k_2$ and $k_3$ and more weights than the others.

$$
y_{n+1} = y_{n} + \frac{\Delta x}{6}(f(x_n, y_n) + 4f(x_{n+1/2}, y_{n+1/2}^*) + f(x_{n+1}, y_{n+1}^*)
$$

$$
k_1 = f(x_n, y_n)
$$

$$
k_2 = f(x_n+\frac{\Delta x}{2}, y_n+\frac{\Delta x}{2}k_1)
$$

$$
k_3 = f(x_n+\frac{\Delta x}{2}, y_n+\frac{\Delta x}{2}k_2)
$$

$$
k_4 = f(x_n+\Delta x, y_n+\Delta x k_3)
$$

$$
y_{n+1} = y_{n} + \frac{\Delta x}{6}(k_1 + 2k_2 + 2k_3 + k_4)
$$

Now we deal with the same problem with the 4th-order Runge-Kutta method. We have four $k$s

```Fortran
 do i=0, nsteps-1
  k1=f(x(i),y(i))
  k2=f(x(i)+0.5*dx, y(i)+0.5*dx*k1)
  k3=f(x(i)+0.5*dx, y(i)+0.5*dx*k2)
  k4=f(x(i)+dx, y(i)+dx*k3)

  y(i+1)=y(i)+dx/6.0*(k1+2.0*k2+2.0*k3+k4)
  x(i+1)=x(i)+dx
 end do
```