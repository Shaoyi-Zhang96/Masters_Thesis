# Masters_Thesis
Association between Leukotytic telomere length and infectious disease susceptibility

***Sample Demography Data;
libname a xport "/home/u45142492/sasuser.v94/WORK/Master Essay/1999-2000 Data/DEMO.XPT";
proc copy inlib=a outlib=work;
libname b xport "/home/u45142492/sasuser.v94/WORK/Master Essay/2001-2002 Data/DEMO_B.XPT";
proc copy inlib=b outlib=work;

***Hepatitis B Surface Antibody;
libname c xport "/home/u45142492/sasuser.v94/WORK/Master Essay/1999-2000 Data/LAB02.XPT";
proc copy inlib=c outlib=work;
libname d xport "/home/u45142492/sasuser.v94/WORK/Master Essay/2001-2002 Data/L02_B.XPT";
proc copy inlib=d outlib=work;

***Herpes Simplex Virus Type-1 & Type-2;
libname E xport "/home/u45142492/sasuser.v94/WORK/Master Essay/1999-2000 Data/LAB09.XPT";
proc copy inlib=E outlib=work;
libname F xport "/home/u45142492/sasuser.v94/WORK/Master Essay/2001-2002 Data/L09_B.XPT";
proc copy inlib=F outlib=work;

***Cytomegalovirus Antibodies - Serum (Surplus);
libname G xport "/home/u45142492/sasuser.v94/WORK/Master Essay/1999-2000 Data/SSCMV_A.XPT";
proc copy inlib=G outlib=work;
libname H xport "/home/u45142492/sasuser.v94/WORK/Master Essay/2001-2002 Data/SSCMV_B.XPT";
proc copy inlib=H outlib=work;

***HIV data;
libname I xport "/home/u45142492/sasuser.v94/WORK/Master Essay/1999-2000 Data/LAB03.XPT";
proc copy inlib=I outlib=work;
libname J xport "/home/u45142492/sasuser.v94/WORK/Master Essay/2001-2002 Data/L03_B.XPT";
proc copy inlib= J outlib=work;

***Telo Data;
libname K xport "/home/u45142492/sasuser.v94/WORK/Master Essay/1999-2000 Data/TELO_A.XPT";
proc copy inlib=K outlib=work;
libname L xport "/home/u45142492/sasuser.v94/WORK/Master Essay/2001-2002 Data/TELO_B.XPT";
proc copy inlib=L outlib=work;

***wholeblood percentage data;
libname M xport "/home/u45142492/sasuser.v94/WORK/Master Essay/1999-2000 Data/LAB25.XPT";
proc copy inlib=M outlib=work;
libname N xport "/home/u45142492/sasuser.v94/WORK/Master Essay/2001-2002 Data/L25_B.XPT";
proc copy inlib=N outlib=work;
run;

***BMI***;
libname O xport"/home/u45142492/sasuser.v94/WORK/Master Essay/1999-2000 Data/BMX.XPT";
proc copy inlib=O outlib=work;
libname P xport"/home/u45142492/sasuser.v94/WORK/Master Essay/2001-2002 Data/BMX_B.XPT";
proc copy inlib=P outlib=work;
run;

***display data;
data demo_a; set a.demo;
data demo_b; set b.demo_b;
data HEPAB_a; set c.lab02;
data HEPAB_b; set d.l02_b;
data HERPa; set E.lab09;
data HERPb; set F.l09_b;
data CYTOA; set G.sscmv_a;
data CYTOB; set H.sscmv_b;
data HIVA; set I.LAB03;
data HIVB; set J.L03_B;
data telo_a; set K.telo_a;
data telo_b; set L.telo_b;
data WB_A;set M.lab25;
data WB_B;set N.l25_b;
data BMI_A;SET O.BMX;
data BMI_B;SET P.BMX_B;
run;

data demo; set demo_a demo_b;
data HEPAB; set HEPAB_a HEPAB_b;
data HERP; set HERPA HERPB;
data CYTOV; set CYTOA CYTOB;
data HIV; set HIVA HIVB;
data telo; set telo_a telo_b;
data Wholeblood; set WB_A WB_B;
data BMI;set BMI_A BMI_B;
run;

data demo; set demo;
keep SEQN riagendr ridageyr ridreth1 dmdeduc indhhinc DMDHHSIZ;***GENDER, AGE, RACE, EDUCATION, INCOME Householdpeople;
run;

data hepab; set hepab;
keep SEQN LBDHBG;***HBV surface antigen;
run;

data herp; set herp;
keep SEQN LBXHE1 LBXHE2;
run;

data cytov; set cytov;
keep SEQN SSCMIGM;***Cytomegalovirus IgM;
run;

data HIV; set hiv;
keep SEQN LBDHI;***HIV antibody;
run;

data wholeblood;set wholeblood;
keep SEQN LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT;
run;

data telo; set telo;
keep SEQN telomean;
run;

data BMI;set BMI;
keep SEQN BMXBMI;
run;


*********;
data complete;
merge demo hepab herp cytov hiv telo wholeblood BMI;
run;

proc sort data=complete out=complete;
by seqn;
run;

data complete; set complete;
if telomean = . then delete;
if riagendr = . then delete;
if ridageyr = . then delete; 
if ridreth1 = . then delete;
if dmdeduc = . then delete;
if dmdeduc = 7 then delete;
if dmdeduc = 9 then delete;
if indhhinc = 77 then delete;
if indhhinc = . then delete;
if indhhinc = 99 then delete;
if LBXEOPCT = . then delete;
if LBXLYPCT = . then delete;
if LBXMOPCT = . then delete;
if LBXNEPCT = . then delete;
if LBXBAPCT = . then delete;
if DMDHHSIZ = . then delete;
if BMXBMI=. then delete;
run;

***reverse telomere length***;
data complete;
set complete;
telomean = telomean*(-1);
run; 

***Z transformation for telomean;
proc means data=complete;
var telomean;
run;

proc standard data=complete mean=0 std=1
out=complete;
var telomean;
run;



**Logistic regression*****************************************************************;

***raw;
data test11; set complete;
if LBDHBG =. then delete;
if LBDHBG = '3' THEN DELETE;
if lbdhbg = '1' then lbdhbg ='0';
if lbdhbg = '2' then lbdhbg = '1';
proc LOGISTIC data=test11;
model LBDHBG = TELOMEAN;
run;

data test22; set complete;
if LBXHE1 =. then delete;
if LBXHE1 ='3' then delete;
if LBXHE1 = '1' then LBXHE1 ='0';
if LBXHE1 = '2' then LBXHE1 = '1';
proc logistic data=test22;
model LBXHE1 = TELOMEAN;
run;

data test33; set complete;
if LBXHE2 =. then delete;
if LBXHE2 ='3' then delete;
if LBXHE2 = '1' then LBXHE2 ='0';
if LBXHE2 = '2' then LBXHE2 = '1';
proc logistic data=test33;
model LBXHE2 = TELOMEAN;
run;

data test44; set complete;
if SSCMIGM =. then delete;
if SSCMIGM ='3' then delete;
if SSCMIGM = '1' then SSCMIGM ='0';
if SSCMIGM = '2' then SSCMIGM = '1';
proc logistic data=test44;
model SSCMIGM = TELOMEAN;
run;

data test55; set complete;
if LBDHI =. then delete;
if LBDHI ='3' then delete;
if LBDHI = '1' then LBDHI ='0';
if LBDHI = '2' then LBDHI = '1';
proc logistic data=test55;
model LBDHI = TELOMEAN;
run;

***adjusted to blood type;

data test1111; set complete;
if LBDHBG =. then delete;
if LBDHBG = '3' THEN DELETE;
if lbdhbg = '1' then lbdhbg ='0';
if lbdhbg = '2' then lbdhbg = '1';
proc LOGISTIC data=test1111;
model LBDHBG = TELOMEAN LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT;
run;

data test2222; set complete;
if LBXHE1 =. then delete;
if LBXHE1 ='3' then delete;
if LBXHE1 = '1' then LBXHE1 ='0';
if LBXHE1 = '2' then LBXHE1 = '1';
proc logistic data=test2222;
model LBXHE1 = TELOMEAN LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT;
run;

data test3333; set complete;
if LBXHE2 =. then delete;
if LBXHE2 ='3' then delete;
if LBXHE2 = '1' then LBXHE2 ='0';
if LBXHE2 = '2' then LBXHE2 = '1';
proc logistic data=test3333;
model LBXHE2 = TELOMEAN LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT;
run;

data test4444; set complete;
if SSCMIGM =. then delete;
if SSCMIGM ='3' then delete;
if SSCMIGM = '1' then SSCMIGM ='0';
if SSCMIGM = '2' then SSCMIGM = '1';
proc logistic data=test4444;
model SSCMIGM = TELOMEAN LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT;
run;

data test5555; set complete;
if LBDHI =. then delete;
if LBDHI ='3' then delete;
if LBDHI = '1' then LBDHI ='0';
if LBDHI = '2' then LBDHI = '1';
proc logistic data=test5555;
model LBDHI = TELOMEAN LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT;
run;

***adjusted to covariates;

data test11111; set complete;
if LBDHBG =. then delete;
if LBDHBG = '3' THEN DELETE;
if lbdhbg = '1' then lbdhbg ='0';
if lbdhbg = '2' then lbdhbg = '1';
proc LOGISTIC data=test11111;
model LBDHBG = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ BMXBMI;
run;

data test22222; set complete;
if LBXHE1 =. then delete;
if LBXHE1 ='3' then delete;
if LBXHE1 = '1' then LBXHE1 ='0';
if LBXHE1 = '2' then LBXHE1 = '1';
proc logistic data=test22222;
model LBXHE1 = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ BMXBMI;
run;

data test33333; set complete;
if LBXHE2 =. then delete;
if LBXHE2 ='3' then delete;
if LBXHE2 = '1' then LBXHE2 ='0';
if LBXHE2 = '2' then LBXHE2 = '1';
proc logistic data=test33333;
model LBXHE2 = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ BMXBMI;
run;

data test44444; set complete;
if SSCMIGM =. then delete;
if SSCMIGM ='3' then delete;
if SSCMIGM = '1' then SSCMIGM ='0';
if SSCMIGM = '2' then SSCMIGM = '1';
proc logistic data=test44444;
model SSCMIGM = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ BMXBMI;
run;

data test55555; set complete;
if LBDHI =. then delete;
if LBDHI ='3' then delete;
if LBDHI = '1' then LBDHI ='0';
if LBDHI = '2' then LBDHI = '1';
proc logistic data=test55555;
model LBDHI = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ BMXBMI;
run;

***adjusted to all;
ods graphics on;
data test111; set complete;
if LBDHBG =. then delete;
if LBDHBG = '3' THEN DELETE;
if lbdhbg = '1' then lbdhbg ='0';
if lbdhbg = '2' then lbdhbg = '1';
proc LOGISTIC data=test111;
model LBDHBG = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT BMXBMI;
run;

data test222; set complete;
if LBXHE1 =. then delete;
if LBXHE1 ='3' then delete;
if LBXHE1 = '1' then LBXHE1 ='0';
if LBXHE1 = '2' then LBXHE1 = '1';
proc logistic data=test222;
model LBXHE1 = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT BMXBMI;
run;

data test333; set complete;
if LBXHE2 =. then delete;
if LBXHE2 ='3' then delete;
if LBXHE2 = '1' then LBXHE2 ='0';
if LBXHE2 = '2' then LBXHE2 = '1';
proc logistic data=test333;
model LBXHE2 = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT BMXBMI;
run;

data test444; set complete;
if SSCMIGM =. then delete;
if SSCMIGM ='3' then delete;
if SSCMIGM = '1' then SSCMIGM ='0';
if SSCMIGM = '2' then SSCMIGM = '1';
proc logistic data=test444;
model SSCMIGM = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT BMXBMI;
run;

data test555; set complete;
if LBDHI =. then delete;
if LBDHI ='3' then delete;
if LBDHI = '1' then LBDHI ='0';
if LBDHI = '2' then LBDHI = '1';
proc logistic data=test555;
model LBDHI = TELOMEAN RIAGENDR RIDAGEYR RIDRETH1 INDHHINC DMDHHSIZ LBXEOPCT LBXLYPCT LBXMOPCT LBXNEPCT LBXBAPCT BMXBMI; 
run;
 ods graphics off;
