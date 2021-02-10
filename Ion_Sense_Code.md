# Details of Ion processing method  

## Quick version  

- Look for the rise in voltage that indicates the initiation of the spark (look from the demanded spark timing) to get the actual moment the spark jumps the gap  
- Initially record the data from the moment of spark to an arbitrary value (maybe use the end of combustion detection?)  
- Look after spark initiation for the first time that the Ion signal goes below -1v and use that angle as the moment the combustion starts  
- Using a heavy (20 sample/2 degree) moving average value look for the angle that the ion trace goes above -0.3v for low ion signal and -1v for normal signals, this is the end of combustion  
- Using the Start of combustion angle and end of combustion angle, determine the length of the combustion cycle (this might be a good metric to log)  
- Depending on Low ion or normal signal window the Ion signal to look for the pressure peak. Low Ion needs the final 1/3rd of the combustion length and normal signal needs the last 1/2 of the combustion length, offset by ~3 degrees early for best results, maybe just expand window by 3 degrees each direction.  
- Record the angle and magnitude of the peak inside this window  
- Compare the Angle and magnitude to 5 combustion cycle rolling averages of the angle and magnitude  
- Discard the current cycle if: The peak is > 2x the magnitude of the previous peaks and is earlier than 5 degrees into the window (this catches the ringing for the most part)     
- Discard the current cycle if: The peak angle is more than +-5 degrees from the previous rolling average values (this catches misfires, early detection, failed detection and noise)  
- Add the current cycle to the rolling average and output the rolling average as the measured pressure peak angle for that cycle/log  
  


- one problem we may have is the detection of the low ion signal condition, this is generally when the magnitude of the pressure peak is below -2v, however its really hard to detect that before you know where the pressure peak is. Best solution I can think of is to set a TPS and RPM limit for the use of low signal mode. It seems to only be RPMs below ~2500 and BMEPs below 6 bar that suffer this.  
Other engines may be different so our milage may truly vary.  
  
- I think it would be good if we can keep a track of the number of discarded traces as a rolling number so we can look in logs to see if there are failures in the detection method. It would be good to also log the earliest and latest angle in the last X number of logs as a way to see if there is a lot of jitter in the detection, maybe this will give us an idea of the uncertainty of the logs? 

----------------------------------------------

## Full matlab code blocks below  

lowion= 0

-------------------------------------------------  
## Load structure  

ION1=double(data.(filesname{i}(1:end-4)).ION1.data);  ***% loads the ion data from the matlab structure***    
CrankAngle=double(data.(filesname{i}(1:end-4)).PCYL2.axis);  ***% loads the crank angle data from the matlab structure***  

----------------------------------------------------------------------
## auto detect spark angle from ion trace  
***Use the rise of >0.3v to detect the initiation of the spark, the actual initiation of the spark can be a little of to the commanded spark angle and there is some shift with load and rpm.***   
***% create array and record the index that ion trace exceeds -1 volt***  

isSpark = ION1 > 0.3;                        ***% check to see if ion trace goes over +0.3 volts, creates logical array***  
sparkpoint = zeros(1, columns);  ***%Creates an empty array***  
for col = 1 : columns;  ***% for loop that finds the first true value in the logical array and records index this occurs at***  
	sparkpoint(1, col) = find(isSpark(:, col), 1, 'first');  
end  
sparkpointCA= (sparkpoint-6000)/10;  ***% rectifies for matlab/recorded data offset***  
Spark = sparkpointCA(:,cycle);  ***% sets spark as first true index for the desired cycle***


---------------------------------------------------------------  
## Ion data set manipulation  

IONAVGtime = timeseries(IONAVG);  ***% time series of IONAVG just for simulink reasons***  
Ion_trim = getsamples(IONAVGtime,(SOCCell+2400:EOCAVGCF+2400));  ***% trims Ion to post spark to look for the first time the value goes below -1***  
Ion_axis_time = timeseries(IONAXIS);  
Ion_trim_axis = getsamples(Ion_axis_time,(SOCCell+2400:EOCAVGCF+2400));  

***% just some values for moving average I tried out, 7 was the best overall***  
IONMA = movmean(IONAVG,1);  
IONMA5=movmean(IONAVG,5);  
IONMA7=movmean(IONAVG,7);  
IONMA10=movmean(IONAVG,10);  
IONMA13=movmean(IONAVG,13);  
IONMA15=movmean(IONAVG,15);  
IONDIFF1 = diff(IONMA7);  
IONDIFF1MA = movmean(IONDIFF1,5);  
IONDIFF2 = diff(IONDIFF1MA);  
IONDIFF2MA = movmean(IONDIFF2,5);  

---------------------------------------------------------------------
## Ion trace windowing

ionindex = 6000+(Spark*10);         ***% determine when the spark happens to clip array***  
fromspark = ION1(ionindex:end, 1:columns);      ***% create ion data array from spark***  
isflame = fromspark < -1;        ***% check to see if ion trace goes below -1 volts***  
[rows, columns] = size(isflame);        ***% create array and record the index that ion trace exceeds -1 volt***  
ignitionpoint = zeros(1, columns);  
for col = 1 : columns;  
	ignitionpoint(1, col) = find(isflame(:, col), 1, 'first');  
end  

if lowion > 0  
    ***%repeat detection for last -0.3v value***  
IONENDMA = movmean(ION1, 20);  ***% 20 sample moving average to give a smooth trace to look for end of combustion***  
isflameMA = IONENDMA < -0.3;  
[rows, columns] = size(isflameMA);    ***% create array and record the index that ion trace exceeds -1 volt***  
endpoint = zeros(1, columns);  
for col = 1 : columns;  
	endpoint(1, col) = find(isflameMA(:, col), 1, 'last');  
end  
else  
***%repeat detection for last -1v value***  
IONENDMA = movmean(ION1, 20);  
isflameMA = IONENDMA < -1;  
[rows, columns] = size(isflameMA);     ***% create array and record the index that ion trace exceeds -1 volt***  
endpoint = zeros(1, columns);  
for col = 1 : columns;  
	endpoint(1, col) = find(isflameMA(:, col), 1, 'last');  
end  
end  

Combustionperiod = (endpoint-(ignitionpoint+ionindex));  
***% - note, auto low ion broken, need to fix.***  
***% lowionpoint = round((Combustionperiod/2),0);***  

***% for col = 1 : 300;***  
if lowion < 1 %fromspark(lowionpoint,:) < -2  
    lowion=0  
    Combustionduration = (Combustionperiod/20)-3;  
else  
    lowion=1  
    Combustionduration = (Combustionperiod/30)*2;  
end  
***% end***  

------------------------------------------------------------
## use fromspark array to perform pressure peak detection and add back index for the start of the array to the final crank angle values.

windowoffset=Combustionduration(:,cycle);    ***%offset from start of flame to begin pressure peak detection***  
windowstart=ignitionpoint+ionindex+(windowoffset*10);  
windowend=windowstart+250;  
PCYLpeakarray = PCYL2array(3500:4000,1:columns);    ***%clips cylinder pressure array to required range for peak analysis***  

IONpeakarray = zeros(251,300);  ***% create zero array for ION peak array***  
for IDX = 1:columns;     ***% for loop to select portion of the ion trace that matches the windowing for each cycle***  
IDXstart = windowstart(1,IDX);  
IDXend = windowend(1,IDX);  
IONpeakarray (:,IDX) = ION1(IDXstart:IDXend,IDX);  
end  
IONpeakMA = movmean(IONpeakarray,50);  

[PM,PI] = max(PCYLpeakarray);   ***%Find the max values and indices for the pressure and ion trace***  
[IM,II] = min(IONpeakMA);    ***%be sure to use min as ion is negative***  
PC = ((PI+3500)/10)-360;  
IC = zeros(1,columns);  ***% for loop to calculate the correct Ion peak crank angle based on the windowing offsets***  
for INDX = 1:columns;  
WINSTRT = windowstart(1,INDX);  
IIindx = II(1,INDX);  
IC(:,INDX) = ((IIindx+WINSTRT)/10)-(240+360);  
end  

----------------------------------------------------------------------  
## Filtering for poor quality ion traces  


 ***% detect peaks is less than 5 deg into the window and > 2x average of trace***    
TraceAVG = movmean(IM,5);  ***% Moving average Ion Max value to 5 samples***    
TraceTwoX = TraceAVG*2;  ***% Double moving average***   
IIAVG = movmean(II,5)  ***% Moving average of Ion Max Index for 5 samples***  
***% for loop to check if traces violate the following conditions***  
for IDX = 1:columns,  
if (IM(:,IDX) < TraceTwoX(:,IDX)) & II(:,IDX)<50; ***%NaN traces with peaks >2x the average AND peaking early that 5 degrees***  
    IC(:,IDX)=NaN;  
end  
if II(:,IDX)< (IIAVG(:,IDX)-50) | II(:,IDX)> (IIAVG(:,IDX)+50); ***%NaN traces with peaks more than 10 degrees of the average (unlikely to be real)***   
    IC(:,IDX)=NaN;  
end  
end  
 
-------------------------------------------------------------------------  
## Post Process  

PCMA = movmean(PC,100);  
ICMA = movmean(IC,100,'omitnan');  ***% discards the NaN values in the IC array*** 

MFB50_this_cycle = MFB50(cycle,:)  

PCavg = mean(PC)  
ICavg = mean(IC,'omitnan') ***%omitnan ignores NaN in IC array***  