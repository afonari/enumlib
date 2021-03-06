# Set up conditional compilation so that one Makefile can handle
# multiple platforms/compilers. Branch according to an environmental
# variable F90. I wish someone would show me a better way of doing this.
#

# Shell
SHELL		= /bin/bash

LBDR1 = ../../../celib/trunk
LBDR2 = ..# enumlib
FOUND = false
ifeq (${F90},gfortran)  # gfortran compiler
  ifeq (${DEBUG},false)
     FFLAGS = -O3 -ffree-line-length-none -fno-underscoring -I${LBDR1}  -I${LBDR2}
     FOUND = true
  else
     FFLAGS = -fPIC -g -fbounds-check -Wall -ffree-line-length-none -fno-underscoring -I${LBDR1} -I${LBDR2}
     FOUND = true
  endif
endif

ifeq (${F90},ifc)  # Intel compiler
  FFLAGS = -fPIC -g -error-limit 7 -traceback -check bounds -warn  -I${LBDR}
  FOUND = true
endif

ifeq (${F90},ifort)  # Intel compiler
  ifeq (${DEBUG},false)
     FFLAGS =  -fPIC -O3 -I${LBDR} 
     FOUND = true
  else
#  F90 =  /opt/intel/fc/10.0.016/bin/ifort
     FFLAGS =  -fPIC -g -debug -error-limit 7 -heap-arrays -traceback -check bounds -warn -I${LBDR} 
     FOUND = true
#-prof-use -prof-dir .
   endif
endif


ifeq (${F90},xlf90) # IBM compiler
  FFLAGS = -g -C -fPIC -qsuffix=f=f90  -I${LBDR}
  FOUND = true
endif
ifeq (${F90},f95) # Absoft PPC compiler
#  FFLAGS =  -profile -p ${LBDR} 
  FFLAGS = -g -Rb -Rc  -fPIC -nodefaultmod -p ${LBDR} #-ea
# B80  show entry in subprograms ; Rb bounds; Rc array conformance;
# z2 warning level
# -O3 optimization
# -ea stop after one error 
  FOUND = true
endif
ifeq (${F90},)  # If the variable isn't set, make sure compilation bombs
error:
	echo Error environmental variable "F90" not set!
endif
ifneq (${FOUND},true) # If it's set but no matching flags then bomb
error:	
	echo Error: makefile doesn\'t have flags for this compiler
endif



SRC =  derivative_structures.f90 driver.f90
#\
      #kgrid_utilities.f90 cwrapper.f90

OBJS = ${SRC:.f90=.o}
LIBS =  ${LBDR1}/libcomparestructs.a ${LBDR1}/libutils.a ${LBDR1}/libsym.a \
         ${LBDR1}/librational.a ${LBDR1}/libcombinatorics.a  ${LBDR2}/libenum.a

.SUFFIXES :  
.SUFFIXES :  .f .f90 .f95 .o


deriv.x: ${OBJS}
	 ${F90} ${LDFLAGS} -o $@ ${OBJS} ${LIBS}

compare_enum_files.x:  compare_two_enum_files.o
	${F90} ${LDFLAGS} -o $@ compare_two_enum_files.o libenum.a ${LIBS}

.f95.o : 
	${F90} ${FFLAGS} -c $<
.f90.o : 
#	bash -c "perl -pi -e 's/write\(21.*/write\(21,\*\) \"SVN revision number:'`svnversion`'\"/' derivative_structure_generator.f90 "
#	bash -c "echo `svnversion` > version.enum"
	${F90} ${FFLAGS} -c $<
.f.o : 
	${F90} -c $<



CLEAN  = *.o *.mod *.a *.x svn-commit.*
clean : 
	rm -f ${CLEAN}
clobber : 
	rm -f  ${CLEAN}  *~ \#*
	make
