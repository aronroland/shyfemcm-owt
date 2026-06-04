#------------------------------------------------------------------------
#
#    Copyright (C) 1985-2020  Georg Umgiesser
#    This file is part of SHYFEM.
#
#    Rules.make.aron — cleaned/reorganized drop-in (A. Roland).
#    Functionally identical to the upstream Rules.make: same effective
#    flags, toggles and compatibility checks. Only dead reassignments,
#    unused variables (ARON_*, GGU_INIT, TRAP_LIST, WNOINIT, ...) and
#    obsolete prose (lahey, ERSEM/AmgX notes) were removed and the file
#    reorganized. To use:  cp Rules.make.aron Rules.make
#
#------------------------------------------------------------------------

##############################################
## USER DEFINED PARAMETERS AND FLAGS
##############################################

# Compiler profile: NORMAL | CHECK | SPEED
COMPILER_PROFILE = SPEED

# Fortran compiler: GNU_G77 | GNU_GFORTRAN | INTEL | PORTLAND | IBM | PGI
FORTRAN_COMPILER = GNU_GFORTRAN
# C compiler: GNU_GCC | INTEL | IBM | PGI
C_COMPILER       = GNU_GCC
# Intel front-end (only for FORTRAN_COMPILER=INTEL): IFORT | IFX
INTEL_VERSION    = IFORT

# Parallelization (OMP and MPI are mutually exclusive)
# PARALLEL_MPI: NONE | NODE | ELEM(not ready)
PARALLEL_OMP = false
PARALLEL_MPI = NODE

# Domain decomposition library: NONE | METIS | PARMETIS (PARMETIS needed for WW3)
PARTS       = PARMETIS
METISDIR    = /home/aron/opt/parmetis_gfortran
PARMETISDIR = /home/aron/opt/parmetis_gfortran

# Matrix solver: GAUSS | SPARSKIT | PARDISO(Intel only) | PARALUTION | PETSC | PETSC_AmgX
SOLVER = SPARSKIT

# Solver extras (only used by the matching SOLVER/GPU choice)
PETSC_DIR          = ${PETSC_HOME}
AMGX_C_WRAPPER_DIR = ../amgx-c-wrapper/amgx-c-wrapper
AMGX_WRAPPER_DIR   =
AMGX_DIR           =
CUDA_DIR           =
PARADIR            =
# GPU: NONE | OpenCL | CUDA | MIC
GPU                = NONE

# NetCDF output
NETCDF     = true
NETCDFDIR  = /home/aron/opt/netcdf_gfortran
NETCDFFDIR = /home/aron/opt/netcdf_gfortran

# GOTM turbulence model (needed for 3D with variable viscosity/diffusivity)
GOTM = true

# Ecological / extra modules
# ECOLOGICAL: NONE | EUTRO | AQUABC | BFM
ECOLOGICAL = NONE
MERCURY    = false
BFMDIR     = $(BFM_HOME)
FLUID_MUD  = false

# WW3 wave model coupling (experimental; needs PARMETIS + NETCDF + MPI=NODE)
WW3       = true
WW3DIR    = /home/aron/git/ww3.erdc/WW3
WW3SWITCH = switch_itedev

# ESMF-NUOPC coupling
NUOPC   = false
ESMFDIR = ${ESMF_HOME}

##############################################
## END OF USER DEFINED PARAMETERS
## Normally nothing below here needs changing.
##############################################

##############################################
# Version and directories (FEMDIR set by calling Makefile)
##############################################

RULES_MAKE_VERSION = 1.11
DISTRIBUTION_TYPE  = experimental

DEFDIR = $(HOME)
LIBDIR = $(FEMDIR)/lib
BINDIR = $(FEMDIR)/bin
MODDIR = $(LIBDIR)/mod

LIBX = -L/usr/X11R6/lib -L/usr/X11/lib -L/usr/lib/X11 -lX11

##############################################
# Compatibility checks of the chosen options
##############################################

RULES_MAKE_PARAMETERS = RULES_MAKE_OK
RULES_MAKE_MESSAGE    = ""

ifeq ($(FORTRAN_COMPILER),GNU_G77)
  ifeq ($(GOTM),true)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "g77 compiler and GOTM=true are incompatible"
  endif
  ifeq ($(PARALLEL_OMP),true)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "g77 and PARALLEL_OMP=true are incompatible"
  endif
  ifneq ($(PARALLEL_MPI),NONE)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "g77 and PARALLEL_MPI/=NONE are incompatible"
  endif
endif

ifeq ($(SOLVER),)
  RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
  RULES_MAKE_MESSAGE = "No solver chosen. Please set SOLVER"
endif

ifneq ($(FORTRAN_COMPILER),INTEL)
  ifeq ($(SOLVER),PARDISO)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "Pardiso solver needs Intel compiler"
  endif
endif

ifneq ($(SOLVER),PARALUTION)
  ifneq ($(GPU),NONE)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "Use GPU=NONE without PARALUTION solver"
  endif
endif

ifeq ($(SOLVER),PARALUTION)
  ifneq ($(PARALLEL_OMP),true)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "Paralution solver needs PARALLEL_OMP=true"
  endif
  ifeq ($(PARADIR),)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "PARALUTION solver needs PARADIR directory"
  endif
endif

ifeq ($(SOLVER),PETSC)
  ifeq ($(PETSC_DIR),)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "PETSC_DIR directory is empty"
  endif
endif

ifeq ($(C_COMPILER),INTEL)
  ifneq ($(FORTRAN_COMPILER),INTEL)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "INTEL C works only with INTEL Fortran compiler"
  endif
endif

ifeq ($(ECOLOGICAL),BFM)
  ifeq ($(BFMDIR),)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "BFM model needs BFMDIR directory"
  endif
  ifeq ($(NETCDF),false)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "BFM model needs NETCDF support"
  endif
endif

ifeq ($(PARALLEL_MPI),NODE)
  ifeq ($(PARALLEL_OMP),true)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "OMP and MPI parallelization are incompatible"
  endif
  ifeq ($(PARTS),NONE)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "MPI parallelization PARTS = METIS"
  endif
endif

ifeq ($(PARALLEL_MPI),ELEM)
  RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
  RULES_MAKE_MESSAGE = "MPI on element partition is not yet ready"
endif

ifeq ($(WW3),true)
  ifneq ($(PARTS),PARMETIS)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "Please set PARTS = PARMETIS"
  endif
  ifneq ($(PARALLEL_MPI),NODE)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "PARALLEL_MPI must be set to NODE"
  endif
  ifneq ($(NETCDF),true)
    RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
    RULES_MAKE_MESSAGE = "WW3 model needs NETCDF support"
  endif
endif

##############################################
# Utility:  make print-VARIABLE  shows $(VARIABLE)
##############################################

print-% : ; @echo $* = $($*)

##############################################
# Compiler profile -> PROFILE / DEBUG / OPTIMIZE / WARNING / BOUNDS
##############################################

CPROF = false

ifeq ($(COMPILER_PROFILE),NORMAL)
  CPROF = true
  PROFILE = false
  DEBUG = true
  OPTIMIZE = MEDIUM
  WARNING = true
  BOUNDS = false
  XFLAG = -DSHYFEM_NORMAL
endif

ifeq ($(COMPILER_PROFILE),CHECK)
  CPROF = true
  PROFILE = true
  DEBUG = true
  OPTIMIZE = NONE
  WARNING = true
  BOUNDS = true
  XFLAG = -DSHYFEM_CHECK
endif

ifeq ($(COMPILER_PROFILE),SPEED)
  CPROF = true
  PROFILE = false
  DEBUG = false
  OPTIMIZE = HIGH
  WARNING = false
  BOUNDS = false
  XFLAG = -DSHYFEM_SPEED
endif

ifeq ($(CPROF),false)
  RULES_MAKE_PARAMETERS = RULES_MAKE_PARAMETER_ERROR
  RULES_MAKE_MESSAGE = "COMPILER_PROFILE must be one of NORMAL,CHECK,SPEED"
  $(warning COMPILER_PROFILE=$(COMPILER_PROFILE))
endif

##############################################
# Compiler major-version detection
##############################################

GMV       := $(shell $(BINDIR)/cmv.sh -quiet gfortran)
IMV       := $(shell $(BINDIR)/cmv.sh -quiet intel)
GMV_LE_4  := $(shell [ $(GMV) -le 4 ] && echo true || echo false )
GMV_LE_8  := $(shell [ $(GMV) -le 8 ] && echo true || echo false )
IMV_LE_14 := $(shell [ $(IMV) -le 14 ] && echo true || echo false )

##############################################
# GNU compiler (gfortran / g77)
##############################################
#
#   -Wall warnings   -O/-O3 optimize   -g debug   -p profile
#   -fdefault-real-8 double precision
#   stacksize segfaults:  ulimit -s unlimited
#
# Per-version idiosyncrasies:
#   gfortran <=4 : -Wtabs (not -Wno-tabs), no -fallow-argument-mismatch
#   -ffree-line-length-none required everywhere (WW3 long continuation lines)

WTABS = -Wno-tabs
FGNU_allow-argument-mismatch = -fallow-argument-mismatch
ifeq ($(GMV_LE_4),true)
  WTABS = -Wtabs
  FGNU_allow-argument-mismatch =
endif

FGNU_GENERAL = -cpp -std=f95
ifdef MODDIR
  FGNU_GENERAL = -cpp -J$(MODDIR)
endif
FGNU_GENERAL += $(XFLAG)

FGNU_PROFILE =
ifeq ($(PROFILE),true)
  FGNU_PROFILE = -p
endif

# Warning/diagnostic set (also carries -g/-fbacktrace for the NORMAL/CHECK
# profiles). To re-enable uninitialized-memory poisoning for debugging, append:
#   -finit-integer=98765432 -finit-real=snan -finit-logical=true
FGNU_WARNING =
ifeq ($(WARNING),true)
  FGNU_WARNING = -Wall $(WTABS) -Wno-conversion \
		-g -ggdb -ffree-line-length-none -fbacktrace -fno-realloc-lhs \
		-Werror=return-type -Wsurprising \
		-Werror=strict-aliasing -Werror=type-limits \
		-Wno-unused -Wno-unused-dummy-argument -Werror=unused-value
endif

FGNU_BOUNDS =
ifeq ($(BOUNDS),true)
  FGNU_BOUNDS = -fcheck=all
endif

FGNU_NOOPT =
ifeq ($(DEBUG),true)
  FGNU_NOOPT = -g -fbacktrace $(FGNU_BOUNDS)
endif

FGNU_OPT = -O -ffree-line-length-none
ifeq ($(OPTIMIZE),HIGH)
  FGNU_OPT = -O3 -ffree-line-length-none
endif
ifeq ($(OPTIMIZE),NONE)
  FGNU_OPT = -ffree-line-length-none
endif

FGNU_OMP =
ifeq ($(PARALLEL_OMP),true)
  FGNU_OMP = -fopenmp
endif

ifeq ($(FORTRAN_COMPILER),GNU_G77)
  FGNU         = g77
  FGNU95       = g95
  F77          = $(FGNU)
  F95          = $(FGNU95)
  LINKER       = $(F77)
  LFLAGS       = $(FGNU_OPT) $(FGNU_PROFILE) $(FGNU_OMP)
  FFLAGS       = $(LFLAGS) $(FGNU_NOOPT) $(FGNU_WARNING)
  FFLAG_SPECIAL = $(LFLAGS) $(FGNU_WARNING) $(FGNU_allow-argument-mismatch)
  FINFOFLAGS   = --version
  MAJOR        = $(GMV)
endif

ifeq ($(FORTRAN_COMPILER),GNU_GFORTRAN)
  FGNU         = gfortran
  FGNU95       = gfortran
  ifneq ($(PARALLEL_MPI),NONE)
    FGNU       = mpif90
    FGNU95     = mpif90
  endif
  F77          = $(FGNU)
  F95          = $(FGNU95)
  LINKER       = $(F77)
  LFLAGS       = $(FGNU_OPT) $(FGNU_PROFILE) $(FGNU_OMP)
  FFLAGS       = $(LFLAGS) $(FGNU_NOOPT) $(FGNU_WARNING) $(FGNU_GENERAL)
  FFLAG_SPECIAL = $(LFLAGS) $(FGNU_WARNING) $(FGNU_GENERAL) \
		$(FGNU_allow-argument-mismatch)
  FINFOFLAGS   = --version
  MAJOR        = $(GMV)
endif

##############################################
# PGI / NVIDIA compiler (nvfortran)
#   download: https://developer.nvidia.com/nvidia-hpc-sdk-download
##############################################

FPGI_GENERAL =
ifdef MODDIR
  FPGI_GENERAL = -module $(MODDIR)
endif
FPGI_GENERAL += $(XFLAG)

FPGI_OMP =
ifeq ($(PARALLEL_OMP),true)
  FPGI_OMP = -mp
endif

FPGI_BOUNDS =
ifeq ($(BOUNDS),true)
  FPGI_BOUNDS = -Mbounds -Mchkptr -Mchkstk
endif

FPGI_PROFILE =
ifeq ($(PROFILE),true)
  FPGI_PROFILE = -Mprof
endif

FPGI_NOOPT = -cpp
ifeq ($(DEBUG),true)
  FPGI_NOOPT = -g -traceback -Ktrap=fp -cpp
endif

FPGI_OPT = -O
ifeq ($(OPTIMIZE),HIGH)
  FPGI_OPT = -O3
endif
ifeq ($(OPTIMIZE),NONE)
  FPGI_OPT =
endif

FPGI_WARNING =

ifeq ($(FORTRAN_COMPILER),PGI)
  FPGI         = nvfortran
  F77          = $(FPGI)
  F95          = nvfortran
  LINKER       = $(FPGI)
  LFLAGS       = $(FPGI_OPT) $(FPGI_PROFILE) $(FPGI_OMP) $(FPGI_BOUNDS)
  FFLAGS       = $(LFLAGS) $(FPGI_NOOPT) $(FPGI_WARNING) $(FPGI_GENERAL)
  FFLAG_SPECIAL = $(FFLAGS)
  FINFOFLAGS   = --version
endif

##############################################
# IBM compiler (xlf)
#   xlf95 defaults to -qnosave ; xlf_r is thread safe
##############################################

FIBM_PROFILE =
FIBM_GENERAL += $(XFLAG)
FIBM_WARNING =
FIBM_NOOPT =

FIBM_OPT = -O
ifeq ($(OPTIMIZE),HIGH)
  FIBM_OPT = -O3
endif
ifeq ($(OPTIMIZE),NONE)
  FIBM_OPT =
endif

FIBM_OMP =
ifeq ($(PARALLEL_OMP),true)
  FIBM_OMP = -qsmp=omp -qnosave -q64 -qmaxmem=-1 -NS32648 -qextname -qsource -qcache=auto -qstrict -O3 -qarch=pwr6 -qtune=pwr6
endif

ifeq ($(FORTRAN_COMPILER),IBM)
  FIBM         = xlf_r
  F77          = $(FIBM)
  F95          = xlf_r
  LINKER       = $(FIBM)
  FFLAGS       = $(FIBM_OMP) $(FIBM_GENERAL)
  FFLAG_SPECIAL = $(FFLAGS)
  LFLAGS       = $(FIBM_OMP) -qmixed -b64 -bbigtoc -bnoquiet -lpmapi -lessl -lmass -lmassvp4
endif

##############################################
# Portland compiler (pgf90)
##############################################

FPG_PROFILE =
ifeq ($(PROFILE),true)
  FPG_PROFILE = -Mprof=func
endif
FPG_GENERAL += $(XFLAG)

FPG_WARNING =

FPG_NOOPT = -cpp
ifeq ($(DEBUG),true)
  FPG_NOOPT = -g -cpp
endif

FPG_OPT = -O
ifeq ($(OPTIMIZE),HIGH)
  FPG_OPT = -O3
endif
ifeq ($(OPTIMIZE),NONE)
  FPG_OPT =
endif

FPG_OMP =
ifeq ($(PARALLEL_OMP),true)
  FPG_OMP = -mp
endif

ifeq ($(FORTRAN_COMPILER),PORTLAND)
  FPG          = pgf90
  FPG95        = pgf90
  F77          = $(FPG)
  F95          = $(FPG95)
  LINKER       = $(F77)
  LFLAGS       = $(FPG_OPT) $(FPG_PROFILE) $(FPG_OMP)
  FFLAGS       = $(LFLAGS) $(FPG_NOOPT) $(FPG_WARNING) $(FPG_GENERAL)
  FFLAG_SPECIAL = $(FFLAGS)
  FINFOFLAGS   = -v
endif

##############################################
# INTEL compiler (ifort / ifx, mpiifort for MPI)
#   -check none|all|bounds|uninit|pointer
#   -r8 / -autodouble  double precision
#   stacksize segfaults:  export KMP_STACKSIZE=32M
##############################################

# ERSEM defines (used only by Intel + ECOLOGICAL=ERSEM)
REAL_4B  = real\(4\)
DEFINES += -DREAL_4B=$(REAL_4B)
DEFINES += -DFORTRAN95
DEFINES += -DPRODUCTION -static
FINTEL_ERSEM = $(DEFINES)

FINTEL_GENERAL = -fpp
ifdef MODDIR
  FINTEL_GENERAL = -fpp -module $(MODDIR) -diag-disable=10448
endif
FINTEL_GENERAL += $(XFLAG)

FINTEL_PROFILE =
ifeq ($(PROFILE),true)
  FINTEL_PROFILE = -p
endif

FINTEL_WARNING =
ifeq ($(WARNING),true)
  FINTEL_WARNING = -warn interfaces,nouncalled -gen-interfaces
endif

FINTEL_BOUNDS =
ifeq ($(BOUNDS),true)
  FINTEL_BOUNDS = -check uninit -check bounds -check pointer
endif

FINTEL_NOOPT =
ifeq ($(DEBUG),true)
  FINTEL_TRAP  = -debug all                 # WW3_ARON
  FINTEL_NOOPT = -g -traceback $(FINTEL_BOUNDS) $(FINTEL_TRAP)
endif

FINTEL_OPT = -O
ifeq ($(OPTIMIZE),HIGH)
  FINTEL_OPT = -O1 -assume byterecl -no-wrap-margin   # WW3_ARON
endif
ifeq ($(OPTIMIZE),NONE)
  FINTEL_OPT =
endif

FINTEL_OMP =
ifeq ($(PARALLEL_OMP),true)
  FINTEL_OMP = -qopenmp
  ifeq ($(IMV_LE_14),true)
    FINTEL_OMP = -openmp
  endif
endif

ifeq ($(FORTRAN_COMPILER),INTEL)
  FINTEL       = ifort
  ifneq ($(PARALLEL_MPI),NONE)
    FINTEL     = mpiifort
  endif
  ifeq ($(INTEL_VERSION),IFX)
    FINTEL     = ifx
    ifneq ($(PARALLEL_MPI),NONE)
      FINTEL   = mpiifort -fc=ifx
    endif
  endif
  F77          = $(FINTEL)
  F95          = $(F77)
  LINKER       = $(F77)
  LFLAGS       = $(FINTEL_OPT) $(FINTEL_PROFILE) $(FINTEL_OMP)
  FFLAGS       = $(LFLAGS) $(FINTEL_NOOPT) $(FINTEL_WARNING) $(FINTEL_GENERAL)
  FFLAG_SPECIAL = $(FINTEL_OMP) $(FINTEL_GENERAL)
  FINFOFLAGS   = -v
  MAJOR        = $(IMV)
endif

##############################################
# C compiler
##############################################

ifeq ($(C_COMPILER),GNU_GCC)
  CC      = gcc
  CFLAGS  = -O -Wall -pedantic -std=gnu99
  LCFLAGS = -O
  CINFOFLAGS = --version
endif

ifeq ($(C_COMPILER),PGI)
  CC      = nvc
  CFLAGS  = -O -Wall
  LCFLAGS = -O
  CINFOFLAGS = --version
endif

ifeq ($(C_COMPILER),INTEL)
  CC      = icc
  ifeq ($(INTEL_VERSION),IFX)
    CC    = icx
  endif
  CFLAGS  = -O -g -traceback
  LCFLAGS = -O
  CINFOFLAGS = -v
endif

ifeq ($(C_COMPILER),IBM)
  CC      = xlc
  CFLAGS  = -O
  LCFLAGS = -O
  CINFOFLAGS = -v
endif
