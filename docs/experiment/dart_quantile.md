---
title: DART_QCEFF main log
date: 2023-07-16 14:29:59
password: qceff
categories:
- log
tags: 
- log
- algorithm
---
<!-- more -->
\bibliography

# DART_QCEFF experiment

## introduction/proposal

对于observed variable, 如果单纯考虑filter是否quantile conserving()

对于unobserved variable quantile conserving filter，……，需要注意的是，quantile conserving 并不意味着quantile of x^prior and x^post are the same, it just ensure quantile conserving in observation space increment part.


about gaussian anamorphose, see here[@Wu_2018]
## DART_QCEFF experiment design

**3 filters**: main_eakf, main_rhf, quantile_rhf

**2 kind of observation**: allsky, cloudysky

d01_7.5km: 0030, 0100, 2 sets.

d02_1.5km: 0030-0100, 10min inverval, 4 sets.

d03_300m: 0030-0100, 5min inverval, 7 sets. **Together 13 sets**

The output/data directory should named as $DIR=**f"{expt}\_{domain}\_{time}\_{obs}"**.

!!! note

    关于{obs}: 我在d01 0030 尝试了allsky and cloudy两种observation。尝试cloudy obs的原因是cloudy area的prior dist有更明显的non-gaussian (Larger KLD)，而在cloudy obs影响的区域中，这种non-gaussian区域的占比显然更大。后续不一定会继续用。

!!! 

## 一些推导

In probability theory and statistics, the probit function is the quantile function associated with the standard normal distribution. (from [Wiki](https://en.wikipedia.org/wiki/Probit#:~:text=In%20probability%20theory%20and%20statistics,modeling%20of%20binary%20response%20variables.)) So, what we do is (to some extent) like a gaussian anamorphose(高斯失真)? Not the same! gaussian anamorphose transform state ensemble to a new variable which obey the gaussian distribution, but what we do is just transform the state ensemble to the probit space, and **keep quantile the same**, so the transformed variable is not necessarily obey the gaussian, unless the quantiles obey the uniform(0,1) dist, which is the case for rank histogram. (a quenstion: why not just do this in quantile space? In this case, we are doing next things in uniform space. why need to do an extra transform to probit space?)

### normal(gaussian) prior is no need to do a probit transform

why? 

```fortran
subroutine to_probit_normal(ens_size, state_ens, p, probit_ens, use_input_p)

integer, intent(in)                  :: ens_size
real(r8), intent(in)                 :: state_ens(ens_size)
type(dist_param_type), intent(inout) :: p
real(r8), intent(out)                :: probit_ens(ens_size)
logical, intent(in)                  :: use_input_p

! Don't need to do anything for normal
probit_ens = state_ens

end subroutine to_probit_normal
```

## difference between main and quantile_method branch

see the difference [at github](https://github.com/NCAR/DART/compare/main...quantile_methods)

assimilation_code/modules/assimilation/assim_tools_mod.f90
assimilation_code/modules/assimilation/filter_mod.f90
assimilation_code/modules/assimilation/algorithm_info_mod.f90
assimilation_code/modules/assimilation/probit_transform_mod.f90

### what i have changed?

just algorithm_info_mod.f90, and a minor change in rh_distribution.f90

## 如何重复quantile-conserving filter 的实验？

bashrc in lililei4: dart_rttov

quantile version of DART: /share/home/lililei4/haoxing/DART_quantile

main version of DART: /share/home/lililei4/haoxing/DART_main

### Raw data在哪里？

在lililei4上，raw data全部在 ~/haoxing/quantile_data/ 中。llleistu200上同样有备份。

var_cut*: 关于domain格点的lat/lon信息，用于确定OSSE观测的位置。

$DIR/: 主要的raw data。关于OSSE obs/FG/POSTERIOR都放在这里。里面的README记录了$DIR文件夹里内容的基本信息。

!!! tip

    $DIR 的定义如下：**f"{domain}_{time}"**  一个$DIR里至少应该有: 
    
    FG -> firstguess dir
    
    obs_{domain}_ch1025_totalline_withpert: text OBSERVATION
    
    obs_seq.out_{domain}_{time}: DART OBSERVATION
    
    prior_BT: external FO data (gengerated by RTTOVv11.2)

diagfile/: 把data从text转成diag再转成DART observation_converter需要的text......see more in README。

scripts/: 一些用于处理observation的脚本，以及一个基本的matlab画图脚本for d01(7.5km)

#### from linfan, raw data在哪里？

Ens：/ldata3/llleistu/linfan/Ensemble/

**d01_7.5km/ldata3/llleistu/linfan/Ensemble/d01_7.5km/ens_20210608/rst0400_50mem_040000_040230/1-50 是1-50个member 不同时刻，如果只要几个时刻，在上层文件夹里，有04_00_30 04_01_00 04_01_30 。。。**

d02_1.5km/2rst0400_50mem_1.5km/1-50

d03_300m/rst0400_50mem_300m/1-50

**/share/home/lililei1/lfzhou/hyperspectral_da/step1_obs_ensBT/step2_les_obs/4obsdir_d01 -d04 有不同分辨率的obs hx，加了withpert就是添加了扰动的**

/share/home/lililei1/lfzhou/scripts/bt_vars_corr/1nonlinear_criterion/d01/Ca_judgment: 这里有云检测的东西。

### Create DART obs_seq.out

see here: /share/home/lililei4/haoxing/DART_quantile/observations/obs_converters/text_giirs/

制作符合DART_RTTOV要求的观测并且使用external FO（也就是说在外面调用RTTOV，直接把RTTOV输出的BT值写进obs_seq.out，并且让DART_RTTOV不要再在filter里面调用FO来算prior，直接使用obs_seq.out里面我们给定的值作为prior）会有点麻烦。

!!! note

    haoxing: create_3d_obs will set obs_def for this obs, but we want more, like(has_external_FO) so better do it ourselves.
    
    haoxing: need to tell obs_def we use external FO. refer to GSI2DART convertor.
    
    具体这个text_to_obs是如何运作的，请参见text_giirs/text_to_obs.f90

!!! warning

    新定义了一个observation type:  FY4_1GIIRS_TB
    
    在observation/forward_operator/obs_def_rtttov_mod.f90 中可以看到
    
    FY4_1_GIIRS_TB,               QTY_BRIGHTNESS_TEMPERATURE
    
    在迁移到别的DART version时记得需要修改obs_def

#### How to use external RTTOV as FO?

主要参见两个script，在/share/home/lililei4/haoxing/quantile_data/scripts

driver_matlab_prof.sh: 从wrfout生成profile data。需要wrfout中有给定的变量。

!!! warning
    需要在DART input.nml &model_nml中列出所有driver_matlab_prof.sh需要的变量，因为posterior根据wrf_state_variables输出并存储变量（如果不使用obs_impact来约束的话，所有相关的variable都会被观测更新）。

一个范例：
```fortran
&model_nml
default_state_variables = .false.,
wrf_state_variables     =      'U','QTY_U_WIND_COMPONENT','TYPE_U','UPDATE','999',
                                'V','QTY_V_WIND_COMPONENT','TYPE_V','UPDATE','999',
                                'U10','QTY_10M_U_WIND_COMPONENT','TYPE_U10','UPDATE','999',
                                'V10','QTY_10M_V_WIND_COMPONENT','TYPE_V10','UPDATE','999',
                                'W','QTY_VERTICAL_VELOCITY','TYPE_W','UPDATE','999',
                                'T','QTY_POTENTIAL_TEMPERATURE','TYPE_T','UPDATE','999',
                                'T2','QTY_2M_TEMPERATURE','TYPE_T2','UPDATE','999',
                                'TH2','QTY_POTENTIAL_TEMPERATURE','TYPE_TH2','UPDATE','999',
                                'TSK','QTY_SKIN_TEMPERATURE','TYPE_TSK','UPDATE','999',
                                'P','QTY_PRESSURE','TYPE_P','UPDATE','999',
                                'PB','QTY_PRESSURE','TYPE_PB','UPDATE','999',
                                'PH','QTY_GEOPOTENTIAL_HEIGHT','TYPE_GZ','UPDATE','999',
                                'MU','QTY_PRESSURE','TYPE_MU','UPDATE','999',
                                'PSFC','QTY_SURFACE_PRESSURE','TYPE_PS','UPDATE','999',
                                'QVAPOR','QTY_VAPOR_MIXING_RATIO','TYPE_QV','UPDATE','999',
                                'QCLOUD','QTY_CLOUDWATER_MIXING_RATIO','TYPE_QC','UPDATE','999',
                                'QRAIN','QTY_RAINWATER_MIXING_RATIO','TYPE_QR','UPDATE','999',
                                'QSNOW','QTY_SNOW_MIXING_RATIO','TYPE_QS','UPDATE','999',
                                'QICE','QTY_ICE_MIXING_RATIO','TYPE_QI','UPDATE','999',
                                'QGRAUP','QTY_GRAUPEL_MIXING_RATIO','TYPE_QG','UPDATE','999',
                                'Q2','QTY_2M_SPECIFIC_HUMIDITY','TYPE_Q2','UPDATE','999',
                                'CLDFRA','QTY_CLOUD_FRACTION','TYPE_CFRA','UPDATE','999',
```


driver_rttov.sh: 用于drive RTTOV，根据matlab生成的prof data来计算BT。

#### merged_FO_data是什么？

```fortran
&text_to_obs_nml
    text_input_file = '../data/giirs_BT'
    FO_input_file = '../data/merged_FO_data.txt'
    obs_out_file    = 'obs_seq.out_allsky'
    is_prior = .true.
    debug           = .true.
  /
```

在text_to_obs的nml中我添加了一项FO_input_file，这是用来读取上述external FO data的。RTTOV输出了$ENS_SIZE的FO data file，为了读取方便需要把他们merge到一个file中，再指定FO_input_file，从而把data读取到obs_seq.out的external_FO 定义中。

```fortran
! haoxing : open input ensemble FO file
iFOunit = open_file(FO_input_file, 'formatted', 'read')
if (debug) print *, 'opened input file ' // trim(FO_input_file)

read(iFOunit, "(A)", iostat=rcio) FO_input_line
read(FO_input_line, *, iostat=rcio) FO_copy

if (is_prior) then 
    call set_obs_def_external_FO(obs_def, has_external, write_external,key,ens_size,real(FO_copy,r8)) ! CSS sets obs_def%has_exteral_FO 
endif
```

关于如何merge成一个file，参见/scripts/merge_FO_in_one_file.py

