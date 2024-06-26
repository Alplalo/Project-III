
# Fortran compiler
FC=mpif90

# Number of processors
N=12


# Code location
VPATH=src/

#COMP_D_FLAGS=-Wall -Wextra -g -fcheck=all -Wall -Wextra -pedantic -std=f2008 -fbacktrace -ffpe-trap=zero,overflow,underflow -finit-real=nan -finit-integer=-9999
COMP_P_FLAGS=-O2 #-pg -fallow-argument-mismatch

######################################################################
##               MOLECULAR DYNAMICS /    HELP                       ##
######################################################################
##                                                                   #
## - Contributors:                                                   #
##   - @anna-mr98 (Anna Monclús)                                     #
##   - @ainagaya (Aina Gaya)                                         #
##   - @Alplalo (Albert Plazas)                                      #
##   - @gluoon8 (Manel Serrano)                                      #
##                                                                   #
##                                                                   #
##    !IMPORTANT: Remember   to   choose    your                     #
##                parameters in namMD.nml file!!                     #
##                                                                   #
##  - All the members have contributed to the creation of Makefile.  #
##       -Documentation by Manel Serrano                             #
##                                                                   #
##-------------------------------------------------------------------#
##                                                                   #
## - Commands:                                                       #
##                                                                   #
.PHONY. : help
help:
	@echo " "
	@grep "^##" Makefile

# ~ RUNNING THE PROGRAM ~
##  · make multirun: Compiles needed files and                       # 
##                   runs the program with MPI.                      #
##                                                                   #
##          DON'T USE IT if you want to send it                      #
##                                 to a cluster.                     # 
##                                                                   #
.PHONY.: multirun
run: MD.exe
	mpirun -n ${N} ./MD.exe

# ~ SUBMITTING TO QUEUE ~
##  · make qsub: Submits the job to the queue (iqtc07)               #
##                                                                   #

.PHONY.: qsub
qsub:
	@qsub -q iqtc07.q openmpi.sub
	@echo "Enviado a cola iqtc07"

# ~ PLOTS ~
##  · make plot: Plots the output data and perform statistics:       #
##                                                                   #
##     Plots                                                         #
##      - Epot, Ekin, Etot vs time                                   #
##      - Momentum vs time                                           #
##      - T vs time                                                  #
##      - Pressure vs time                                           #
##      - Radial distribution function                               #
##                                                                   #
##     Statistics                                                    #
##      - Mean values of Epot, Ekin, Etot, momentum, T, P            #
##             with standard deviations (Block-average method)       #
##                                                                   #

.PHONY.: plot
plot:
	@echo "Plotting files... (this may take a while)"
	@python3 ./src/plots/plots.py
	@cp ./src/plots/*.png ./results/
	@echo "Done! Check the plots in the main directory."
	@echo "Calculating statistics..."
	@echo " "
	@python3 ./src/postproc/stats.py
	@cp ./src/postproc/*.png ./src/postproc/*.dat ./results/
	@echo " "
	@echo "Done! Check the statistics in the results folder."



# ~ LINKING ~
##  · make all: Compiles the program and creates executable MD.exe   #
##                                                                   #
all: MD.exe
MD.exe:  pbc.o forces.o integrators.o  initialize.o  main.o
	$(FC) $(F_FLAGS) $(COMP_D_FLAGS) $(COMP_R_FLAGS) $^ -o $@


# ~ CLEANING ~
##  · make clean: Removes the modules, objects and executable        #
##                                                                   #
##  · make cleandata: Removes data files                             #
##                                                                   #
##  · make cleanplot: Removes plot files                             #
##                                                                   #
.PHONY.: clean
clean:
	@rm -rf *.o *.mod MD.exe hello.* HELLO
	@echo "Modules, objects and executable removed!" 

.PHONY.: cleandata
cleandata:
	@rm -f *.dat
	@echo "Data files removed!"

.PHONY.: cleanplot
cleanplot:
	@rm -f *.png
	@echo "Plot files removed!"



# ~ COMPILING ~

pbc.o: pbc/pbc.f90
	$(FC) -c $^

forces.o: forces/forces.f90
	$(FC) -c $^

integrators.o: integrators/integrators.f90
	$(FC) -c $^

initialize.o: initialization/initialize.f90
	$(FC) -c $^

main.o: main.f90
	$(FC) -c $^

##                                                                   #
##                                                                   #
######################################################################
