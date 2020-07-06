
// Use this macro to declare a function which only takes magic references
#define DECLARE_ENGINE_PARAMETER_SIGNATURE void
// Use this version of the macro as the suffix if method has other parameters
#define DECLARE_ENGINE_PARAMETER_SUFFIX
// Pass this if only magic references are needed
#define PASS_ENGINE_PARAMETER_SIGNATURE
// Pass this after some other parameters are passed
#define PASS_ENGINE_PARAMETER_SUFFIX

things like the global engine object are globals in the firmware, but not in unit tests, so that we can pass one in (different) for each test
so in the firmware, those defines do nothing, but in the unit tests, they pass in a few args
So the TLDR is it serves no purpose for running the engine, its simply a test mechanism. 


