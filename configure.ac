dnl Process this file with autoconf to produce a configure script
AC_INIT(DESCRIPTION)

dnl Determine R_HOME.
: ${R_HOME=`R RHOME`}
if test -z "${R_HOME}"; then
        echo "Could not determine R_HOME"
	exit 1
fi

R_SCMD="${R_HOME}/bin/Rscript -e"


CC=`${R_HOME}/bin/R CMD config CC`
CFLAGS=`${R_HOME}/bin/R CMD config CFLAGS`

# for ncdf4, I've chosen to REQUIRE the netcdf v4 interface
AC_CONFIG_MACRO_DIR([tools])
AC_PATH_NETCDF
if test $NETCDF_V4 = "no"; then
	echo "-------------------------------------------------------------------"
	echo "ncdf4 REQUIRES the netcdf library be version 4 or above,"
	echo "AND installed with HDF-5 support (i.e., the netcdf library must be"
	echo "compiled with the --enable-netcdf-4 flag). If you don't want to install"
	echo "the full version of netcdf-4 with HDF-5 support, then please install"
	echo "the old, deprecated ncdf package instead."
	echo "-------------------------------------------------------------------"
	exit -1
fi

HAVE_NETCDF=TRUE

NETCDF_LDFLAGS="$NETCDF_RPATH $NETCDF_LDFLAGS"


dnl pbdMPI
SPMD_CPPFLAGS=`${R_SCMD} "source('./R/get_conf.r');get.conf('PKG_CPPFLAGS','"${R_ARCH}"')"`
SPMD_LDFLAGS=`${R_SCMD} "source('./R/get_conf.r');get.conf('PKG_LIBS','"${R_ARCH}"')"`


dnl check parallel functions
NETCDF_HEADER="`$NC_CONFIG_FULLQUAL --prefix`/include/netcdf_par.h"
NETCDF_INCLUDE=0
if test -f "${NETCDF_HEADER}" ; then
  NETCDF_INCLUDE=1
fi

NETCDF_PAR=FALSE
AC_ARG_ENABLE(parallel, [  --enable-parallel       link with parallel NetCDF4 and HDF5], [
  NETCDF_PAR=TRUE
  ], [
  NETCDF_PAR=FALSE
])

dnl Take care parallel C code.
NETCDF_PAR_OBJS=""
if test "X$NETCDF_PAR" = "XTRUE" ; then
  NETCDF_PAR_OBJS="pncdf4.o"
fi

dnl Take care inst/R.par/* and inst/R.ser/*
if test "X$NETCDF_PAR" = "XTRUE" ; then
  rm -rf ./R/pncdf4*
  cp ./inst/R.par/pncdf4* ./R/
fi
if test "X$NETCDF_PAR" = "XFALSE" ; then
  rm -rf ./R/pncdf4*
  cp ./inst/R.ser/pncdf4* ./R/
fi


dnl substitue all configures.
AC_SUBST(SPMD_CPPFLAGS)
AC_SUBST(SPMD_LDFLAGS)
AC_SUBST(NETCDF_HEADER)
AC_SUBST(NETCDF_INCLUDE)
AC_SUBST(NETCDF_PAR)
AC_SUBST(NETCDF_PAR_OBJS)

AC_SUBST(NETCDF_CPPFLAGS)
AC_SUBST(NETCDF_LDFLAGS)
AC_SUBST(NETCDF_RPATH)
AC_SUBST(HAVE_NETCDF)


dnl output files.
AC_OUTPUT(src/Makevars)
AC_OUTPUT(R/zzz.R)


echo " "
echo "****************** Results of ncdf4 package configure *******************"
echo " "
echo "SPMD_CPPFLAGS   = $SPMD_CPPFLAGS"
echo "SPMD_LDFLAGS    = $SPMD_LDFLAGS"
echo "NETCDF_HEADER   = $NETCDF_HEADER"
echo "NETCDF_INCLUDE  = $NETCDF_INCLUDE"
echo "NETCDF_PAR      = $NETCDF_PAR"
echo "NETCDF_PAR_OBJS = $NETCDF_PAR_OBJS"
echo "netCDF v4 CPP flags 	= $NETCDF_CPPFLAGS"
echo "netCDF v4 LD flags  	= $NETCDF_LDFLAGS"
echo "netCDF v4 runtime path 	= $NETCDF_RPATH"
echo " "
echo "*************************************************************************"
echo " "

