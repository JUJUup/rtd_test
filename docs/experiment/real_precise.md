# 关于DART/WRF的精度问题

## 关于内存占用

!!! note
    Modify the DART code to use 32bit reals. Most WRF/DART users run both the WRF model and the DART assimilation code using 32bit reals. This is not the default for the DART code. Make this single code change before building the DART executables to compile all reals as 32bit reals.

更多相关的信息请参看[WRF/DART tutorial](https://docs.dart.ucar.edu/en/latest/models/wrf/tutorial/README.html#build-the-dart-executables) 简而言之，WRF使用单精度浮点数(4个字节，32 bit real number)，而DART默认使用的是64bit real(双精度浮点数)，这在进行内存需求量很大的运算时会要求额外许多的核数，我使用64bit real提交了1200个core到mpi都会报错内存不足，用32bit real的话600 core一下子就能跑了......

## 关于精度改变导致的quantile_method bug

邮件咨询了一下DART，下面是邮件记录：

---

Dear DARES members:

Im a graduate student at Nanjing University, and is trying to use DART quantile_method branch to do something. I encountered a bug in this branch, maybe is correlated with the real precision. Here is what happened:

im assimilating OSSE obs into WRF with fine model resolution, which need a large memory, so i change the real precision by editing $DART_DIR/assimilation_code/modules/utilities/types_mod.f90 follows this guide https://docs.dart.ucar.edu/en/latest/models/wrf/tutorial/README.html#build-the-dart-executables. 

!!! note
    ! real precision:

    ! TO RUN WITH REDUCED PRECISION REALS (and use correspondingly less memory)

    ! comment OUT the r8 definition below and use the second one:

    integer, parameter :: r4 = SELECTED_REAL_KIND(6,30)

    integer, parameter :: r8 = SELECTED_REAL_KIND(12)   ! 8 byte reals

    !integer, parameter :: r8 = r4                      ! alias r8 to r4

after a quickbuild recompile, the filter is re-submitted, and it abort with these message:

!!! error
    ERROR FROM:

    source : rh_distribution_mod.f90

    routine: inv_rh_cdf
    
    message:  x less than lower_bound           19 -2.0822274E-15

i have tried the two real precision in a coarser grid case, r8 goes smoothly without any problem, and r4 will encounter the same error:

!!! error
    ERROR FROM:

    source : rh_distribution_mod.f90

    routine: inv_rh_cdf

    message:  x less than lower_bound           11 -4.1970271E-16

The error part of code in inv_rh_cdf says:

```fortran
   ! Check for posterior violating bounds; This may not be needed after development testing
   if(bounded_below) then
      if(x(i) < lower_bound) then
         write(errstring, *) 'x less than lower_bound ', i, x(i)
         call error_handler(E_ERR, 'inv_rh_cdf', errstring, source)
      endif
   endif
   
   if(bounded_above) then
      if(x(i) > upper_bound) then
         write(errstring, *) 'x greater than upper_bound ', i, x(i)
         call error_handler(E_ERR, 'inv_rh_cdf', errstring, source)
      endif
   endif
```

Seems it is ok to just ignore these error by commenting out this part of code? Or is it needed to polish the check so it can ignore the small bound violation brought by real precision? I checked the newest quantile_method branch in Github, the source code where things went wrong may have changed to 'rh_distribution_mod.f90'.

Thanks for any reply~

Haoxing

---

On Sun, Jul 16, 2023 at 11:13 AM Jeffrey Anderson <jla@ucar.edu> wrote:
Haoxing,
The quantile_method branch has not been tested with reduced precision for reals. I think there are serious concerns about using the transforms with reduced precision, but I do not have time to evaluate those issues in the immediate future. A solution would be to work with higher resolution within certain parts of the assimilation code, but I have not thought about exactly what part of the code would require that. Obviously the message you report has identified at least one place.

You could go ahead and turn off this error and see what happens, but you should be extremely cautious in interpreting the results. 

Sorry that I can't be of more immediate help, Jeff

---

Hi Haoxing,

Here are a few namelist options you can set to reduce the per-node memory for filter.  Possibly your WRF domain is still too big, but you can give these a try. 

&assim_tools_nml
distribute_mean = .true.
/
This will spread the mean across all processors, rather than each processor having a copy of the mean during the assimilation.


$ensemble_manager_nml
layout = 2
tasks_per_node = N
/
where N is the number of tasks per node you are using on the machine.  This will spread out the ensemble members across the nodes, which reduces the per-node memory particularly when doing state IO.


&state_vector_io_nml
single_precision_output = .true.
/
This will read and write wrf single precision (r4) files, even if filter is built with double precision (r8).   This is useful if you need to run wrf in single precision, but you have enough per-node memory to to run filter double precision. 

Cheers,
Helen

---

Hi Jeff and Helen,

Thanks for the reply! I have tried the suggested options from Helen(distribute_mean = .true. || tasks_per_node = 30 || single_precision_output = .true.), it does not abort when using fewer cores, but is stuck in 'filter_assim' and not output any info... The end of log file is shown below, and the whole log and input.nml will be in attachment. (the input.nml and test.log is using main branch's filter. quantile_method branch's filter is also facing the same problem.)

 PE 0:  filter trace: After  computing prior observation values
 PE 0:  filter trace: Before observation space diagnostics
 PE 0:  filter trace: After  observation space diagnostics
 PE 0: filter: Ready to assimilate up to   16129 observations
 PE 0:  filter trace: Before observation assimilation
 Before observation assimilation TIME: 2023/07/18 11:36:07

\------------------------------------------------------------
Sender: LSF System <lsfadmin@c17n04>
Subject: Job 8906831: <test_main_eakf_allsky_cf0.001> in cluster <njucluster> Exited

and about the r4 in quantile_method branch,  i made a minor change to rh_distribution.f90 (just add a 1e-10_r8 to bounds, see code blocks below) and it works... for now I decide to continue my work with r4 precision, and after the problem in r8 is solved i will make some comparison to make sure whether r4 DART in quantile_method have problems or not.

```fortran
   if(bounded_below) then
      if(x(i) < lower_bound - 1e-10_r8) then
         write(errstring, *) 'x less than lower_bound ', i, x(i)
         call error_handler(E_ERR, 'inv_rh_cdf', errstring, source)
      endif
   endif
   
   if(bounded_above) then
      if(x(i) > upper_bound + 1e-10_r8) then
         write(errstring, *) 'x greater than upper_bound ', i, x(i)
         call error_handler(E_ERR, 'inv_rh_cdf', errstring, source)
      endif
   endif
```

Many thanks for your help~
Haoxing

---

Thanks for the update Haoxing!

The hanging, then exiting without an error is something we see when running out of memory.   I'm hoping we can reduce the memory footprint of filter in the not too distant future, so I may reach out to you for your high-res WRF for a test case if that is ok with you.

Cheers,
Helen
