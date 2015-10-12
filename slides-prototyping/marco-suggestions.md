# where to start

 - do you know where to start?
 - did you discuss anything with the team?
 - design domain models first
 - import your testing framework
 - import zf2 later
 
# designing the domain

 - mostly suggestions by @mathiasverraes
 
# splitting the domain?

 - split only if you can deploy it separately
 - otherwise, keep it together (you are just adding complexity)

# importing zf2

 - skeleton application (as usual)
 - remove all the useless bits
    - custom zf2 autoloading
    - all the paths that are unused
    - keep the `Application` module (the error handling stuff is useful!)
 - start designing 
 - start integrating your domain with the app
 