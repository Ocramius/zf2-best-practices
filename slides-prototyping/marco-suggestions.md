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
 - start designing your module
    - please use composer to autoload it (no more `getAutoloaderConfig`)
 - start integrating your domain with the app
 
# designing a module

 - implement:
    - `ConfigProviderInterface`
    - `InitProviderInterface` (only for hacky modules)
    - `BootstrapListenerInterface`
       - only for modules with listeners
       - keep it thin, use the `ListenerAggregateInterface` instead!
    - nothing else in the Module!
    
 - config format:
    - config must be serializable
    - write a test for `Module#getConfig()`!
       - `unserialize(serialize($module->getConfig())) == $module->getConfig()`
    - anything referencing a class should use `::class`
    - no magic constants! Reference module class constants!
    
 - let's code a controller!
    - actually, let's skip on controllers until more substantial stuff is done
    - HTTP accessible page != working thing
    - stop thinking that a working http page is your aim. Your aim is a working domain!