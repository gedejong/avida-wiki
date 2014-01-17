Avida 3 will include and be built around a flexible, extensible application programming interface (API).  The intention of the API is to provide a powerful, stable interface to core Avida functionality.

The API will be broken up into multiple modules that will be connected together to create rich experiments and powerful analyses. The development of this API is very active right now.  The following provides an overview of existing components and an estimation of their completeness and stability.

### API Modules
* core
* data
* environment
* systematics
* util
* viewer

### Notes
The following are miscellaneous notes, which may or may not be relevant to the current Avida 3.0 plans. Preserved for consideration.

* Get rid of merit.  Organisms have energy (which they absorb by performing reactions) and metabolic rate, which determines how quickly they use that energy to execute instructions.
* Each thread in an organism is placed into the scheduler.  When a thread is created, the scheduler assigns it a process ID, and that ID is then freed up with the thread is removed.  A thread must keep track of its own ID. When a thread is removed, its ID is placed in a container of free IDs.  Only when that container is empty are new IDs created.  This will happen rarely enough that its okay if its a slow process.
* New analyze mode!  This should be interesting...  Require lex & yacc?
* Implement new method of performing mutations.  The old stuff should be entirely removed.
* This version will be optimized for stack-based CPUs, but the old register based CPUs should still function fine.
* Make sure all output files have headers.  There should be a central object that all files are output through that will make sure this is the case.
* Make events just call analyze scripts.  Re-design entire event structure.
