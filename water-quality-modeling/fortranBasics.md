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

# Fortran basics


In Fortran, you need to declare a type of variable. The types for number typically used are real (a number with a decimal point) and integer (a number without a decimal point). We want to set double precision for real to conduct more accurate calculations. We can use a module, iso_fortran_env and add (real64) to real as shown below.

```Fortran
use,intrinsic :: iso fortran env

real(real64)
```

We cover five important features of Fortran in this section, showing examples of how to use them.

## Input and output
Let's think of a simple input and output operation: After asked "Enter the number: " in the terminal, we want to type an arbitrary number (which must be integer for this case) and store that number to a variable named $\verb|num|$ and then to show that number in the terminal with a message of "number = ". We declare the type of $\verb|num|$ as integer in the first line.

```Fortran
 integer :: num 

 print*, 'Enter the number: '
 read*, num
 print*, 'number = ', num   
```

In the next example, we want to control a format of how numbers are displayed in the terminal. Instead of using $\verb|*|$ after $\verb|print|$, we can use $\verb|'(*F10.4))'|$. Here $\verb|F|$ indicates "floating number", $\verb|10|$ means that we secure $10$ digits including blanks, while $\verb|4|$ is to set a number of decimal places. By placing $\verb|*|$ in front of $\verb|F|$, we do not have to manually indicate the number of variables (i.e., three in this case). We need to put 'd0' after a number to 'activate' the double precision. If we forget to add it, the value remains single precision, even though we declare the type in the double precision. 

```Fortran
real(real64) :: a, b, c 

 a=1.9d0; b=2.4d0; c=3.3d0
 print '(*F10.4))' a, b, c

```
Let's slightly modify the previous code. We want to write numbers of a to c to a $\verb|csv|$ file. We need to use a $\verb|open|$ function, which requires two arguments $\verb|unit|$ and $\verb|file|$. The former is a number allocated to a different file to be differentiated from other files, while the latter is a file name ($\verb|results.csv|$ in this case). Next, we need to use a $\verb|write|$ function. Again, we will provide two arguments. The first is exactly same with the $\verb|open|$ function. We can omit $\verb|unit=|$. The second is a format as previously seen. Format is always defined with single quotes. $\verb|*|$ indicates repeat of a format, $\verb|(g0:, ",")|$ as long as data (a to c for this case) exist. Since we deal with a $\verb|csv|$ file, we provide $\verb|","|$ in the second position as a delimiter, while $\verb|g0:|$ in the first position indicates automatic adjustment of displaying and not giving a comma ($\verb|,|$) after the last number (i.e., $\verb|c|$).

```Fortran
real(real64) :: a, b, c 

 a=1.9d0; b=2.4d0; c=3.3d0
 open (unit=10, file='results.csv')
 write(10, '(*(g0:,","))') a, b, c
```

## If statement
We consider the case solving the quadratic equation: $ ax^2 + bx + c = 0 $. Here are the steps:

- Input $a$, $b$ and $c$ from terminal
- Calculate the discriminant: $b^2 - 4ac$
- Check if the discriminant is greater than or equal to $0$
    - If yes: calculate the roots: 
    $
      \frac{
        -b + \sqrt{b^2 - 4ac}
      }{
        2a
      }
    $
    
    - If no: print ‘No real roots’

```Fortran
 real(real64) :: discriminant, a, b, c, root1, root2

 discriminant = b**2 - 4*a*c

 if (discriminant >= 0) then
  root1 = (-b+sqrt(discriminant))/(2.*a)
  root2 = (-b-sqrt(discriminant))/(2.*a)
  print*, 'root 1: ', root1, 'root 2: ', root2
 else
  print*, 'no real roots'
 end if
```
The following is the list of symbols used for rational expressions.

- < : Is less than
- $>$ : Is greater than
- == : Is equal to
- <= : Less than or equal to
- $>=$ : Greater than or equal to
- /= : Not equal to

When we want to combine multiple conditions, we can use $\verb|.AND.|$ and $\verb|.OR.|$.

You can add a user snippets for $\verb|if statement|$. Now you just type $\verb|if|$ and Visual Studio Code automatically provides you with a block of code for $\verb|if statement|$.

## Loop
We calculate the sum of integer from $1$ to $10$, using $\verb|do loop|$.


- Define the two variables: $i$ and $sum$
- Set the initial value of $sum$ at $0$
- Use the \verb|do loop|
  - Inside the loop, update the value of sum and increment $i$ by $1$
- Print the result to terminal
  

You can add a user snippets for $\verb|do loop|$. Now you just type $\verb|do loop|$ and Visual Studio Code automatically provides you with a block of code for $\verb|do loop|$.

## Function/Subroutine
$\verb|Function|$ is used for breaking large codes down into procedures that can be re-used. We calculate saturated DO concentrations with the following empirical equation from 5 to 30 $^\circ C$ (by 5$^\circ C$):
$
 C_{DO} = 14.652 - 0.41022\times T + 0.007991\times T^2 - 0.000077774\times T^3
$

Here are the tasks we want to accomplish for this example:
- Write the function named $\verb|calculate_DO|$
- Print results to a $\verb|csv|$ file with the formatting of $\verb|F6.1|$

We provide a function below a keyword, $\verb|contains|$. $\verb|calculate_DO|$ is a name of the function and $\verb|DO_conc|$ is a returned value. $\verb|temperature|$ is an input for this function and we need to add $\verb|intent(in)|$. In the main code, we use $\verb|do loop|$, where increment is 5 as indicated in the problem statement. Unless increment is 1, we need to explicitly state the number for increment. One more thing important to be mentioned here is that we need to convert the index, $\verb|i|$ defined as integer to a real number, because the $\verb|calculate_DO|$ funciton accepts a real number.

```Fortran
 real(real64) DO_conc, temperature 
 integer :: i
 
 open(unit=10, file='saturated_DO.csv')

 do i = 5, 30, 5
  temperature = real(i)
  DO_conc = calculate_DO(temperature)
  write(10, '(I6, ',', F6.1)') i, DO_conc
 end do

 contains
 function calculate_DO (temperature) result(DO_conc)
 real(real64), intent (in) :: temperature
 real(real64) DO_conc
 
  DO_conc = 14.652 - 0.41022*temperature + 0.007991*temperature**2 - 0.000077774*temperature**3
 
 end function calculate_DO
```

Subroutine is very similar to function, but there are differences between them and here are the characteristics of subroutine:
- More than 1 value can be returned through arguments.
- Subroutine is referenced through a $\verb|CALL|$ statement.

## Array
We consider a case in which temperature distribution in a plate is calculated. The temperature at the position of $(i,j)$ at the time, $k+1$, is calculated by averaging those of the surrounding four points at the time, $k$:

$$
\phi^{k+1}_{i,j} = \frac{1}{4}(\phi^{k}_{i+1,j} + \phi^{k}_{i-1,j} + \phi^{k}_{i,j-1} + \phi^{k}_{i,j-1})
$$

The above equation is derived from the discritized form of a Laplace equation: $\frac{\partial^2 \phi}{\partial x^2} + \frac{\partial^2 \phi}{\partial y^2} = 0$. The second-order derivative can be described as follows:

$$
\frac{\partial^2 \phi}{\partial x^2} = \frac{\phi_{i+1,j}-2\phi_{i,j}+\phi_{i-1,j}}{\Delta x^2} + O(\Delta x^2)
$$

$$
\frac{\partial^2 \phi}{\partial y^2} = \frac{\phi_{i,j+1}-2\phi_{i,j}+\phi_{i,j-1}}{\Delta y^2} + O(\Delta y^2)
$$

We plug the first terms of the right-hand side to the Laplace equation.

$$
\frac{\phi_{i+1,j}-2\phi_{i,j}+\phi_{i-1,j}}{\Delta x^2} + \frac{\phi_{i,j+1}-2\phi_{i,j}+\phi_{i,j-1}}{\Delta y^2} = 0 
$$

With an assumption of a uniform grid spacing in the $x$ and $y$ directions, we get the equation that appears in the top. We create an array for storing temperature data. Since we consider a two-dimensional array, we need to set the size of both $x$ and $y$ axes: $\verb|t(0:NX+1, 0:NY+1)|$

We then need to set initial and boundary conditions. In all positions except for boundaries, temperatures are  initially set at $0$, while those on boundaries (i.e., $x$ and $y$ axes) are fixed at $100$ during the simulation.

```Fortran
 integer, parameter :: NX=3, NY=3
 real(real64) t (0:NX+1, 0:NY+1)

 t = 0.0d0
 t(:, 0) = 100d0; t(0, :) = 100d0
```

In this case, we create a subroutine in which calculations of temperature are conducted. There are four arguments consisting of three inputs (i.e., $\verb|n1|$, $\verb|n2|$, $\verb|max_iter|$) and one input-output (i.e., $\verb|t|$)

```Fortran
 contains
 subroutine tempcal(n1, n2, max_iter, t)

 integer, intent(in) :: n1, n2, max_iter
 real(real64), intent(inout) :: t(0:n1+1, 0:n2+1)

   do iter = 1, max_iter
    do i = 1, n1
     do j = 1, n2
      t(i,j) = 0.25*(t(i+1, j) + t(i-1, j) + t(i, j+1) + t(i, j-1))
     end do
    end do
  end do

 end subroutine tempcal
```

Now. let's return to the main program. We use $\verb|call|$ to reference the subroutine defined above. We need to give four arguments (i.e., $\verb|NX|$, $\verb|NY|$, $\verb|max_iter|$, $\verb|t|$) to this subroutine

```Fortran
 integer, parameter :: NX=3, NY=3
 integer :: max_iter=1000
 real(real64) :: t (0:NX+1, 0:NY+1) 

 t = 0.0d0
 t(:, 0) = 100d0; t(0, :) = 100d0

 call tempcal(NX, NY, max_iter, t)

```

Finally, we write results into a csv file. Just for the data appearance consistent with a $x$-$y$ coordinate graph, the index, $j$ starts from $\verb|NY+1|$ down to $\verb|0|$. In each $y$ axes, we want to display five data: $\verb|(t(i, j), i=0, NX+1)|$. 

```Fortran
 open(unit=10, file='result.csv')

 do j = NY+1, 0, -1
  write(10, '(*(g0:,","))')(t(i, j), i=0, NX+1)
 end do
```

We further modify the code to get a series of data capturing temperature distribution in every iteration cycle. Since we want to produce a separate csv file, we add an iteration cycle after the extension $\verb|csv|$. A symbol, $\verb|//|$, represents concatenation of $\verb|results.csv.|$ and $\verb|iter|$. Here, we need to somehow convert an index used in $\verb|do loop|$ (which is defined as integer) to a character. Fortran does not have a straightforward way for this conversion: we use a kind of trick, using a $\verb|write|$ function. In the line of code, $\verb|write(iter, '(I4)') i_iter|$, we inject a value of $\verb|i_iter|$ to $\verb|iter|$, of which type is **character**. A built-in function, $\verb|adjustl|$, "will left adjust a string by removing leading spaces. Spaces are inserted at the end of the string as needed" according to the GNU compiler official documentation (GNU is an operating system advocating for free and open software. GNU is also referred to as a collection of programs including the Fortran compiler). You can find information on which built-in functions are available as well as how those functions can be used at: https://gcc.gnu.org/onlinedocs/gfortran/Keyword-Index.html#Keyword-Index

```Fortran
 do i_iter = 1, 100
   write(iter, '(I4)') i_iter
   iter = adjustl(iter)
   open(unit=10, file='result.csv.' //iter)
   call tempcal(NX, NY, i_iter, t)
  
   write(10,*) 'x, y, z, t'
  
   do i = 0, NX+1
   xx = dble(i)
   do j = 0, NY+1
   yy = dble(j)
   write(10, '(*(g0:,","))') xx, yy, 0.0, t(i, j)
   end do; end do
 end do
```
As we completed implementation of the basic functionality, we introduce a new feature of timing the code below. This feature is used to analyze a part which is time-consuming for further improvement of code. We use a $\verb|system_clock|$ module twice, sandwiching the subroutine, which is a part of interest in this case. For the first call, two arguments (i.e., $\verb|count_start|$ and $\verb|count_rate|$) are given, while one argument (i.e., $\verb|count_end|$) is given for the second call. All of three arguments are integer and are used for calculation of $\verb|calc_time|$, which are displayed in the terminal eventually.

```Fortran
Integer(int32) :: count_start, count_end, count_rate
 real (real64) :: calc_time

 call system_clock(count_start, count_rate)

 call tempcal(nx, ny, max_iter, t)

 call system_clock(count_end)
 calc_time = real(count_end-count_start)/real(count_rate)
 print*, 'Run time = ', calc_time
```

One more revise. The array size can be flexible with addition of a keyword $\verb|allocatable|$ in the declaration. Then, we do not provide specific numbers; instead, $\verb|:|$ is used. Once the size of the array is fixed, we can use $\verb|allocate()|$. Inside the parentheses, we provide $\verb|t(0:nx+1, 0:ny+1)|$. 

```Fortran
 integer :: nx, ny
 real(real64), allocatable :: t (:,:)

 nx = 3; ny = 3
 allocate(t(0:nx+1, 0:ny+1))

 t=0d0; t(:, 0) = 100d0; t(0, :) = 100d0

 deallocate(t)
```