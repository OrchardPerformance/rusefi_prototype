# Basic info dump for VVT 

Factors affecting the air flow and thus VVT position - 
RPM - 
Desired VE - 
Intake length - 
Exhaust length - 
Cam duration - 
Lift (but only during overlap) - 
Intake velocity - 


If a tuning method is used that has 2 tables, one of desired intake position and one of desired overlap then 
algorithm must always return the cams to the same point for a given position on that table. 

This may preclude having variables outside of the axis of the table. (i.e. load and RPM)
Thus only fixed characteristics should go into the math. 

If intake length, cam timing and intake CSA are required then this needs to be a lower sensitivity to these values so that
estimates are not seriously detrimental to the base line control.

Info available: 
Simulated M20 
ECU info from M50 
ECU info from M54 
Maybe ECU info from N52

Full throttle results from M20, positions for max VE:   
1000 = +10 deg inlet @108  
1500 = +10 @108  
2000 = between +10 @108 and +5 @103  
2500 = +5 @103 (but tiny loss for +10 @108)  
3000 = 0 @103   
3500 = -5 @103  
4000 = -10 @103 and -5 @103  
4500 = between -15 @103 and -10 @103  
5000 = Same ve for -15 @108, -10 @108, -5 @113  
5500 = between -10 @113 and -5 @113   
6000 = -5 @118  
6500 = -5 @118  
7000 = -10 @113 (0.2% loss for -5 @118)  
7500 = -10 @ 113 

Suggestion from this is that the overlap/centre line needs to open up heading towards peak power and maximum inlet retard around peak torque. 
Idle wants mid range inlet advance with reduced overlap.  
This would fit with the idea that more overlap is needed for larger airflows and more power. 
Inlet retard coincides with largest inlet manifold velocity vs engine RPM. 

Full throttle results from M20, possible losses for 1 degree of freedom:   
1000 = +10 can loose 6.4%, 108 can loose 6.2%ve (max 10.7% loss)  
1500 = +10 = 5.9%  @108 = 5.7% 
2000 = between +10 @108 and +5 @103  
2500 = +5 @103 (but tiny loss for +10 @108)  
3000 = 0 @103   
3500 = -5 @103  
4000 = -10 @103 and -5 @103  
4500 = between -15 @103 and -10 @103  
5000 = Same ve for -15 @108, -10 @108, -5 @113  
5500 = between -10 @113 and -5 @113   
6000 = -5 @118  
6500 = -5 @118  
7000 = -10 @113 (0.2% loss for -5 @118)  
7500 = -10 @ 113 


M20 simulated cam angles: Due to the way centre line affects both cams the advance value is not IVO position. Fixed IVO position as below. Method would set intake angle and then overlap.  
adv0CL93    = -15 intake  
adv0CL98    = -10 intake   
adv0CL103   = -5 intake   
adv0CL108   = Stock  
adv0CL113   = +5 intake  
adv0CL118   = +10 intake  
adv0CL123   = +15 intake  

adv5CL93    = -10 intake  
adv5CL98    = -5 intake  
adv5CL103   = Stock  
adv5CL108   = +5 intake  
adv5CL113   = +10 intake  
adv5CL118   = +15 intake  
adv5CL123   = +20 intake  

adv10CL93   = -5 intake  
adv10CL98   = Stock  
adv10CL103  = +5 intake  
adv10CL108  = +10 intake  
adv10CL113  = +15 intake  
adv10CL118  = +20 intake  
adv10CL123  = +25 intake    

adv15CL93   = Stock  
adv15CL98   = +5 intake  
adv15CL103  = +10 intake  
adv15CL108  = +15 intake  
adv15CL113  = +20 intake  
adv15CL118  = +25 intake  
adv15CL123  = +30 intake  

ret5CL93    = -20 intake  
ret5CL98    = -15 intake  
ret5CL103   = -10 intake   
ret5CL108   = -5 intake   
ret5CL113   = Stock  
ret5CL118   = +5 intake  
ret5CL123   = +10 intake  

ret10CL93   = -25 intake  
ret10CL98   = -20 intake  
ret10CL103  = -15 intake  
ret10CL108  = -10 intake  
ret10CL113  = -5 intake  
ret10CL118  = Stock  
ret10CL123  = +5 intake  

ret15CL93   = -30 intake  
ret15CL98   = -25 intake  
ret15CL103  = -20 intake  
ret15CL108  = -15 intake  
ret15CL113  = -10 intake  
ret15CL118  = -5 intake  
ret15CL123  = Stock  