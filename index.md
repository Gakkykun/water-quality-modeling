# Welcome to Fortran programming

This manual is intended as an educational material, which provides you with a basic understanding of Fortran as well as the implementation of water quality model with Fortran. Fortran, which was developed in 1950s by IBM, is one of programming languages used for scientific computation. The basic work flow consists of the following three steps:

1. Coding with Fortran
2. Running the code
3. Data visualization

The core task of simulations is iterative calculation. Simulation results are stored in a csv file. In data visualization, we draw figures based on data contained in the csv file that is produced in the Step 2. A water quality model simulates the fate and transport process
of pollutants released into water bodies so that we can estimate the temporal evolution of the concentration of pollutants at different locations in the water body under different scenarios. The description of water quality variables is formulated as a set of ordinary coupled differential equations describing the rate of change for each state variable based on processes taking place in the water body. Numerically solving those differential equations can be done with iterative calculation. We first go through the installation process of software used and then learn five important Fortran features that you should master. After that, we will see several water quality models and how to implement them with Fortran. We acknowledge the two online resources, from which we have learned fundamentals and techniques regarding Fortran. The first is Professor Spall’s online courses available in
[Udemy](https://udemy.com) and the second is blog articles authored by implicit_none in [Qiita](https://qiita.com/implicit_none).
