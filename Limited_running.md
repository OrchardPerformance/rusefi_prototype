If RPM is above hard limit, cut ignition and injection  
If MAP is above boost cut limit, cut injection  
If the electronic throttle has a problem (TBD how to compute that), set a rev limit of ~1500-2500 rpm (TBD) and disable throttle control for all throttles  
If a fatal error occurs, disable injection, ignition, electronic throttle, and trigger input (kill the engine).  

Possible things to add in the future:  
In case of a sensor failure, change fueling mode to a fallback that doesn't use that sensor  
In case of engine overheat, lower rev limit, do rolling cylinder cut, possibly cut engine?  
In case of low oil pressure, limit engine speed/throttle position?  

-- If rpm rate of change is bad 5krpm to 0rpm in one rotation then cut fuel pump.  
we sort of already do this, the pump is on when there was a trigger event in the last XX period of time. Maybe that period should be shorter than the 1 second that it currently is.  
-- if over temp then limit hp or do something to protect the engine  
-- if speed density sensor failure, then switch to alpha-n or average two fuel algos or something like that.  
-- if unreasonably air temp then ???  
-- if any sensor fail then ???  
-- if onstar then self destruct  

Yes, most of them should get a bit, but a few won't. For example, I'm not interested in letting you disable the ETB protections.  

The way I handle that in my set up is the feature is always on, but simply set the "act if" point to a value that effectively disables it. The only thing I currently act on is low oil pressure and limit engine rpm and max throttle position (engine load is probably better) but to disable I just set the min oil pressure to 0

I have a malfunction light that has oil pres, engine temp, fuel pres at the moment but will likely add more like lamba error nd a few others.


I think my asks are going to be more of a 2.0 than a 1.0 but...

Error handling is soooo critical.

I think that this splits into two pieces:

1. Define conditions that cause an error count
2. Define how to respond to a given error

The nice thing about this is that some existing functions (like a rev limiter) could be implemented by the same kind of logic. i.e. (if RPM > low limit) ign -= 20deg and if (RPM > hi limit) ign source = none or fuel source = none

On many race cars, doing everything to keep the engine running can be a design choice. i.e. mechanically self destruct before giving up
On most street cars, gracefully shutting things down before serious damage occurs typically is far more desirable.
Point: different use cases are going to need drastically different fault handling

On a manual transmission car, losing a clutch switch or clutch position sensor might be worth trapping.
Onn an automatic transmission car, losing a gear shift selector input might be worth trapping.
Neither makes sense on the other car.

What I'm proposing basically is an analog to FSIO (with perhaps many of the same "inputs" ?) that outputs error states. FSERR? Almost the same code could be used, except instead of changing the state of IO, simply an internal "errorstate" could be changed.

What I'm proposing is that the behavior of the rest of the system have the error states available as inputs, just as the value of a sensor would be.

A couple examples of how others do it:

I was looking through the motec M1 software some years ago when I had one and was planning to use it, they have an error condition for every sensor I think as part of the setup that you can set or not set.

Enginelab that ended up installing is library function driven, with a GUI for each available function. All items have the ability to clamp the output, but the analog input function provides an error message if the clamp is enabled and exceeded and there is a predefined channel (every thing in the "program" is a channel, user set channels first then all the built in stuff like these error channels that can't be user edited). Any channel can read any other channel, so adding a sensor error to the MIL light is just "on if AN_xx Error not 0".

So in both ECUs this is baked into every at least sensor whether the user chooses to use it or not. As all aftermarket ECUs are sold in the US for "off-road use" its more about racing where a warning and tracking are desired over any kind of limp-home assuming the driver knows how to limp it home once notified if that is the correct action. Oil pressure can cause serious damaged pretty quickly so that is the one place I have the ECU take action on its own...light on at pressure x, engine limited at lower pressure y o if it drops too quick for the driver to notice and react the engine may be saved.

I know there is a way to save the error to flash and I want to figure that out at some point. There may also be a way to add an error output for any item exceeding the clam limit but those things are not spoken of in the user manual so I need to ask when I get to that point.



- Coolant level - depends on sensor location and type. Best bet is level sender in the expansion tank, a lot of RWD stuff can fit a BMW radiator and they have provisions for level sending. Some things have a head temperature, a deviation between a head temp sensor and the coolant sensor can be used to guess that there may be a coolant level issue and flag a warning. 

