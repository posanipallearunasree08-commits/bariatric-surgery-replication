# bariatric-surgery-replication
Replication study of postoperative complications in bariatric surgery using ACS NSQIP (2014). Includes data cleaning, variable creation, statistical analysis, multivariable regression models, and visualizations (graphs &amp; charts) in SAS.
# Replication Study: Postoperative Complications in Bariatric Surgery

**Author:** Dr. Aruna Sree, BDS, MPH (Biostatistics)  
**Affiliation:** University of Nebraska Medical Center  
**Capstone Project Date:** April 2024  

## 📌 Project Overview
This project replicates and extends the study:  
Sanni, A., Perez, S., Medbery, R. et al. (2014). *Postoperative complications in bariatric surgery using age and BMI stratification*. Surg Endosc, 28, 3302–3309. https://doi.org/10.1007/s00464-014-3606-7  

The original study investigated the association between **age, BMI, and postoperative complications** using the **ACS NSQIP dataset (2014)**.  

This replication enhances the original analysis by including:  
- **Data Cleaning & Preparation** – addressed missing values, standardized variables, and ensured dataset quality.  
- **New Variable Creation** – derived variables for stratification and regression modeling.  
- **Statistical Analysis** – univariate and multivariable regression models to assess risk factors.  
- **Pure Replication** – reproduced the original study’s results for validation.  
- **Measurement & Estimation Analysis (MEA)** – applied alternative methods (e.g., nonparametric tests).  
- **Theory of Change Analysis (TCA)** – evaluated the relationship between **diabetes status and wound complications**, adjusting for age and BMI.  
- **Visualizations** – generated graphs, charts, and tables to communicate findings clearly.  

## 🛠 Methods
- **Software:** SAS  
- **Techniques:** Data cleaning, feature engineering, regression analysis, multivariable modeling, stratified analysis  
- **Statistical Procedures:** Logistic regression, Chi-Square tests, multivariable models, nonparametric tests  
- **Visualization:** Charts, plots, and tables created using SAS  
- **Data Source:** ACS NSQIP (2014)  

## 📊 Results (Summary)
- Confirmed significant associations between **age, BMI, and postoperative complications**.  
- Multivariable regression models provided deeper insights into independent risk factors.  
- TCA analysis highlighted the role of **diabetes status in wound complications**.  
- Visualizations (graphs & charts) enhanced interpretability of the findings.

## Code 
# Replication Study: Postoperative Complications in Bariatric Surgery
/*SAS Code*/ 
/*Dataset*/ 
LIBNAME mylib '/home/u61325000/sasuser.v94'; 
data capstone; 
set mylib.sanni; 
if age ='90+' then age2=90; 
else age2=10*age/10; 
if DIABETES in ("INSULIN", "NON-INSULIN") then 
        diabetes4 = "Yes"; 
    else if DIABETES = "NO" then 
        diabetes4 = "No"; 
 
    if CPT= '43644' then Procedure='LGBP'; 
    else if CPT= '43770' then Procedure='LAGB'; 
    else if CPT= '43775' then Procedure='LSG'; 
    if  CPT= '43644' then Procedure2='2'; 
34 
else if CPT= '43770' then Procedure2='0'; 
else if CPT= '43775' then Procedure2='1'; 
if DOpertoD = -99 then mortality=0; 
else mortality = 1; 
if noupneumo = "1" or reintub = "1" or pulembol="1" or failwean="1" or renainsf="1" 
or urninfec="1" or cdmi="1" or cdarrest="1" or nothbleed="1" or RENAFAIL="1" or 
NOTHDVT="1" or neurodef="1" or dcnscoma="1" or cnscva="1" or mortality = "1" 
then complication=1; 
else complication=0; 
if ASACLAS = 'None assigned' then ASACLAS=.; 
if ASACLAS = "1-No Disturb" or ASACLAS = "2-Mild Disturb" then NewClass = 0; 
else if ASACLAS = "3-Severe Disturb" or ASACLAS = "4-Life Threat" then NewClass 
= 1; 
if hxcopd ne "NULL"; 
if dyspnea = "No" then dyspnea2=0; 
else dyspnea2 = 1; 
if hypermed ne "NULL"; 
if diabetes = "NO" then diabetes3=0; 
else diabetes3=1; 
run; 
/* Proc contents */      
proc contents data=capstone; 
run; 
PROC PRINT DATA=CAPSTONE (OBS=50); 
RUN; 
/*Measurement and Estimation Analysis*/ 
/*Doubt table 1 : age bmi*/ 
/*table 1: sex race inpatient asa*/ 
/*table-1 age*/ 
/*Original Article*/ 
proc means data=capstone; 
class procedure; 
var age2; 
run; 
proc freq data=capstone; 
tables sex*procedure asaclas*procedure inout*procedure race_new*procedure; 
run; 
/*Alternate Method*/ 
proc glm data=capstone plots(MAXPOINTS=20000); /* Increase the cutoff to 20000 */ 
class procedure; 
model Age2 = procedure; 
means procedure / Tukey;   
title 'Analysis of Age by Bariatric Procedure';    
run; 
35 
 
/*for Boxplot*/ 
ods rtf file="/home/u61325000/capstone.rtf4"; 
proc sgplot data=capstone; 
    vbox Age2 / category=procedure  
               groupdisplay=cluster  
               lineattrs=(color=blue) 
               fillattrs=(color=lightbrown); 
               title  'Analysis of Age by Bariatric Procedure'; 
run; 
ods rtf close; 
 
/*table 1 bmi*/ 
/*Orginal Article*/ 
proc means data=capstone; 
class procedure; 
var bmi; 
run; 
/*Alternative Method*/ 
proc glm data=capstone; 
    class procedure; 
    model bmi = procedure; 
    means procedure / tukey; /* Tukey's post hoc test for pairwise comparisons */ 
   title  'Analysis of Age by Bariatric Procedure';
   run; 
/*For Boxplot*/ 
ODS RTF FILE="/home/u61325000/Caps.rtf"; 
proc sgplot data=capstone; 
    vbox BMI / category=procedure  
               groupdisplay=cluster  
               lineattrs=(color=blACK) 
               fillattrs=(color=PINK); 
               title  'Analysis of BMI by Bariatric Procedure'; 
run; 
ODS RTF CLOSE; 
 
/*table 2 */ 
/*No Morbidity Variable*/ 
/*mortality*/ 
data updated; 
set capstone; /* Replace your_dataset with your actual dataset name */ 
if YRDEATH in (2010, 2011) then YRDEATH_GROUPED = "2010-2011"; 
else if YRDEATH = -99 then YRDEATH_GROUPED = "-99"; 
else YRDEATH_GROUPED = "Other"; /* Adjust based on how you want to handle other 
years */ 
run; 
proc freq data=updated; 
tables YRDEATH_GROUPED*procedure / chisq  missing; 
title "Frequency of YRDEATH_GROUPED by CPT"; 
run; 
/*reoperation*/ 
36 
 
proc freq data=capstone; 
table returnor*procedure/chisq; 
run; 
/*OPtime*/ 
/*Original Data Method*/ 
PROC MEANS DATA=capstone mean min std ; 
    CLASS procedure; 
    VAR Optime; 
    RUN; 
/*Alternative Method*/   
proc npar1way data=capstone;  
 class procedure;  
 var optime;  
 run;   
proc univariate data=capstone; 
   class Procedure; 
   var optime; 
 run; 
/*Length of stay*/ 
/*Original Data Methods*/ 
proc univariate data=capstone nextrobs=0; 
 class procedure; 
   var tothlos; 
   output out=location 
          mean=Mean mode=Mode median=Median 
          q1=Q1 q3=Q3 p5=P5 p10=P10 p90=P90 p95=P95 
          max=Max; 
run; 
/*Alternative Method*/ 
proc npar1way data=capstone;  
 class procedure;  
 var tothlos;  
 run;   
proc print data=location noobs; 
run; 
proc univariate data=capstone; 
   class Procedure; 
   var tothlos; 
 run; 
  
 
/*table 3*/ 
PROC FREQ DATA=capstone; 
    TABLES noupneumo*procedure ReIntub*procedure Pulembol*procedure 
           FailWean*procedure  RenaInsf*procedure 
           Urninfec*procedure   
           nothdvt*procedure /chisq; 
             RUN; 
proc freq data=capstone; 
37 
tables cdmi*procedure nothbleed*procedure Cdarrest*procedure neurodef*procedure 
dcnscoma*procedure noprenafl*procedure cnscva*procedure/fisher; 
run; 
/*table 4*/ 
/*wound complications*/ 
/*superficial*/ 
proc freq data=capstone; 
tables supinfec*procedure/chisq; 
run; 
/*Deep Space Infection*/ 
proc freq data=capstone; 
table wndinfd*procedure/chisq; 
run; 
/*Organ space site infection*/ 
proc freq data=capstone; 
table ORGSPCSSI*procedure/chisq; 
run; 
/*wound dehiscence*/ 
/*As sample size is small used Fisher's exact test*/ 
proc freq data=capstone; 
tables dehis*procedure/fisher; 
run; 
/*Figure 1*/ 
/*Multiregression Model*/ 
proc logistic data=capstone;  
class procedure2 (param=ref ref='0'); 
class ASAClass (param=ref ref='0'); 
class hxcopd (param=ref ref="No"); 
class dyspnea2 (param=ref ref='0'); 
class hypermed (param=ref ref="No"); 
class diabetes3 (param=ref ref='0'); 
class mortality (param=ref ref='0'); 
/*class wound (param=ref ref='0')*/; 
model complication(event="1") =procedure2 age2 bmi ASAClass hxcopd dyspnea2 
hypermed diabetes3  
/clodds=pl; 
TITLE "LOGISTIC MODEL RESULTS"; 
run; 
/*Theory of Change Analysis*/ 
/*Diabetes is categorized as Insulin, Non-Insulin, and No-Diabetic*/ 
/*1.Association between wound complications and diabetes*/ 
proc logistic data=CAPSTONE DESC; 
class  DIABetes (param=ref ref="NO"); 
38 
 
  model supinfec = DIABetes  ; 
  run; 
  proc logistic data=CAPSTONE DESC; 
  class  DIABetes (param=ref ref="NO") ; 
  model wndinfd = DIABetes  ; 
  run; 
  proc logistic data=CAPSTONE DESC; 
  class  DIABetes (param=ref ref="NO"); 
  model ORGSPCSSI = DIABetes ; 
  run; 
  proc logistic data=CAPSTONE DESC; 
  class  DIABetes ( param=ref ref="NO"); 
  model dehis = DIABetes ; 
  run; 
/*Freqency tables for Wound complications*/ 
 /* Superficial Site Infection*/ 
  proc freq data=capstone; 
  tables supinfec*diabetes/chisq; 
  run; 
 /*Deep site Infection*/ 
 proc freq data=capstone; 
  tables wndinfd*diabetes/fisher; 
  run; 
/*Organ Space Site Infection*/ 
proc freq data=capstone; 
tables orgspcssi*diabetes/chisq; 
run; 
/*Wound dehiscence*/ 
proc freq data=capstone; 
tables dehis*diabetes/fisher; 
run; 
 
 /*2.Multiregression Logistic MOdel using Age, BMI, and Diabetes for Wound 
Complications*/ 
  /* Backward selection for supinfec */ 
proc logistic data=CAPSTONE; 
    class DIABetes4 (param=ref ref="No"); 
    model supinfec = BMI DIABETES4 AGE2 / selection=backward slstay=0.05; 
    run; 
 
/* Backward selection for wndinfd */ 
proc logistic data=CAPSTONE; 
    class DIABetes4 (param=ref ref="No"); 
    model wndinfd = BMI DIABETES4 AGE2 / selection=backward slstay=0.05;
     run; 
 
/* Backward selection for ORGSPCSSI */ 
proc logistic data=CAPSTONE; 
    class DIABetes4 (param=ref ref="No"); 
    model ORGSPCSSI = BMI DIABETES4 AGE2 / selection=backward slstay=0.05; 
run; 
39 
/* Backward selection for dehis */ 
proc logistic data=CAPSTONE; 
class DIABetes4 (param=ref ref="No"); 
model dehis = BMI DIABETES4 AGE2 / selection=backward slstay=0.05;
run;
    
