If RPM is above hard limit, cut ignition and injection  
If MAP is above boost cut limit, cut injection  
# Can this also be supplemented by setting a max position for the wastegate control and in the case of ETB closing the throttle by say 20% over a second? This would have an effect of reducing the energy to the turbine and lowering the MAP pressure (vs the TIP pressure).

If the electronic throttle has a problem (TBD how to compute that), set a rev limit of ~1500-2500 rpm (TBD) and disable throttle control for all throttles  
# As discussed and suggested before, throttle operation can be limited in case of deviation problems to a configurable threshold (sey 30%) or in event of signal loss a fixed position or pulse width equivalent to fast idle can be set.

If a fatal error occurs, disable injection, ignition, electronic throttle, and trigger input (kill the engine).  
# This is a tricky one, in some cases this could be worse than the fatal error, for this to work out then we need to be sure that in all cases of fatal error shutting the engine down is safer than allowing the driver to damage the engine. I am put in mind of the F3 incident where power failure on a car lead to a serious collision with a faster car on a corner exit. 

Possible things to add in the future:  
In case of a sensor failure, change fueling mode to a fallback that doesn't use that sensor  
# Assuming this is the discussed TPS - VE transfer table. Difficulty comes with detecting the issue with the sensor, out of range is easy but deviation is harder. Producing the VE table is also tricky, maybe this is something were a script could auto gen it from a log? 
In case of engine overheat, lower rev limit, do rolling cylinder cut, possibly cut engine?  
# Rolling skip fire could be a good strategy for thermal limiting, proven to work well by cadillac. 
In case of low oil pressure, limit engine speed/throttle position?  
# Limit throttle as it lower pressure on the bearings, rpm may be needed in a case of a weak pump to keep the oil pressure up. However there is not much we can do about that one, your pretty SOL if its anything more than a bad pressure sender. 

-- If rpm rate of change is bad 5krpm to 0rpm in one rotation then cut fuel pump.  
we sort of already do this, the pump is on when there was a trigger event in the last XX period of time. Maybe that period should be shorter than the 1 second that it currently is.  
# Given there is some time to loose pressure from the rail does this not present the possibility of a noisy trigger signal causing a lean out and engine destruction? Thinking if the pump ended up pulsing on and off. 
# there is also the issue of determining what is the threshold for "bad" we either go so broad with the default that its in effective or have the user set a limit which could be a source of calibration issues. 
# What could be very useful is simply logging the events and turning a warning light on as it helps show an aging sensor or a bracket that is just a little too far from the trigger wheel. 


-- if unreasonably air temp then ???  
# If high air temp then retard ignition and enrich target AFR. 

-- if onstar then self destruct  
# Agreed.

Yes, most of them should get a bit, but a few won't. For example, I'm not interested in letting you disable the ETB protections. 
# Agreed, IMO we should be more aggressive with the requirement for dual signal. 

The way I handle that in my set up is the feature is always on, but simply set the "act if" point to a value that effectively disables it. The only thing I currently act on is low oil pressure and limit engine rpm and max throttle position (engine load is probably better) but to disable I just set the min oil pressure to 0
# That is more or less how the older systems are configured when hacking OEM ecus, you just set them to a range where they no longer cause an intervention. 

I have a malfunction light that has oil pres, engine temp, fuel pres at the moment but will likely add more like lamba error nd a few others.
# May I suggest a further intervention for hitting the duty cycle limit, and that is to lower the boost target by 50%. Also perhaps allow a minimum AFR while the boost target is above X, to prevent lean outs, if that occurs take the same steps as with duty cycle. 


Error handling is soooo critical.

I think that this splits into two pieces:

1. Define conditions that cause an error count
2. Define how to respond to a given error

The nice thing about this is that some existing functions (like a rev limiter) could be implemented by the same kind of logic. i.e. (if RPM > low limit) ign -= 20deg and if (RPM > hi limit) ign source = none or fuel source = none

On many race cars, doing everything to keep the engine running can be a design choice. i.e. mechanically self destruct before giving up
On most street cars, gracefully shutting things down before serious damage occurs typically is far more desirable.
Point: different use cases are going to need drastically different fault handling
# Yes and no, the fault triggering can be the same but the steps taken can differ, being able to turn off some error handling may be enough to do this or perhaps some choice of fault handling, i.e. a drop down box with "when xxx error" do nothing, shut down, run rich, etc. 
# I believe this would be relatively easy to include 

On a manual transmission car, losing a clutch switch or clutch position sensor might be worth trapping.
Onn an automatic transmission car, losing a gear shift selector input might be worth trapping.
Neither makes sense on the other car.

What I'm proposing basically is an analog to FSIO (with perhaps many of the same "inputs" ?) that outputs error states. FSERR? Almost the same code could be used, except instead of changing the state of IO, simply an internal "errorstate" could be changed.

What I'm proposing is that the behavior of the rest of the system have the error states available as inputs, just as the value of a sensor would be.
# This makes sense, rather than writing specific code for the result of each error we set some flags to indicate the type of error, then the various affected sub routines can have a check for those flags added directly within the function. This could allow quicker and easier reconfiguring of the errors and a gradual roll out of the system while limiting the scope of each PR.
# I think there would also be less chance of creating spaghetti code this way as I know from seeing other error handling it can easily turn into that. 

A couple examples of how others do it:

I was looking through the motec M1 software some years ago when I had one and was planning to use it, they have an error condition for every sensor I think as part of the setup that you can set or not set.

Enginelab that ended up installing is library function driven, with a GUI for each available function. All items have the ability to clamp the output, but the analog input function provides an error message if the clamp is enabled and exceeded and there is a predefined channel (every thing in the "program" is a channel, user set channels first then all the built in stuff like these error channels that can't be user edited). Any channel can read any other channel, so adding a sensor error to the MIL light is just "on if AN_xx Error not 0".

So in both ECUs this is baked into every at least sensor whether the user chooses to use it or not. As all aftermarket ECUs are sold in the US for "off-road use" its more about racing where a warning and tracking are desired over any kind of limp-home assuming the driver knows how to limp it home once notified if that is the correct action. Oil pressure can cause serious damaged pretty quickly so that is the one place I have the ECU take action on its own...light on at pressure x, engine limited at lower pressure y o if it drops too quick for the driver to notice and react the engine may be saved.

I know there is a way to save the error to flash and I want to figure that out at some point. There may also be a way to add an error output for any item exceeding the clam limit but those things are not spoken of in the user manual so I need to ask when I get to that point.


# Coolant level - depends on sensor location and type. Best bet is level sender in the expansion tank, a lot of RWD stuff can fit a BMW radiator and they have provisions for level sending. Some things have a head temperature, a deviation between a head temp sensor and the coolant sensor can be used to guess that there may be a coolant level issue and flag a warning. 
# Anything based on time is going to be just too slow to respond, it becomes a warning after you have broken down, error may as well say "hey pal, your f**ked".

# Something to consider is we will smart ways to detect a lot of these errors, short term fault and long term fault, short term is easy, just exceeded threshold X or Y, long term is harder, often the way they are handled in OEM is to use a counter with the deviation from expected adding cumulatively to the counter, a second subtractive value is used periodically to reduce the value, depending on the magnitude of the fault the counter will either increase or decrease, if the counter hits a threshold the error is triggered. (Though I know Matt knows this).

# If we are triggering error conditions is it possible to have the ECU fault light flash a blink code that matches the error? 
# Could we also broadcast the OBD PID on the CAN too? 
# If we are going to fault detect then lets use it to make out lives easy. 

# Cam sync is something that could be useful handling here, perhaps when cranking if say 100 good cycles have passed without achieving cam sync we could switch to batch injection and wasted spark? This needs a user configurable choice for what the wasted spark pairs are
# Similarly if there is a reliable cam signal while cranking and no life from the crank sensor do we want to fire up in batch mode from the cam signal alone? Again configurable for things like VVT where it would not be possible unless cam actuation was disabled and the timing retarded to avoid the inevitable jitter of the cams.

# EGT limiting would be good, see my comments about rolling the WBO2 into a lambda and EGT module on a board that fits inside an MRE case, simple unit for exhaust side sensing that can live in the engine bay and provide exhaust side info to the main ECU via CAN. This would allow us to take EGT into account. 