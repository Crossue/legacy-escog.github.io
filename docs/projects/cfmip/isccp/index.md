<h1 class="title">ISCCP simulator README</h1>

<div id="cog_post_body">
    <div id="cog_post_body">
        <p>
	&nbsp;</p>
<pre>
Name:		ISCCP Simulator ICARUS/SCOPS
What:		Simulate ISCCP cloud products from GCM inputs
Version:	4.1
Authors:        Steve Klein <klein21@llnl.gov>
                Mark Webb <mark.webb@metoffice.gov.uk>

This README file is written by Mark, and so references to &#39;I&#39;
or &#39;me&#39; refer to Mark.

************************************************************************
This code is subject to copyright, according to a BSD licence

(c) 2009, Lawrence Livermore National Security Limited Liability
Corporation.  All rights reserved. ( icarus.f )

(c) British Crown Copyright 2009, the Met Office. All rights reserved.
(remaining code)


Redistribution and use in source and binary forms, with or without 
modification, are permitted provided that the
following conditions are met:

    * Redistributions of source code must retain the above 
      copyright  notice, this list of conditions and the following 
      disclaimer.
    * Redistributions in binary form must reproduce the above 
      copyright notice, this list of conditions and the following 
      disclaimer in the documentation and/or other materials 
      provided with the distribution.
    * Neither the names of the above organisations nor the names of 
      their contributors may be used to endorse or promote products
      derived from this software without specific prior written 
      permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS 
"AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT 
LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR 
A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT 
OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, 
SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT 
LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, 
DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY 
THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT 
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE 
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.  

**********************************************************************

0. Contents
-----------

0. Contents
1. About the code
2. Conditions of use
3. No warranty
4. Compilation and testing
5. Points to be aware of when using the code
   1) Are you running a correct version?
   2) Calling the code from within your model.
   3) Passing the cloud types in correctly.
   4) Setting NCOL. 
   5) Setting the seed correctly.
   6) Memory usage
   7) Check the results against your total cloud amount.
   8) Set top_height=1 for best comparisons with ISCCP IR-VIS.
   9) Handling sunlit points.
   A) Meaning of outputs from the ISCCP simulator. 
6. Revision history of released versions
7. Some other issues to consider

1. About the code
-----------------

This is a code that can be used to take cloud and atmosphere
information from atmospheric models and convert it into something that is
comparable to data from the ISCCP.  It has two parts.

SCOPS - Subgrid Cloud Overlap Profile Sampler.

This is the core of the code (written by Mark) which samples the subgrid
distibution of clouds within a model gridbox using a pseudo-random 
sampling process. It takes vertical profiles of convective and large 
scale cloud amount as input and applies a choice of cloud overlap 
assumptions to provide a number of cloud profiles sampled from random 
positions within the gridbox.

ICARUS - ISCCP Clouds and Radiances Using SCOPS.

This is the code (written by Steve) that emulates the ISCCP retrieval
using the profiles extracted from the GCM gridbox using SCOPS.  

For more information, see Klein and Jakob 1999; Webb et al. 2001.

2. Conditions of use
--------------------

Version 4.0 is released under a BSD licence, to promote open
use of the code.  Please refer to the copyright statements in the code.  

(c) 2009, Lawrence Livermore National Security Limited Liability
Corporation.  ( icarus.f )

(c) British Crown Copyright 2009, the Met Office. (other code)

Please email us to let us know if you are using the code so that
we can let you know about new releases.  Please also acknowledge
us in anything you write up, and cite:

Klein & Jakob (Monthly Weather Review 1999) and
Webb, Senior, Bony & Morcrette (Climate Dynamics 2001)

3. Other sources of information.
-------------------------------

Announcements regarding the code will be made on a mailing list - see
below for details on how to subscribe:

Two mailing lists are available for news, updates and comments
on the ISCCP Simulator software:

  isccp-simulator@metoffice.com 

(for technical announcements and queries about the simulator)

  isccp-simulator-projects@metoffice.com 

(for projects using the simulator - e.g. CFMIP)

To subscribe, send a message to majordomo@metoffice.com with the following 
message body:

     subscribe isccp-simulator your.email@address.com

The list is a closed one so only subscribers may post to the list. 
Subscription requests may take up to two working days to process.

See also www.cfmip.net

4. Compilation and testing
--------------------------

How to compile me:

	gunzip icarus-scops-4.0-bsd.tar.gz
	tar xvf icarus-scops-4.0-bsd.tar
	cd icarus-scops-4.0-bsd
        make clean
	make 

You may need to change the name of the compiler in the Makefile,
e.g.

	F77=f90 ( T3E )
	F77=g77 ( GNU FORTRAN compiler )

How to test me:

	make test

A successful test will look something like the following.

$ make test
test_isccp_cloud_types.ksh
make[1]: Entering directory `/home/hc0300/hadmw/icarus-scops-3.4'
make[1]: `test_isccp_cloud_types' is up to date.
make[1]: Leaving directory `/home/hc0300/hadmw/icarus-scops-3.4'
tests passed ok.

An unsuccessful test looks something like the following:

e.g.

t3ea> make test
        test_isccp_cloud_types.ksh
`test_isccp_cloud_types' is up to date.
 STOP (PE 0)   executed at line 92 in Fortran routine '$MAIN'
 STOP (PE 0)   executed at line 92 in Fortran routine '$MAIN'
 STOP (PE 0)   executed at line 92 in Fortran routine '$MAIN'
 STOP (PE 0)   executed at line 92 in Fortran routine '$MAIN'
 STOP (PE 0)   executed at line 92 in Fortran routine '$MAIN'
 STOP (PE 0)   executed at line 92 in Fortran routine '$MAIN'
4225c4225
<             0.17    0.30    0.00    0.00    0.15    0.00    0.00
---
>             0.18    0.30    0.00    0.00    0.15    0.00    0.00
4912c4912
<             0.30    0.17    0.00    0.00    0.15    0.00    0.00
---
>             0.30    0.18    0.00    0.00    0.15    0.00    0.00
there may be a problem with the test - files stdout and stdout.expected do not match.
Make: "test_isccp_cloud_types.ksh": Error code 1
cmd-2436 make: Stop.

Minor differences like those seen in this case can be caused by
rounding characteristics in formatting on different platforms.
I'd be interested to see any test output that has more serious
differences than these.

If you see something like this:

[mark@sagan icarus-scops-3.4]$ make test
./test_isccp_cloud_types.ksh
make[1]: Entering directory `/home/mark/icarus-scops-3.4'
f77 -c test_isccp_cloud_types.f
f77 -c isccp_cloud_types.f 
f77 -c ran0.f
f77 test_isccp_cloud_types.f isccp_cloud_types.o ran0.o  -o test_isccp_cloud_types
make[1]: Leaving directory `/home/mark/icarus-scops-3.4'
916c916
< meantaucld   =   0.00000
---
> meantaucld   =   3.25000
1942c1942
< meantaucld   =   0.00000
---
> meantaucld   =   1.67000
2968c2968
< meantaucld   =   0.00000
---
> meantaucld   =   1.89000
3704c3704
< meantaucld   =   0.00000
---
> meantaucld   =   3.25000
4440c4440
< meantaucld   =   0.00000
---
> meantaucld   =   1.67000
5176c5176
< meantaucld   =   0.00000
---
> meantaucld   =   1.89000

then you are most likely unable to read the unformatted files containing the 
optical thickness weights - try setting readbinary=.false. near line
24 of test_isccp_cloud_types.f

5. Points to be aware of when using the code
--------------------------------------------

1) Are you running a correct version?

There are various versions of the code around.  If you
didn't get the code directly from me, Steve, the GCSS 
DIME website or www.cfmip.net, what you have may not be what is 
described in the version history below.  Just because
it says it's version x does not mean that it is.
A good way to check is to take what you have, run the tests
supplied with the latest version and see if the results
are as expected. 

If you have a version that doesn't look like one of the ones below,
please send me a copy so that I can incorporate any improvements
into a future release.

In particular, if you are running with a version that says version 1.13 in 
isccp_cloudtypes.f, the results may be incorrect.

2) Calling the code from within your model.

Before the vector version of the code was available, people
looped over model grid points, calling the code column by column,
either on all model points, or just those with daylight.  Now
that the code accepts vector inputs, you have the choice of 
continuing to do this, or of passing full model fields into the
code.  The latter approach is likely to be more CPU efficient, 
particularly on vector processors, but will use more memory.
See the section on memory usage if you run out of memory.

Although the input arrays are mostly of the form (npoints,nlevs),
there is no problem passing in arrays of the form (nx,ny,nlevs)
if that is more convenient for you.  As long as npoints=nx*ny,
this should be fine, although it is worth switching 
the debug logical on occasionally to check that all of the
arguments are being passed correctly.

3) Passing the cloud types in correctly.

When running with convective cloud, cc represents the _total_
cloud amount, which includes the convective fraction
conv.   i.e. cc = conv + stratiform.  It is a common mistake
to assume that cc = stratiform and conv = convective.
The treatment of convective cloud assumes that you can maximally
overlap the convective cloud first and then overlap the 
stratiform cloud in the remaining cloud free space according
to the specified overlap type.  This is consistent with the 
overlap scheme in Edwards/Slingo (in the HadCM3) model
( convective cloud maximally overlapped within but may not
be true for schemes used for overlapping separate convective and
stratiform clouds in other models.

4)  Setting NCOL. 

The simulator uses a Monte Carlo method for sampling various columns
within each model gridbox.  The number of columns is set by the value
of NCOL.  The value that you want to set NCOL to depends on the
accuracy you want and amount of averaging you are doing on the outputs.

The recommended rule of thumb recommended by the authors is that you 
should aim for something like 2400 samples to keep statistical
noise to a reasonable level.  

For example, if you are doing no averaging, (i.e. you are
be calling the simulator once on instantaneous model variables
and looking directly at the results), you should set 
might expect that you need to set NCOL to something around 2400.

If you are looking at daily means, and are calculating this by
averaging 8 3-hourly calls to the simulator, NCOL should be set
to 300 ( = 2400/8 )

If, say, you are looking at monthly means, and are calling the
simulator, say, every 15 hours, NCOL should be set to 
50 =2400/(24*30/15)

If you are looking at monthly means, and are calculating this by
averaging 8 3-hourly calls per day to the simulator, NCOL should be set
to 10 ( = 2400/(8*30) )

*WARNING* Running with NCOL < 10 is not recommended even if you are
doing a lot of averaging on the results - I have experienced
systematic biases when doing this myself, although this might
not be a problem if the random number generator is seeded
properly.

Finally, don't forget to set NCOLMAX to be at least as big
as NCOL, ( but not bigger than necessary as the amount
of memory used is proportional to NCOLMAX.)  ( ** Note that
ncolmax is not required for versions 3.2 onwards. )

5) Setting the seed correctly.

It is essential that the seed for the random number generator
is set to a different value for each model gridbox it is called on,
as it is possible that the choice of the same seed value every time
may introduce some statistical bias in the results, particularly
for low values of NCOL.

In the simulations in Webb et al 2001, this was done by setting
seed=(pfull(nlev)-int(pfull(nlev)))*100+1, although this
may or may not work for you.   I now recommend something more like:
seed=(pfull(nlev)-int(pfull(nlev)))*100000+1
as always seeding with a small number could in theory 
cause problems.

From version 2.2 onwards, seed is passed as an argument to
isccp_cloudtypes.

( Note that a seed value of 50 is required to get the correct
test output. )

6) Memory usage

If you find that you run out of memory setting large values
on NCOL, try calling the simulator repeatedly and averaging the
results. If you do this, set the seed before the first call
but let subsequent calls use the value of the seed returned
by the previous call.  If you set the seed to the 
same value for each call, each call will give exactly the
same results, defeating the object of the repeated
call.

7) Check the results against your total cloud amount.

It is strongly recommended that you check that the code is 
giving results consistent with the overlap scheme used in
your radiation code.  This can be done by:

     a) setting the sample size to a large values, ideally 10000
	( ncol=10000 or an average of 100 calls with ncol=100 )
     b) running the code on a varied selection of inputs from your
        model
     c) summing the output from all of the cloud classes (including the
	ones representing points with tau < isccp_taumin ) and checking
        that this is statistically equivalent to the total cloud amount 
        diagnosed in your radiation scheme for sunlit points.

If you can't get this to agree, switch on the debug flag and 
check that the values are being passed in properly.  Look
at the code and see if you are using a value of overlap 
that is consistent with the overaps used in your radiation
code.  Failing that, I may be able to help, but can't guarantee
to be able to modify the code to match your overlap assumptions.

8) Set top_height=1 for best comparisons with ISCCP IR-VIS.

It is recommended that for best comparison with ISCCP IR-VIS
products the code be set to calculate effective rather than
actual cloud top pressures i.e. set top_height to 1 
for comparison with VIS/IR daytime products (consistent
with Webb et al 2001.)  However if you want to examine
nighttime products from ISCCP, the option set top_height = 3
would be the best one to compare to at night.  Using top_height
= 1 at night would be significantly worse in this case.

9) Handling sunlit points.

for top_height=(1 or 2) the input array sunlit should be set to 1 for 
day points and 0 for nighttime points.  

The outputs are set as described below:  
for the following values of top_height.  These output
domains are set on the assumption that the outputs
will be compared with the ISCCP variables shown below
(documented at

http://eosweb.larc.nasa.gov/PRODOCS/isccp/table_isccp.html
  -> D1 parameters list.
  -> DX parameters list
)

all points (A), sunlit points only (S) or not diagnosed (0)

top_height=1
------------

diagnostic domain comparable ISCCP diagnostic

fq_isccp,       S D1 30d-71d
totalcldarea,   A D1 12  Number of cloudy pixels
meanptop,       S D1 78  Mean PC for cloudy pixels (VIS-adjusted day, unadjusted night)
meantaucld,     S D1 92d Mean TAU for cloudy pixels
boxtau,         S DX 26. VALBTA    : VIS retrieved cloud tau or surface albedo 
                  DX 30. VTAUIC    : VIS retrieved ice cloud tau 
boxptop         S DX 29. VPRS      : VIS adjusted cloud top pressure

Please note that currently meanptop is not diagnosed in the IS for night-time
points if top_height=1, although ISCCP D1 item 78 is available for day and night
points.  For top_height=1, meanptop should only be compared to
item 78 for sunlit points.

top_height=2 
------------

as for top_height=1

top_height=3
------------

diagnostic domain comparable ISCCP diagnostic

fq_isccp,       A d1 23-29 Cloud top pressure distribution (unadjusted PC)
                ( although this is in 7 classes rather than 49, so 
                you need to sum over the different tau classes, excluding
                the ones for tau below isccp_taumin) *
totalcldarea,   A d1 13  Number of IR-cloudy pixels
meanptop,       A d1 79  Mean PC for IR-cloudy pixels (unadjusted)
meantaucld,     0 not diagnosed
boxtau,         0 not diagnosed
boxptop         A dx 18. IPRS      : IR retrieved cloud top pressure

* Note from Steve:

Note that we have a problem here. One thing I didn't sort out was
that for the ir-only method what would be a minimum cloud emissivity for
which the ir-threshold method would not detect cloud.  Probably the most
defensible method, which would not be hard to implement, would be to compare
the clear sky and cloudy sky brightness temperatures.  If the difference is
less than the ir-thresholds then the cloud would not be seen by ISCCP.
The table of ir-thresholds is table 3.2.4 of ISCCP documentation.  This
table is scene-type (e.g. ocean/land/coastal/high topography/snow covered)
dependent.  Thus the simulator inputs would need to have this information
included.  This will take time to build.

For now probably the best thing is to do is to note that
the isccp_taumin thresholding is not the best but an interim measure until
a proper method (i.e. the last paragraph) can be implemented.

A) Time averaging of outputs from the ISCCP simulator. 

It is important to be careful when time averaging the outputs from 
the IS as some variables are set to a missing data indicator in certain 
cases - e.g. for night points when top_height=(1 or 2) and for in-cloud
values where the cloud fraction is zero.

The time averaging needs to be done outside the simulator, and from
version 3.6 onwards, this can be done in two ways.

1/ Missing data indicator method

This option may be preferred in models where the time averaging
system knows about missing data indicators.

icarus.f now contains a data statement which sets 
a real variable called "output_missing_value" to a value of
-1.E+30.   This can be changed to match the value of the 
missing data indicator in the model.

Variables fq_isccp and totalcldarea will contain the missing 
data indicator at night when top_height=(1 or 2).  These should 
be averaged only over the points which are not flagged by the 
missing data indicator.

Variables "meanptop", "meanalbedocld" and "meantaucld" will contain
the missing data indicator at night when top_height=(1 or 2),
and also when totalcldarea is zero.

Time means for "meanptop", "meanalbedocld", "meantaucld" should
be made using cloud area weighted grid box mean values to
prevent, for example, very large optical depths over
small cloud fractions dominating the statistics - e.g.

gridbox_meanalbedocld=meanalbedocld*totalcldarea for totalcldarea>0
                     =0                          for totalcldarea=0

These should be set zero when totalcldarea is zero, overwriting
the missing data indicator value present.  Any missing data 
indicators at night time points should remain, as for fq_isccp 
and totalcldarea.

At the analysis stage these should be divided by the mean cloud fraction 
to produce cloud area weighted in-cloud values - e.g.

avg(meanalbedocld) = avg(gridbox_meanalbedocld)/avg(totalcldarea).

Points where avg(totalcldarea) is zero will need to be considered
'missing data'

2/ Missing data mask method

This option may be preferred in models where the time averaging
system does not use missing data indicators, and is based on
guidance for versions of the simulator prior to vn3.6.

icarus.f now contains a data statement which sets 
a real variable called "output_missing_value" to a value of
-1.E+30.   This value should be set to zero.

When top_height=(1 or 2), a sunlit points mask variable will be
required.  This needs to be type REAL, taking a value of 1 for
lit points and a value of 0 for night points (essentially
a REAL version of the INTEGER sunlit variable.)  A time
average should be made of this variable, which will be used
to 'unweight' the time mean outputs at the analysis stage.

Variables fq_isccp and totalcldarea will contain zeros at night 
when top_height=(1 or 2).  These variables should be averaged over
all points.   These averages can be divided by the time average
sunlit mask at the analysis stage.  Points where the mask is
zero should be considered 'missing data'.

Variables "meanptop", "meanalbedocld" and "meantaucld" will contain
zeros at night when top_height=(1 or 2), and also when totalcldarea 
is zero.

Time means for "meanptop", "meanalbedocld", "meantaucld" should
be made using cloud area weighted grid box mean values to
prevent, for example, very large optical depths over
small cloud fractions dominating the statistics - e.g.

gridbox_meanalbedocld=meanalbedocld*totalcldarea 

In this case all points can be treated the same way in the 
weighting process and in the time averaging.

At the analysis stage these should be divided by the mean cloud fraction 
to produce cloud area weighted in-cloud values - e.g.

avg(meanalbedocld) = avg(gridbox_meanalbedocld)/avg(totalcldarea).

Locations where avg(totalcldarea) (e.g. night points or cloud
free points) are zero should be considered 'missing data'.  
Care should be taken to ensure that the numerator and demoninator 
have had the sunlit mask applied consistently to both, or to 
neither, which should give the same result.

Mark Webb 
________________________________________________________________________

6. Revision history of released versions
----------------------------------------
_______________________________________________________________________________

Changes made by Steve Klein from version 4.0 to 4.1

1/ This is a bugfix for a bug found by Jason Cole (thanks, Jason!).
This is for *RARE* cases where the cloud temperature is greater than
any temperature in the troposphere, however, the maximum temperature
in the atmosphere is in the stratosphere. Under these cases, the cloud
top pressure variable, ptop, was not assigned, and (depending on
compiler) the model crashes because it couldn't evaluate the scalar
operations involving ptop.

The fix is to assign the maximum and minimum temperatures used in the
cloud-top pressure detection to temperatures within the troposphere.
This will prevent any values of ptop remaining undefined.

_______________________________________________________________________________

Changes made by Steve Klein and Mark Webb from version 3.8 to 4.0

1/ The experimental setting to top_height_direction (= 2) is now declared
to be the default setting for all ISCCP simulator uses. When implementing
version 4.0 into a model, please confirm that the setting of this
variable is correct.

2/ BSD copyright is now applied to the simulator.
_______________________________________________________________________________

Changes made by Steve Klein and Mark Webb from version 3.7 to 3.8

Three changes have been made in this release.  

1/ isccp_cloud_types is now a wrapper routine which calls SCOPS and
ICARUS routines.  This has been done to allow the ISCCP simulator to
be easily bundled along with COSP (CFMIP Observational Simulator 
Package - see www.cfmip.net )  The ICARUS routine contains most 
of the code that was in ISCCP_CLOUD_TYPES previously.  

The integer call_scops has been removed from the ISCCP_CLOUD_TYPES
argument list, as users who wish to bypass SCOPS can now do so by 
calling the ICARUS routine directly.

Users who call SCOPS directly should note that SCOPS now takes 
slightly different arguments.  Previously the cloud fraction 
inputs cc(npoints,nlev) and conv(npoints,nlev) were copied into
tca(npoints,0:nlev) and cca(npoints,nlev) in ISCCP_CLOUD_TYPES,
which were then passed into SCOPS.  SCOPS now takes cc(npoints,nlev) 
and conv(npoints,nlev) as inputs, to be consistent with ISCCP_CLOUD_TYPES
and ICARUS.  tca(npoints,0:nlev) is now an internal variable in SCOPS, 
and the cca variable has been removed since it was an unnecessary copy 
of the conv variable.

2/ This version of the simulator adds the capability to output as
diagnostic variables the grid-box mean (i.e. average over sub-columns)
of the 10.5 micron infrared brightness temperature for all-sky and
clear-sky conditions. These variables are called "meantb" and "meantbclr

3/ A minor bugfix has been made to some debugging code in icarus.f
to prevent an array bound error when setting the levmatch variable.
levmatch is a debugging variable and and fixing this bug does not
affect pc-tau counts or any other output variables of the
simulator.

_______________________________________________________________________________
Changes made by Steve Klein and Mark Webb from version 3.6 to 3.7

Two changes have been made in this release.

1/ In all previous versions of the code, two input tables were used to
convert cloud optical thickness into cloud albedo and vice versa. These
tables, invtau and tautab, came from the original ISCCP software and
documentation. In this version of the code, these tables are removed
from the code and replaced with the following analytic functions which
are a reasonable fit to these tables.

ALB =   TAU**0.895   /    (   TAU**0.895  +  6.82 )

TAU =  (  6.82  / ( ( 1. / ALB ) - 1. ) ) ** (1./0.895)

where TAU is the cloud optical thickness and ALB is the cloud albedo.

2/ Option to bypass call to SCOPS in ISCCP_CLOUD_TYPES

This is to support integration into the CFMIP Observational Simulator
Package (COSP - see http://www.cfmip.net )

New input arguments for isccp_cloud_types:

integer call_scops
real frac_out(npoints,ncol,nlev)

Default behaviour when using the ISCCP simulator on its own is
to set the integer argument call_scops to 1.   frac_out does
not need to be set in this case.

If calling scops externally to isccp_cloud_types, call_scops should
be set to zero and overlap information passed in using frac_out.

_______________________________________________________________________________
Changes made by Steve Klein from versions 3.5.1 to 3.6

The modifications can be divided into three groups. Those involving 
averages of cloud properties across the sub-columns (i.e. the 
"Lightweight" diagnostics of total cloud area, mean cloud top pressure, 
and mean cloud albedo), those involving the determination of 
cloud-top pressure, and those to handle missing data issues.

All modifications (except the missing data issues) change results, 
although the differences in results should be relatively minor for 
most situations. The most prominent differences will be:

(a) the values of the lightweight diagnostics in grid-boxes that have a 
lot of cloud with optical thickness less than the minimum that ISCCP can 
detect ("isccp_taumin" = 0.3).

(b) the values of cloud-top pressure for clouds under strong temperature 
inversions (e.g. marine stratocumulus) when the experimental cloud-top 
pressure assignment is used. Note that currently the experimental 
cloud-top pressure assignment is not recommended but may be become 
default if it is demonstrated to yield improved agreement with ISCCP 
observations through ICARUS-assessment tests being performed by Jay Mace 
and his colleagues (e.g. Mace et al. JGR 2005, doi: 10.1029/2005JD005921).

Missing data handling
---------------------

1) In the case of dark points but "top_height" not equal to 3, the
output variables will now be equal to the value of a new real defined in the
code. This real is called "output_missing_value" and set to -1.E+30 in
a data statement in icarus.f.  Note that the output variables include
"fq_isccp", "totalcldarea", "meanptop", "meanalbedocld", "meantaucld",
"boxptop", and "boxtau".

2) In the case of "totalcldarea" = 0., "meanptop", "meanalbedocld" and
"meantaucld" will be equal to the "output_missing_value".

3) For all sub-columns with no cloud, "boxptop" and "boxtau" now have
the "output_missing_value" and not the values of 0. as they did previously.


Lightweight diagnostics modifications
-------------------------------------

1. Restrictions involving the reporting of totalcldarea

In the original code, "totalcldarea", which is the diagnostic for the 
total horizontal area of a grid box covered by clouds at any level, was 
always calculated. This was done even though other diagnostics such as 
"meanptop" and "meantaucld", which are the mean cloud-top pressure and 
cloud optical thickness, were calculated only for sunlit gridpoints in 
most circumstances. This modification restricts the calculation of 
"totalcldarea" to the same situations used to calculate "meanptop" and 
"meantaucld".

To review, these diagnostics are calculated only if the grid-box is 
sunlit in the case that "top_height" = 1 or 2, and at all times is 
"top_height" = 3. The value of "top_height" equal to 1 corresponds to 
the calculation of cloud-top pressure using algorithms appropriate to 
compare to ISCCP daylight data (i.e. the pc-tau diagrams) and is the 
value used in all CFMIP projects.  The value of "top_height" equal to 2 
corresponds the assignment of cloud-top pressure at the model's actual 
highest cloud-top pressure. The value of "top_height" equal to 3 
corresponds to the calculation of cloud-top pressure according the 
methods that correspond to ISCCP IR-only retrievals which are performed 
both at night and during the day. It should be used when comparing to 
the ISCCP IR-only cloud data, but I haven't seen that ever done, 
although it could be.

2. Addition of "meanalbedocld" diagnostic

Williams and Webb (2008, Climate Dynamics doi:10.1007/s00382-008-0443-1) 
created the term "Lightweight diagnostics" to represent a much smaller 
set of ISCCP simulator output quantities than the 49 pc-tau histograms. 
These diagnostics are the total cloud area ("totalcldarea"), the mean 
cloud-top pressure ("meanptop"), and the mean cloud albedo 
("meanalbedocld").  These lightweight diagnostics were also used in an 
earlier clustering study involving ISCCP observations (Gordon et al. JGR 
2005 doi:10.1029/2004JD005027).

The modification is to have the simulator compute and output the mean 
cloud albedo ("meanalbedocld"). Previous versions of the ISCCP simulator 
did not do this but output the mean cloud optical thickness 
("meantaucld") which was internally calculated from the mean cloud 
albedo. This modification results in the addition of "meanalbedocld" to 
the isccp_cloud_types subroutine interface and will require users to 
make appropriate modifications to their calling statements to ISCCP 
simulator.

3. Restriction of lightweight diagnostics to clouds with optical 
thickness greater than the minimum ISCCP can detect ("isccp_taumin")

If these lightweight diagnostics are to be compared to ISCCP 
observations, they must be calculated using only the model clouds that 
ISCCP can detect. In the case of the simulator, this condition is 
determined by saying that clouds with optical thickness less than a 
threshold ("isccp_taumin") are not detectable by ISCCP. The current 
value of "isccp_taumin" is 0.3.  The modification limits the calculation 
of "totalcldarea", "meanptop", "meanalbedocld", and "meanptop", to 
clouds in sub-columns with column cloud optical thicknesses greater than 
"isccp_taumin". Previous versions of the simulator did not impose this 
restriction.

This will have a noticeable impact on the values of the lightweight 
diagnostics in grid-boxes that have a lot of model clouds with optical 
thickness less than "isccp_taumin". Relative to previous versions of the 
ISCCP simulator, this will result in decreases of "totalcldarea" and 
increases of "meanalbedocld" and "meantaucld".


Cloud-top pressure modifications
--------------------------------

1. Actual cloud-top pressures for "top_height" equal to 2.

In the case that one wants to examine the cloud-top pressures actually 
produced by a model, one selects "top_height" equal to 2. This 
modification is to calculate the cloud-top pressure as the half-level 
pressure corresponding to the top of the highest model level to contain 
any cloud. Previous versions assigned the cloud-top pressure to the 
full-level pressure of the highest model level to contain any cloud. 
Note the terminology is that "full" level is the pressure corresponding 
to somewhere in the middle of the model level and that "half" level is 
the pressure corresponding to the boundaries of a model level. Thus, 
half levels are staggered relative to full levels.  This modification, 
following a suggestion of Tony Del Genio, now obeys the common 
parameterization assumption that where clouds occur, they fill the full 
vertical extent of a model level. Under this assumption, the actual 
cloud-top pressure is at the top of the model level.

Note that "top_height" equal to 2 is not generally used.

2. Interpolated cloud-top pressure

In the case, "top_height" equals 1 or 3, the cloud-top pressure is 
determined from an infrared brightness-temperature derived cloud-top 
temperature. This involves a step of locating the pressure level on a 
temperature profile which has the radiance-derived cloud-top 
temperature. The modification now determines cloud-top pressure through 
vertical interpolation in log-pressure space of the model's temperature 
profile. Previous versions had determined the cloud-top pressure as the 
full-level pressure of the model level which had a temperature nearest 
the radiance-derived cloud-top temperature.  The new method is more 
accurate.

As you might expect, this change has a very minor impact on the pc-tau 
histogram for the generally true condition when model levels are more 
finely spaced in pressure than the 7 cloud-top pressure bins. In some 
applications though, users had examined the values of cloud-top pressure 
directly and found that the default procedure was insufficient. This was 
first noticed by Steve Krueger and Yali Luo (Luo et al. JAS 2005). This 
change can also markedly impact the values of the mean cloud-top 
pressure "meanptop".

3. Experimental alternative radiance-derived cloud-top pressure

In the case that there is only one tropospheric level in a temperature 
profile with temperature equal to the radiance-derived cloud-top 
temperature, the method to determine cloud-top pressure has a unique 
solution. However, when temperature inversions occur, there are multiple 
solutions for cloud-top pressures. Because many clouds are capped by 
inversions (e.g. marine stratocumulus clouds), this situation arises 
frequently in the atmosphere.

All previous versions of the ISCCP simulator have set the cloud-top 
pressure to be that corresponding to the highest pressure level (i.e. 
lowest altitude level) which has the temperature closest to the 
radiance-derived cloud-top temperature. In normal circumstances, this 
cloud-top pressure would be very close to the actual cloud-top pressure 
in the model.

However, there is abundant evidence (from most recently the new Clousat 
and Calipso data) that ISCCP (and other satellite retrievals involving 
passive visible and infrared radiances) is more likely to set the 
cloud-top pressure in this circumstance closer to the lowest pressure 
level (i.e. highest altitude level) which has the temperature closest to 
the radiance-determined cloud-top temperature. In the case of marine 
stratocumulus clouds underneath at 10K inversion this difference can be 
very large. For example, it might be the difference between a cloud-top 
pressure of ~900 mb and ~750 mb.  It is for this reason that Pat Minnis 
(Minnis et al. J. Appl. Met. 1992) derived an alternative technique for 
cloud-top height by scaling the difference between cloud-top and 
sea-surface temperatures by an assumed lapse-rate for the marine 
boundary layer. Indeed, one finds that in marine stratocumulus regions 
that ISCCP reports a lot of cloud in the cloud-top pressure bin between 
680 and 800 mb. Probably much of this cloud should properly be in the 
800 mb to the surface cloud-top pressure bin. A recent article (Garay, M.
J., S. P. de Szoeke, and C. M. Moroney (2008), Comparison of marine
stratocumulus cloud top heights in the southeastern Pacific retrieved
from satellites with coincident ship-based observations, J. Geophys.
Res., 113, D18204, doi:10.1029/2008JD009975) shows that ISCCP seriously
underestimates the cloud-top pressure under these situations (see Garay
et al. Figure 2).

However, the purpose of the simulator is to mimic ISCCP data and this an 
important ISCCP error which probably should be mimicked. Thus, as an 
experimental modification, a flag variable "top_height_direction" is 
provided which allows one to choose whether or not to set the cloud-top 
pressure as the highest or lowest pressure for which the interpolated 
temperature profile matches the radiance-derived cloud-top temperature.

The flag variable "top_height_direction" is a new interface variable to 
the isccp_cloud_types subroutine. When "top_height_direction" equals 1, 
the cloud-top pressure corresponds to the highest pressure (i.e. lowest 
altitude) with temperature matching the radiance-derived cloud-top 
temperature. When "top_height_direction" equals 2, the cloud-top 
pressure corresponds to the lowest pressure (i.e. highest altitude) 
level with the matching cloud-top temperature.

All previous versions of the ISCCP simulator have been using the method 
that corresponds to "top_height_direction" equal to 1. This remains the 
recommended default setting. However, if further work (i.e. 
ICARUS-assessment tests by Jay Mace) demonstrates that 
"top_height_direction" equal to 2 would be a better match to the ISCCP 
cloud-top pressures then in the future it may be recommended to use 
"top_height_direction" equal to 2.

_______________________________________________________________________________

Changes made by Mark Webb from version 3.5 to 3.5.1

SCOPS separated out into its own subroutine (8/11/06)

_______________________________________________________________________________
Changes made by Mark Webb from version 3.4.1 to 3.5

Version released under LGPL ( GNU Public License )
Introduced a new random number generator to allow release
under LGPL.  Results should be statistically equivalent to 3.4
Minor changes to the README file on seeding.
Updated Steve's email address
_______________________________________________________________________________
Changes made by Mark Webb from version 3.3 to 3.4.
Changes made by Mark Webb from version 3.4 to 3.4.1

Changes to the README file mainly on guidance for setting NCOL.
Code exactly as 3.4
_______________________________________________________________________________
Changes made by Mark Webb from version 3.3 to 3.4.

Default value for attrop changed to 120K, on request from Steve.
Reference to nlevmax removed from isccp_cloud_types.f - this
caused a segfault when running the tests under Linux/g77.
Moved initialisation of tauchk to start of isccp_cloud_types.f
as it was not being initialised for top_height = 2.

Various minor changes to the README file requested by Steve.
_______________________________________________________________________________

Changes made by Mark Webb and Steve Klein from version 3.2 to 3.3.

Converted debug to be an integer value - 0 = no printing,
non-zero specifies the step with which the printout loops over j.

Added debugcol allow separate activation of box printout.

Modified tropopause diagnosis:

Previously, the code worked by starting at the top of the atmosphere
and working down, setting the tropopause temperature to the layer 
temperature as long as the layer temperature is greater than in
the layer below - i.e. the tropopause temperature was the
temperature of the lowest layer that is in or near to the
stratosphere.  The problem with this is that if the atmosphere 
is isothermal, or if temperature monotonically decreases with
height, attrop is never set, and the default value of ptrop
of 50mb is used.

The main effect of this was (in the cases where a tropopause
was not found ) to set the cloud tops pressures
for pixels with optical thickness around .3 or less to 50mb.
This is not thought a serious problem, as most of the cloud 
affected is below the detection threshold for ISCCP anyway.

This version has been modified to diagnose the tropopause as
the coldest level between 400mb and 50mb.   A tropopause will
always be diagnosed if the inputs have pressure levels
between these levels.  Failing that, ptrop will be set
to 50Mb, and attrop will be set to 0K.  This is safer
than setting attrop to, say, 200K as the emissivity correction
for optically thin clouds does not work properly if the
cloud temperature is lower than that of attrop.

_______________________________________________________________________________

Changes made by Bryant McAvaney going from version 3.1 to 3.2.

Bryant made the following changes:

renamed:

isccp_cloudtypes.f, test_isccp_cloudtypes.f, test_isccp_cloudtypes.f

to

isccp_cloud_types.f, test_isccp_cloud_types.f, test_isccp_cloud_types.f

to avoid problems when using interactive debugger.

itrop -> itrop(npoints) ( fix for bug in version 3.1 )

initialised attrop to 200K and itrop to 1 ( as were uninitialised on 
occasions where tropopause code failed to find a tropopause. )

now only do emcld adjustment to fluxtop if tau(j,ibox) .gt. (tauchk) - 
this aviods floating exceptions when ir inputs are passed with no or v.
small vis values e.g. at dawn/dusk

removal of ncolmax, nlevmax

added debugcol logical for column printout

modified most of ncolprint loops to work in vector mode

re-ordered write statements under 'debug' to match argument list

initialised boxtau,boxptop,box_cloudy

moved bb emission calc out of ibox loop

added rec2p13 to hold reciprocal of 2.13

added tauchk to hold -1.*log(0.9999999) value

moved btcmin calc out of ibox loop

_______________________________________________________________________________

Changes made by Mark Webb in going from Version 3.0 to Version 3.1

( ** Please note that a bug has been found in this version.
The tropopause index itrop was not dimensioned over npoints.
This is corrected in version 3.3 )  

This version is scientifically equivalent to version 3.0, but
is optimised to run more efficiently on vector processors
such as the NEC SX-6.  It should be bit reproducible with version 3.0.
Please let me know if you find a situation where the two versions
give difference answers for consistent inputs.

To do this I have had to modify isccp_cloudtypes.f to accept
vector inputs rather than single column inputs.   This version
can still be called column by column, ( just set npoints to
1 ), but this is very inefficient on vector architectures.

A few new arguments have been added - I have put these at
the start of the argument list to make them easier to spot.

debug is a logical which, if set to true, tell isccp_cloudtypes
to print out lots of diagnostics to unit 6 and unit 9.  debug
is set to true in test_isccp_cloudtypes.f, and is useful for
checking that you are passing all of the relevant arguments
in correctly.

npoints is the number of grid points in the horizontal that you
want to process.  Most of the input variables now have npoints
as their first array dimension, and most of the inner loops
in isccp_cloudtypes loop over this array dimension, (and do
vectorise on the sx-6).  The larger the value of npoints, the
better the performance you are likely to get on a vector 
processor.  The code is not structured to vectorise over 
loops over ncol/ibox, although a small number of these do.

sunlit should be set to 1 for day points and 0 for nighttime
points.  See the section below on handling sunlit points for a 
discussion of the related issues.

Added sections on setting NCOL, handling sunlit points, and
averaging to the 'things to bear in mind' section.
_______________________________________________________________________________

Changes made by Steve Klein in going from Version 2.2.1.1 to Version 3.0

*** Please note that moving to this version changes the results given 
by the code ***

1. Based upon e-mail correspondance with Bill Rossow, the minimum visible 
   optical thickness ISCCP is assumed to detect is set to 0.3.  Previous
   versions used 0.1.

2. Provisions haven been made for an IR-only cloud top pressure adjustment. This 
   permits comparison to ISCCP data at night. This is done by adding a third
   option to top_height which will be the IR-only adjusted top option. Note
   that the previous adjusted option used the visible optical depth to adjust 
   the cloud emissivity.  NOTE that one must still pass the visible optical 
   depth to the code even if one wishes IR-only calculations.  This is done 
   primarily to count the abundance of cloud types in different visible optical 
   thickness categories.  Comparison to IR-only ISCCP data is accomplished by 
   summing over all categories of optical thickness for a given cloud top 
   pressure interval.
   
3. Previous versions of the code assumed that tau-ir = tau_vis / 2.13 .  
   Note that 2.13 is the ice microphysics conversion factor.  An error comes 
   from using this factor for liquid phase clouds where the appropriate factor 
   is 2.56.  This has been accomodated by a small iteration loop after the 
   calculation of the cloud brightness temperature.  If the cloud brightness 
   temperature is greater than 260K (ISCCP's ice cloud threshold) then the 
   calculation of cloud brightness temperature is repeated using 2.56 instead 
   of 2.13.

The next minor correction is based upon a more careful reading of page 86 of 
the ISCCP documentation (available from the ISCCP web site):

Rossow, W.B., A.W. Walker, D.E. Beuschel, and M.D. Roiter, 1996: International 
Satellite Cloud Climatology Project (ISCCP) Documentation of New Cloud Datasets. WMO/TD-No. 737, World Meteorological
Organization, 115 pp.

4. Following the calculation of TRANS-MAX described towards the bottom of page 
   86, cloud top pressure is set to the tropopause pressure if tau < taumin 
   based upon transmax. In previous versions though, conversion of taumin from 
   IR-tau to VIS-tau before comparison to tau(ibox) was overlooked. Also at 
   this point, the cloud top temperature is assumed to be 5 K colder than 
   the tropopause temperature. Note that the documentation says the "cloud top 
   pressure equals tropopause pressure".  


============================================================================

Changes made by Mark Webb in going from Version 2.2 to Version 2.2.1.1

A very minor change was applied to test_isccp_cloudtypes.f to 
avoid a problem with some stricter compilers which do not allow
constants arguments to be overwritten in the called routine.  This
was happening because I was passing a constant 50 into the seed 
argument of isccp_cloudtypes instead of the variable seed.

Minor changes also made to Makefile and test_isccp_cloudtypes.ksh 
to remove the need for . in $PATH.

Mark Webb 2/7/2002

============================================================================

Changes made by Mark Webb in going from Version 2.1 to Version 2.2

1) seed is now passed in as an argument.  This makes it
   easier for the user to follow the advice in the README
   file wrt setting different seed values for subsequent calls.
2) a couple of lines were > 72 chars long, which gave errors
   with my compiler.
3) use of formatted write statements to print out values of
   totalcldarea, meanptop, meantaucld ( unformatted writes
   make the tests fail with different compilers as they
   output different white space characters.  I can't ignore
   white space in the comparison because this could mask
   errors in the overlap output. )
4) changes to the formats of some other write statements
   to reduce false errors with different rounding characteristics
   in formatting on different platforms.
5) the binary files containing the enery weightings only work on
   some platforms - I have added formatted versions that can be used
   as alternatives - specify readbinary=.false. in test_isccp_cloudtypes.f.

Mark Webb 26/6/2002

============================================================================

Changes made by Steve Klein in going from Version 2.0 to Version 2.1 

The code was modified so that the primary subroutine, ISCCP_CLOUD_TYPES,
now returns the fraction of columns that contain cloud ('totalcldarea'),
the mean cloud top pressure in the cloudy portion of the grid box ('meanptop'),
and the energy-weighted mean optical thickness ('meantaucld').  Note that
if the grid box contains no clouds whatsoever that meanptop and meantaucld
are both zero.

In addition, the code now returns the output on a column by column basis
of the cloud top pressure ('boxptop') and optical depth ('boxtau'). If
no cloud exists in the column then both these variables are zero.

In computing the energy-weighted mean optical thickness, the tables 
used by ISCCP are applied.  These tables are in binary files 'tautab.bin'
and 'invtau.bin' supplied in the tar file.  Note that the size of the
invtau vector is 45021, rather large.  These binary files are read by
the test_isccp_cloudtypes.f and passed as new input arguments to 
ISCCP_CLOUD_TYPES.

The 'stdout.expected' file is modified from that of version 2.0 in that
the additional fields output by ISCCP_CLOUD_TYPES (totalcldarea, meanptop,
meantaucld, boxtau, boxptop) are added.  Apart from the additions the
stdout.expected file is identical to that in version 2.0.

============================================================================

What's new in 2.0 since 1.17.1.1:

	o No functional changes
	o The tests can now be run using 'make test'
	o There is now a README file
	o All write statements in the tests should use formatted I/O,
  	  which makes it easier to check whether the tests have passed
  	  on different platforms.

_______________________________________________________________________________

Version 1.17.1.1 was made available to a few people by Steve in
a tar file called new_isccp_mark_steve.tar.  Note that the version 
number in isccp_cloudtypes.f is 1.16 although this version is not 
the same as version 1.16.

The contents were:

$ tar tvf new_isccp_mark_steve.tar   
rwxr-xr-x 1116/77      0 Oct 28 18:57 1999 isccp_mark_steve/
rw-r--r-- 1116/77 389120 Oct 28 18:59 1999 isccp_mark_steve/Makefile
rw-r--r-- 1116/77   2595 Aug 15 18:02 1999 isccp_mark_steve/coldecomp.steve.data
rw-r--r-- 1116/77   4534 Aug 14 15:57 1999 isccp_mark_steve/input.data
rw-r--r-- 1116/77    870 Aug 14 15:40 1999 isccp_mark_steve/input.data.mark
rw-r--r-- 1116/77   4534 Aug 14 15:56 1999 isccp_mark_steve/input.data.steve
rw-r--r-- 1116/77  34106 Oct 28 18:55 1999 isccp_mark_steve/isccp_cloudtypes.f
rw-r--r-- 1116/77 325816 Aug 15 17:50 1999 isccp_mark_steve/output.steve.data
rw-r--r-- 1116/77    601 Aug 14 16:06 1999 isccp_mark_steve/ran0.f
rw-r--r-- 1116/77    442 Aug  5 16:25 1999 isccp_mark_steve/rcs_ids
rw-r--r-- 1116/77   3045 Aug 15 18:19 1999 isccp_mark_steve/test_isccp_cloudtypes.f

What was new in 1.17.1.1 compared to 1.17.

	o No functional changes
	o Steve's changes to various boolean expressions to improve portability.

_______________________________________________________________________________

What was new in 1.17 compared to 1.16.

	o new code to handles water vapour
	o uses a better tau - emissivity relationship 
	o notes cloud amounts with tau < 0.1

Version 1.17 was distributed by Steve in a tar file which was most likely
called isccp_mark_steve.tar ( I don't have a copy or the tarfile! ). 
Note that the version number in isccp_cloudtypes.f is also 1.16 although this 
version is not the same as version 1.16.  This version of isccp_cloudtypes.f 
is 33348 bytes long.

_______________________________________________________________________________

Version 1.16 was distributed by Mark in a file called isccp_mark.tar
( as were some previous versions ).  This was in August 1999

The contents were:

$ tar tvf isccp_mark.tar
r--r--r-- 275/107  28516 Aug  3 17:20 1999 isccp_mark/isccp_cloudtypes.f
r--r--r-- 275/107   2769 Aug  5 16:22 1999 isccp_mark/test_isccp_cloudtypes.f
r--r--r-- 275/107    587 Jul 29 10:51 1999 isccp_mark/ran0.f
r--r--r-- 275/107    791 Aug  5 16:24 1999 isccp_mark/input.data
r--r--r-- 275/107    432 Aug  3 15:57 1999 isccp_mark/Makefile
rw-r--r-- 275/107    442 Aug  5 16:25 1999 isccp_mark/rcs_ids

What was new in 1.16 compared to 1.13.

	o correct treatment of top_height=2
	o correct treatment of convective cloud in random & max overlap cases
	o correct treatment of stratiform cloud above convective cloud

_______________________________________________________________________________

Version 1.13 was distributed by Mark in a file called isccp_mark.tar
in July 1999.   Note that this version was still in development and 
had the following problems which were ironed out later:

	o doesn't work for top_height=2 
	o convective cloud not treated properly in random or max overlap cases
	o stratiform cloud above convective cloud not quite right

-rw-r--r--   1 hadmw      mec          40960 Mar 20 09:53 isccp_mark.tar

$ tar tvf isccp_mark.tar 
r--r--r-- 275/107  27454 Jul 29 10:48 1999 isccp_mark/isccp_cloudtypes.f
r--r--r-- 275/107   3931 Jul 29 10:54 1999 isccp_mark/test_isccp_cloudtypes.f
r--r--r-- 275/107    587 Jul 29 10:51 1999 isccp_mark/ran0.f
r--r--r-- 275/107   1619 Jul 29 11:07 1999 isccp_mark/input.data
r--r--r-- 275/107    582 Jul 29 11:36 1999 isccp_mark/Makefile
rw-r--r-- 275/107    441 Jul 29 11:36 1999 isccp_mark/rcs_ids

What was new in 1.13 compared to 1.1.

	o Various changes to make FORTRAN code consistent with Mark's PV-Wave
          including:
	o support for convective as well as stratiform clouds in the same gridbox
	o pseudo-random sampling method to fix 'left fill' problem 

_______________________________________________________________________________

Version 1.1

Steve's box code, as used in Klein & Jacob 1999.  Received by Mark in July 1999.
isccp_cloudtypes.f  is 22230 bytes long.

This version doesn't support convective clouds, and suffers from the 'left fill'
problem.

_______________________________________________________________________________

7. Some other issues to consider
---------------------------------

Steve's email accompanying distribution of release 1.1 in Jan 1999.
 
> NOTES/QUESTIONS/ISSUES TO BE RESOLVED BY THE GROUP AS A WHOLE
> 
> 1. Following our discussion, we have agreed that the optical depth used
>    should be exactly the same as that used in the host GCM.  Thus the
>    program takes as input the optical depth in each model level.  OK?
> 
> 2. The program takes each vertical profile of cloud cover and subdivides
>    an atmospheric column into homogenous columns.  The suggested number of
>    columns is 100.  It is important to note, that the cloud COVER not
>    cloud FRACTION of each model level is required as input.  For example
>    the GISS GCM assumes something that the clouds do not fill the grid
>    box in the vertical completely (at least they did in DelGenio's 1996
>    paper).  They will need to convert their cloud fraction to a cloud cover
>    before input. 
> 
> 3. Cloud top pressure is determined by 2 methods.  If top_height is set
>    to 2, then the cloud top pressure is set to be the mean pressure of the
>    highest cloudy level of each sub-column that contains clouds. This is
>    done in the line:
> 
>                              ptop(ibox)=pfull(ilev)
> 
>    A choice is made here in that one could use the half-pressure level at
>    the top of a given pressure level.  I suppose users will customize.  OK?
> 
> 4. The second method for determining cloud top pressure (used it top_height
>    is set to 1), computes an approximate 11 micron radiance and then follows
>    ISCCP procedures (assuming a single level of cloud) to determine the cloud
>    top pressure.  To do this the program requires more input including:
>    the model's cloud 11 micron emissivity in each level, the surface skin
>    temperature, the air temperature, and the surface's 11 micron emissivity.
>    Questions that arise here are:
> 
>    (a) Should we include a rough simulation of the water vapor continuum
>        as is done in Yu et al., Climate Dynamics, 1996, pages 389-401.?
>        If we do, we will need the specific humidity of water vapor in each
>        level, and a formula for the continuum.  I would use the Roberts
>        formula used in Yu et al. which is much simpler than the one used
>        by ISCCP (see D level documentation page 77).
>    (b) If data is not easily available, should we assume a longwave
>        emissivity for the skin or a temperature relationship between the
>        model's temperature and the skin temperature?

( Note Steve added the continuum treatment at version 1.17 ) 

> 5. What is the minimum optical depth we should assume ISCCP clouds can detect?
>    0.1 (the default of the program) or 0.2?   Should we create (not done in
>    the current program) a separate tau category for all the clouds with tau
>    less than taumin.   ISCCP experts opinions are most needed here.
 
( also done at 1.17 )

> 6. To distribute the clouds, you need to have an overlap assumption. Currently
>    the program has 3 options:   maximum, random, and maximum-random.  The
>    program default is max-random, so that to use the other programs you will
>    need to edit the program and uncomment the alternative line of code to
>    change the overlap assumption.  Don't forget to comment the line of code
>    for max-random then.   (Search the program for 'CLOUD OVERLAP
>    ASSUMPTION' to find these lines of code)
> 
> 7. Horizontal Cloud Inhomogeneity.  Currently the program distributes the
>    cloud optical depth (and longwave emissivity if used) evenly in the hor-
>    izontal.  Users may wish to do other things here depending on their
>    model's assumptions.   Note that if you change this, you have to make
>    an assumption about the vertical correlation of the cloud inhomogeneity
>    (e.g. are the thicker parts of the cloud in level i, directly beneath
>    the thicker parts of the cloud in level i+1).   The line of code to change
>    for optical depth is:
> 
>                do 16 ibox=1,ncol
>                  tau(ibox)=tau(ibox)+real(BOX(ilev,ibox))*dtau(ilev)
>             16 continue
> 
>     where ibox is the index variable for the number of columns.
>     The three places the longwave emissivity is used (variable name dem)
>     will also need to be changed if you decide to have horizontal cloud
>     inhomogeneity.
> 
> Cheers,
> Steve





</pre>
<p>
	&nbsp;</p>
</div> <!--// end div id=cog_post_body //-->