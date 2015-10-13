# what is this talk about?

 - not about common mistakes (we have enough talks about that)
 - more overview than in-depth code (you have enough practical refactoring/SOLID/etc talks out there)

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
    - service definitions should use `::class` rather than custom strings
    - no magic constants! Reference module class constants!
    
 - let's code a controller!
    - actually, let's skip on controllers until more substantial stuff is done
    - HTTP accessible page != working thing
    - stop thinking that a working http page is your aim. Your aim is a working domain!
    
 - let's code the service layer!
    - actually, let's first make sure our domain is working
    
 - where do we keep our domain logic?
    - separate module? No.
    - so where?
 
 - clarifying the module system:
    - modules are designed for reuse
    - modules for ACL, error handling, mailers, etc... these are reusable
    - reusable modules are "infrastructure" modules
    - your app code (and your domain) are not reusable
    - a single app-specific module will usually suffice
 
 - now, after you implemented and tested your domain...
 
# implementing the application logic

 - decide what web interface you want: Web-pages? API?
    - if it's an API, do you REALLY need an API?
       - API
    - we will go web-first for our case

 - design your controllers after the domain endpoints, and not the opposite
    - if you have control over the project:
       - don't build web pages that need new domain logic
       - instead, build new domain logic that needs web pages
       - then build the frontend for it
       - userland interactions should have corresponding domain interactions

# controllers 
 - single action controllers
    - you can name each controller after the described interaction
    - there is no need to group interactions: use namespaces for that
    - we still have to extend from `AbstractController` or `AbstractActionController`, due to weird
      `zendframework/zend-mvc` event flows
       - too bad: `DispatcherInterface` was already an awesome middleware :-(
    - moving out single interactions is simpler
    - may be simpler to move to middlewares in the future
    - we will get back at controller logic later
    
# routing
 - each route on its own
 - strict route constraints (use those regular expressions!)
 - nested routes are not really necessary, unless you are concerned about performance
 - consider using class constants for route names (easier to refactor)
 - one route <=> one controller (bijective)

# services
 - keep services as immutable as possible
 - do not store mutable data in the service manager
    - config is immutable
    - mutable data in the service manager leads to service manager abuse (as a registry)
       
# back at controllers:
 - each controller should usually do following:
    - (optional) retrieve POST data, build a `command` and pass it down to the domain layer
       - a command must be a valid and immutable object: we did all the validation before building it!
    - pass data from the domain to the view
       - can be arrays or a value objects
          - value objects ensure better type-hinting
          - value objects are easier to refactor
          - value objects take some time to code, and view requirements change VERY often
 
 - should controllers follow REST principles?
    - probably only for GET
    - POST/PUT/DELETE are interesting for APIs, but we're just complicating things otherwise
    - make all reads via GET
    - design all user interactions as specific URLs + POST
    - you still have to respect HTTP response codes!
    - it will save you a lot of time (and hair)
    
# what about forms?
 - forms are one of the last bits of your app
    - usually, people design them first: ew!
    - design domain first
    - design command second
    - then design the form
 - how do you use a form?
    - no object binding: consider filling using `$form->bindValues($presets)` manually
    - no object hydration: consider using `$form->getData()` and `MyCommand::fromFormData($data)`
 - MASSIVE simplification (trust me!)
