# HBV602 - Notes

#### Software Architecture, Analysis, Decisions, Structure, Tactics and Checklists

****

## Software Architecture

“A software architecture is
* the set of significant *decisions* **about the organization** of a software system,
* the *selection* **of the structural elements** of which the system is composed,
  * and their **interfaces**,
together with
* their behavior, as specified in the **collaborations** among those elements,
* the **composition** of these structural and behavioral elements
  * into progressively larger subsystems,
and
* the architectural **style** that guides this organization
  * of the elements and their interfaces, their collaboration, and their composition.”

*- based on Booch, Rumbaugh, Jacobson: The UML User Guide, 1999.*

### Architecture’s Impact on Quality Attributes

A system’s architecture **determines** the system’s **quality attributes**, e.g.
* Availability
* Interoperability
* Modifiability
* Performance
* Security
* Testability
* Usability

A system’s architecture is established in a series of design decisions, e.g. on
* Allocation of responsibilities
* Coordination model
* Data model
* Management of resources
* Mapping of architectural elements
* Variations and binding times
* Technology choices

### Architectural analysis

* How do we come up with a software architecture?
* The essence of architectural analysis is to
  * *identify* factors that should influence the architecture,
  * *understand* their variability and priority, and
  * *resolve* them by making architectural decisions.
* Challenge: It’s difficult to...
  * Know what questions to ask
  * Weigh the trade-offs
  * Know the many ways to resolve an architecturally significant factor
  * Decide on the best way under the given circumstances


### Examples of Issues to be Resolved at Architectural Level

* How do reliability and fault-tolerance requirements affect the design?
  * For what remote services will fail-over to local services be allowed? Why?
  * Do the local and remote services work exactly the same? What are the differences?
* How do the licensing costs of purchased subcomponents affect profitability?
  * Should we integrate a high-quality third-party persistence framework that will charge a fee on all transactions, go with a low-cost open-source alternative, or develop our own?
* How do the adaptability and configurability requirements affect the design?
  * What variations of business rules need to be reflected in the implementation?
  * What degree of evolution should be accommodated? How should variations be specified?
* How do availability and dependability requirements influence the effort?
  * Very strict availability requirements may induce huge costs for redundant hardware, hotswap capability, failover mechanisms, backups, etc. Is this effort commensurate with the risk?


### Common Steps in Architectural Analysis 

Goal: Understand **influence**, **priorities** and **variability** of *architectural factors* (a.k.a. *drivers*).

1. Identify and analyze architectural factors, i.e. requirements that have an architectural impact
   * Especially non-functional (quality) requirements
   * But also functional requirements, esp. regarding expected variability or change
   * Can be found e.g. in
     * Vision and Scope document
     * Business Rules document
     * Supplementary Specification document
     * Use Case document (sections on special requirements, technology variations, open issues in fully-dressed format)

2. Make architectural decisions
   * Analyze alternatives
   * Create solutions that address the impact:
     * Build a custom solution
     * Buy a third-party solution
     * Remove the requirement
     * Hire an expert
     * ...

3. Document decisions
   * So people will not inadvertently undermine design decisions later
   * or spend time pursuing rejected options


### Making Architectural Decisions

* **Collecting** and **describing** architectural drivers is comparatively easy.
* **Addressing** and **resolving** them in light of **interdependencies** and **trade-offs** is much more difficult.
  * Highly dependent on application domain and technology
  * Business goals and stakeholder interests become central to technical decisions
  * Requires consideration of more large-scale/global goals and trade-offs than local-scale UI or OO design decisions
  * Requires knowledge and critical consideration of many areas:
    * Architectural styles and patterns, technologies, products, pitfalls, domain knowledge, trends...
* Hierarchy of **goals** to guide **priority** of architectural decisions:
  1. Inflexible constraints, e.g. safety and legal compliance – unavoidable
  2. Business goals, e.g. particular features, deadlines etc. – some flexibility
  3. All other goals (are often derived from business goals) – some leeway for interpretation


### Characteristics of Software Architecture

* Architectural concerns are especially **related to non-functional requirements**,  
but their **resolution permeates the implementation of the functional requirements**.
* Architectural concerns **require awareness** of the **business context**,
**stakeholder goals**, as well as **requirements’ variability and evolution**.
* Architectural concerns involve **system-level, large-scale issues** whose resolution  
usually involves **large-scale, fundamental decisions** that are **extremely costly to change** later on.
* Architectural decisions depend on the **conception** and **critical evaluation** of **alternative solutions**.
* Architectural decisions usually involve **interdependencies** and **trade-offs**.


## Architectural Structures

###### = “Perspectives” on a system. Can be described in design documents, UML diagrams, etc.

* Software architecture is the set of structures needed to **reason** about a system.
  * "Software architecture is the set of design decisions which, if made incorrectly,  
    may cause your project to be cancelled." - Eoin Woods
* A structure is a set of software **elements** and the **relations** between them.
  * **Module structures** show how a system is to be statically structured 
  as a set of code or data units that have to be constructed or procured.
  * **Component-and-connector structures** show how a system is to be dynamically structured  
  as a set of modules having runtime behavior (components) and interactions (connectors).
  * **Allocation structures** show how software structures are to be mapped to the system’s  
  organizational, developmental, installation and execution environments.
* Each structure has the potential to **influence quality attributes** of the system.
  * e.g. availability, interoperability, modifiability, performance, security, testability, usability
* A system’s architecture is **established through a series of design decisions**.
  * e.g. responsibility allocation, resource management, binding times, technology choices
* These **occur in** and **affect** technical, project, business, professional **contexts**.


### Module Structures

* Module structures show how a system is to be **statically structured**
as a **set of code or data units** that have to be constructed or procured.
* Focus on modules and relationships established at **design time**
  * e.g. layers such as database, business logic, user interface; subdivided further into
  components, which are subdivided into classes, which are subdivided into methods
  * **Which modules are there? How are they composed? How do they rely on each other?**
* Examples of module structures:
  * Decomposition structure: Shows how modules are recursively composed of submodules
    * Relevant for localizing responsibilities expressed in functional requirements,  
    organizing the project, supporting modifiability through encapsulation, etc.
  * Uses structure: Shows how modules are depending on other modules
    * Relevant for setting priorities, tracing faults, supporting extensibility and reusability, etc.
* A module structure defines e.g.:
  * What is the primary functional responsibility assigned to each module?
  * What other modules is a module allowed to use?
  * What other modules does it actually use and depend on?
  * What modules are related to other modules by inheritance relationships?

#### Architectural Design Decisions Shaping the Module Structure

* Decisions about the **allocation of responsibilities**  
(i.e. where functional requirements will manifest themselves) (see example below)
  * Identifying the important responsibilities, including basic system functions,  
  architectural infrastructure, satisfaction of quality attributes
  * Determining how these responsibilities are allocated to modules at design time and run time
* Decisions about the **data model**  
(i.e. the representation of entities whose processing is the main purpose of the system) (see example below)
  * Choosing the major data abstractions, their operations and properties
    * i.e. how to create, initialize, access, persist, manipulate, translate, and destroy data entities
  * Compiling metadata needed for consistent interpretation of the data
  * Organizing the data
    * e.g. object-oriented vs. relational representation, conversion between both representations


### Component-and-Connector Structures

* Component-and-connector structures show how the modules **behave and interact**  
  with each other at **run time** to carry out the system’s **functions**.
  * i.e. components such as services, peers, clients, servers, filters etc.,  
    and connectors such as call-returns, process synchronization operations, pipes, etc.
  * **How are the module instances hooked together at runtime? How do they interact?**
* Examples of component-and-connector structures:
  * **Service structure**: Shows how (possibly independently engineered) services can  
  interoperate with each other using a service coordination mechanism
    * Relevant for supporting interoperability, reliability etc.
  * **Concurrency structure**: Shows where opportunities for parallelism exist
  and where resource contention may occur
    * Relevant for supporting performance, testability, availability etc.
* A component-and-connector structure defines e.g.:
  * What are the major executing modules and how do they interact at runtime?
  * What are the major shared data stores?
  * Which parts of the system are replicated?
  * How does data progress through the system?
  * What parts of the system can run in parallel?
  * Can the system’s structure change as it executes, and if so, how?
  * What run-time properties (performance, security, availability etc.) does the system exhibit?

#### Architectural Design Decisions Shaping the Component-and-Connector Structure

* Decisions about the **coordination model**  
(i.e. how modules interact through designed mechanisms) (See example below)
  * Identifying the modules that must cooperate, or are prohibited from cooperating
  * Determining the properties of the cooperation, e.g. timeliness, currency, consistency etc.
  * Choosing the communication mechanisms between modules and systems
    * e.g. stateful vs. stateless, synchronous vs. asynchronous, guaranteed vs. best-effort
* Decisions about **variations and binding times**  
(i.e. allowable range, time and mechanism of variations of a system) (see examples below)
  * Identifying variation points in which functionality/properties of the system can be changed
  * Deciding on the range of supported variations per variation point
  * Deciding on the binding time of variations, and the actor choosing a variation
    * e.g. at design time by the developer, at deploy time by an install wizard, at run time by the user...
  * Choosing mechanisms to specify and execute the variation
    * e.g. in source code, compiler directives, make files, configuration files, graphical user interface...


### Allocation Structures

* Allocation structures show how **software structures** are to be **mapped to** the
**system’s** (*non-software*) organizational, developmental, installation & execution **environments**.
  * e.g. devices, CPUs, file systems, networks, development teams, etc.
  * **Who builds the modules? Where are they run/stored? What infrastructure do they use?**
* Examples of allocation structures:
  * *Work assignment structure*: Shows how responsibility for implementing the modules is  
    assigned to teams, and which expertise is required on each team
    * Relevant for staffing, project management, collaboration infrastructure etc.
  * **Deployment structure**: Shows how modules are allocated to hardware components for  
    processing and communication, and how they may migrate between hardware components
    * Relevant for supporting performance, data integrity, security and availability,  
      especially in distributed and parallel systems.

* An allocation structure defines e.g.:
  * What processor is each module instance executed on?
  * In what directories or files is each module stored during development, testing and building?
  * What is the assignment of each module to development teams?


#### Architectural Design Decisions Shaping the Allocation Structure
* Decisions about the **mapping between architectural elements**  
(i.e. between elements of development and execution; and software and hardware elements) (see examples below)
  * Mapping design-time modules and run-time instances
  * Assigning run-time elements to devices or processors
  * Assigning entities in the data model to data stores
* Decisions about **management of resources**  
(i.e. arbitrating the use of shared resources, e.g. CPU, storage, peripherals etc.)
  * Identifying resources to be managed, and determining limits for each
  * Determining which modules should manage which resource
  * Determining how resources are shared, and what arbitration mechanisms are required
  * Determining and avoiding the impacts of saturation of different resources


### Architectural Design Decisions Shaping All Structures

* Decisions about **technology choices**  
(i.e. selection of suitable technologies to support preceding architectural decisions) (see examples below)
  * Identifying candidate technologies that can realize decisions made in preceding categories
  * Determining whether adequate internal and external expertise is available for a technology
  * Determining side effects of a technology, e.g. in coordination or resource management
  * Determining compatibility of a new technology with existing technology stack
  * Determining whether available tools adequately support technology choices
  * Choosing a suitable technology to support a particular requirement or architectural decision


## Architectural Tactics

An architectural tactic is a design decision that affects a quality attribute.

![quality-attributes](notes-screens/quality-attributes.png)


### Availability Tactics

* Prevent faults 
  * Removal from service, Transactions, Predictive model, 
  * Exception prevention, Increase competence set.
* Detect faults 
  * Ping/echo, Monitor, Heartbeat, Timestamp, Sanity checking, 
  * Condition monitoring, Voting, Exception detection, Self-test.
* Recover from faults 
  * Preparation and repair 
    * Active redundancy, Passive redundancy, Spare, Exception handling, Rollback, 
    * Software upgrade, Retry, Ignore faulty behavior, Degradation, Reconfiguration.
  * Reintroduction 
    * Shadow, State resynchronization, Escalating restart, Non-stop forwarding.


### Modifiability Tactics

* Reduce module size
  * Split module
* Increase cohesion
  * Increase semantic coherence
* Reduce coupling
  * Encapsulate, Use an intermediary, Restrict dependencies, Refactor, Abstract common services.
* Defer binding


### Security Tactics

* Detect attacks
  * Detect intrusion, Detect service denial, Verify message integrity, Detect message delay.
* React to attacks
  * Revoke access, Lock computer, Inform actors
* Resist attacks
  * Identify actors, Authenticate actors, Authorize actors, Limit access, 
  * Limit exposure, Encrypt data, Separate entities, Change default settings.
* Recover from attacks
  * Maintain audit trail, Restore (see *Availability*)


### Testability tactics

* Control and observe system state
  * Specialized interfaces, Record / playback, Localize state storage,
  * Abstract data sources, Sandbox, Executable assertions.
* Limit complexity
  * Limit structural complexity, Limit nondeterminism.


### Usability 

* Support user initiative
  * Cancel, Undo, Pause / resume, Aggregate.
* Support system initiative
  * Maintain task model, Maintain user model, Maintain system model

### Interoperability Tactics

* Locate
  * Discover service
* Manage interfaces
  * Orchestrate, Tailor interface.


### Performance Tactics

* Control resource demand
  * Manage sampling rate, Limit event response, Prioritize events, 
  * Reduce overhead, Bound execution times, Increase resource efficiency.
* Manage resources
  * Increase resources, Introduce concurrency
  * Maintain multiple copies of computations
  * Maintain multiple copies of data
  * Bound queue sizes, Schedule resources



### Example: Design Checklist for Performance

###### *Bass et al.: Software Architecture in Practice, 3rd Ed. Addison-Wesley 2013*

#### Allocation of responsibilites

**Determine the system's responsibilities** that will involve heavy loading, have time-critical response
requirements, are heavily used, or impact portions of the system where heavy loads or time-critical
events occur.

For those responsibilities, **identify the processing requirements of each responsibility, and determine
whether they may cause bottlenecks**. Also, identify additional responsibilities to recognize and process
requests appropriately, including

* Responsibilities that result from a thread of **control crossing process or processor boundaries**
* Responsibilities to **manage the threads of control** - allocation and deallocation of threads,  
  maintaining thread pools, and so forth
* Responsibilities for scheduling shared resources or managing performance-related artifacts  
  such as queues, buffers, and caches.

For the responsibilities and resources you identified, **ensure that the required performance response
can be met** (perhaps by building a performance model to help in the evaluation).


#### Coordination model

**Determine the elements of the system that must coordinate** with each other - directly or indirectly - 
and choose communication and coordination mechanisms that do the following:

* **Support** any introduced **concurrency** (for example, is it thread safe?), 
  **event prioritization, or scheduling strategy**
* Ensure that the **required performance response** can be delivered
* Can capture **periodic, stochastic, or sporadic event arrivals**, as needed
* Have the **appropriate properties of the communication** mechanisms; for example, stateful,  
  stateless, synchronous, asynchronous, guaranteed delivery, throughput, or latency


#### Data model

**Determine those portions of the data model** that will be heavily loaded, have time-critical response
requirements, are heavily used, or impact portions of the system where heavy loads or time-critical
events occur. For those data abstractions, determine the following:

* Whether **maintaining multiple copies** of key data would benefit performance
* Whether **partitioning data** would benefit performance
* Whether **reducing the processing requirements** for the creation, initialization, persistence,  
  manipulation, translation, or destruction of the enumerated data abstractions is possible
* Whether **adding resources** to reduce bottlenecks for the creation, initialization, persistence,  
  manipulation, translation, or destruction of the enumerated data abstractions is feasible.


#### Mapping among architectural elements

Where heavy network loading will occur, determine whether **co-locating some components** will reduce
loading and improve overall efficiency.

Ensure that components with heavy computation requirements are **assigned to processors with the
most processing capacity**. Determine where **introducing concurrency** (that is, allocating a piece of
functionality to two or more copies of a component running simultaneously) is feasible and has a
significant positive effect on performance.

Determine whether the choice of threads of control and their associated responsibilities introduces **bottlenecks**.


#### Resource management

**Determine which resources in your system are critical for performance**.  
For these resources, ensure that they will be **monitored and managed** under normal and overloaded system operation.  
For example:

* System elements that need to be aware of, and manage, time and other performance-critical resources
* Process/thread models
* Prioritization of resources and access to resources
* Scheduling and locking strategies
* Deploying additional resources on demand to meet increased loads


#### Binding time

For each element that will be bound after compile time, determine the following:

* **Time necessary to complete the binding**
* Additional **overhead introduced by using the late binding mechanism**

Ensure that these values do not pose unacceptable performance penalties on the system.


#### Choice of technology

Will your choice of technology let you **set and meet hard, real-time deadlines**?  
Do you know its **characteristics under load and its limits**?  
Does your choice of technology give you the ability to set the following:

* Scheduling policy priorities
* Policies for reducing demand
* Allocation of portions of the technology to processors
* Other performance-related parameters

Does your choice of technology **introduce excessive overhead** for heavily used operations?


### Example: Design Checklist for Modifiability

###### *Bass et al.: Software Architecture in Practice, 3rd Ed. Addison-Wesley 2013*

#### Allocation of responsibilites

Determine which changes or categories of changes are likely to occur through consideration of changes  
in technical, legal, social, business, and customer forces.  
For each potential change or category of changes: 

* Determine the responsibilities that would need to be added, modified, or deleted to make the change.
* Determine what responsibilities are impacted by the change.
* Determine an allocation of responsibilities to modules that places, as much as possible,
responsibilities that will be changed (or impacted by the change) together in the same module,
and places responsibilities that will be changed at different times in separate modules.


#### Coordination model

Determine which functionality or quality attribute can change at runtime and how this affects
coordination; for example, 

* Will the information being communicated change at runtime, or 
* will the communication protocol change at runtime? 
* If so, ensure that such changes affect a small number set of modules.

Determine which devices, protocols, and communication paths used for coordination are likely to change.  
For those devices, protocols, and communication paths, ensure that the impact of changes will
be limited to a small set of modules.

For those elements for which modifiability is a concern, use a coordination model that reduces coupling
such as publish- subscribe, defers bindings such as enterprise service bus, or restricts dependencies such
as broadcast


#### Data model

Determine which changes (or categories of changes) to the data abstractions, their operations, or their
properties are likely to occur. Also determine which changes or categories of changes to these data
abstractions will involve their creation, initialization, persistence, manipulation, translation, or destruction.

For each change or category of change, determine if the changes will be made by an end user, a system
administrator, or a developer. For those changes to be made by an end user or system administrator,
ensure that the necessary attributes are visible to that user and that the user has the correct privileges
to modify the data, its operations, or its properties.

For each potential change or category of change: 

* Determine which data abstractions would need to be added, modified, or deleted to make the change. 
* Determine whether there would be any changes to the creation, initialization,  
  persistence, manipulation, translation, or destruction of these data abstractions.
* Determine which other data abstractions are impacted by the change. For these additional  
  data abstractions, determine whether the impact would be on the operations, their properties,  
  their creation, initialization, persistence, manipulation, translation, or destruction.
* Ensure an allocation of data abstractions that minimizes the number  
  and severity of modifications to the abstractions by the potential changes.

Design your data model so that items allocated to each element of the data rnooel are likely to change together.

#### Mapping among architectural elements

Determine if it is desirable to change the way in which functionality is mapped to computational
elements (e.g., processes, threads, processors) at runtime, compile time, design time, or build time.

Determine the extent of modifications necessary to accommodate the addition, deletion, or
modification of a function or a quality attribute. This might involve a determination of the following,  
for example:

* Execution dependencies
* Assignment of data to databases
* Assignment of runtime elements to processes, threads, or processors

Ensure that such changes are performed with mechanisms that utilize deferred binding of mapping
decisions.

#### Resource management

Determine how the addition, deletion, or modification of a responsibility or quality attribute will affect
resource usage. This involves, for example:

* Determining what changes might introduce new resources or  
  remove old ones or affect existing resource usage
* Determining what resource limits will change and how they will change

Ensure that the resources after the modification are sufficient to meet the system requirements.

Encapsulate all resource managers and ensure that the policies implemented by those resource
managers are themselves encapsulated and bindings are deferred to the extent possible.


#### Binding time

For each change or category of change:

* Determine the latest time at which the change will need to be made. 
* Choose a defer-binding mechanism that delivers the appropriate capability at the time chosen.
* Determine the cost of introducing the mechanism and the cost of making changes using the
chosen mechanism. Use the equation on page 118 to assess your choice of mechanism.
* Do not introduce so many binding choices that change is impeded because the dependencies
among the choices are complex and unknown. 


#### Choice of technology

Determine what modifications are made easier or harder by your technology choices.

* Will your technology choices help to make, test, and deploy modifications?
* How easy is it to modify your choice of technologies?
  * (in case some of these technologies change or become obsolete)

Choose your technologies to support the most likely modifications. For example, an enterprise service
bus makes it easier to change how elements are connected but may introduce vendor lock-in.


## Software Architecture in Practice

### Structural Recommendations for Good Architectures

* Have well-defined, well-encapsulated modules.
* Use well-known architectural patterns and tactics to achieve quality attributes.
* Don’t depend on a particular version of a particular product or tool, but structure  
  the system so that swapping it out is straightforward and inexpensive.
* Separate modules that produce data from modules that consume data.
* Don’t expect a one-to-one correspondence between design-time modules and their run-time instances.
* Ensure that any process can be (re)assigned to any processor, even at run time.
* Use the same small set of module interaction paradigms throughout the system.
* Ensure that areas of resource contention are small,  
  and that clear conflict resolution mechanisms are specified for them.


### Software Process Recommendations Towards Good Architectures

* The architecture should be the responsibility of a **single lead architect** who has a  
  strong connection to the development team, to ensure conceptual integrity.
* The architect should **base the architecture on a prioritized list of well-specified  
  quality attributes** that inform the inevitable trade-offs.
* The architecture **documentation should address the concerns of the most important stakeholders**,  
  including analysis, construction, maintenance and training.
* The architecture should be **evaluated early and repeatedly** for its ability to  
  deliver the system’s important quality attributes.
* The architecture should **lend itself to incremental implementation**
  * *e.g. by building a skeletal system first in which just the communication paths are in place,  
    and to which the functional components can be added incrementally.* **... Damn nice software process.**


### Iterative-Incremental Development of Architecture

* **Inception**
  * Candidate architectures are considered.
  * If architectural feasibility of key requirements is questionable,  
    a light proof-of-concept architecture may be implemented to determine feasibility.
* **Elaboration**
  * Architectural drivers (i.e. major architectural risks) are identified and resolved.
  * An executable architecture that can serve as the backbone of further implementation is built.
* **Construction**
  * Business components are built and integrated with the core architecture.
  * No major changes of the architecture should be necessary.
* **Transition**
  * The final architecture is documented as a basis for future maintenance and evolution.


### Up-Front Architecture Planning vs. Agility

* Pressure to deliver a working system with demonstrable features early  
can lead to a disregard of architectural concerns in an agile project
* Each feature would be independently designed and implemented
* Concerns cutting across more than one feature may be easily missed,  
  resolved in redundant or incompatible ways
* In an architecture-centric project, up-front analysis of functional and quality  
requirements, and making suitable design decisions, likely leads to a stable  
architecture, but takes time in which the customer does not get tangible results
  * Ideally, agile projects will want to evolve the architecture as they go along,  
  while traditional projects invest more time in up-front analysis and planning
    * Balance modifiability tactics with the agile **YAGNI principle ("don't do what you don’t have to do"**)
* The whole point of choosing how much time to budget for architecture is to  
reduce financial / political / operational / reputational risk.
* Project realities often force architects to deal with conflicting goals:
  * Pleasing the customer by enabling the team to deliver many requested features fast, vs.
  * Ensuring the system’s longevity by designing for modifiability, extensibility, portability up front
  * Many requirements trade off against each other
    * e.g. security comes at expense of performance, modifiability comes at expense of time to market,
    availability and performance come at expense of modifiability and cost, etc.
* Strive to work from two perspectives simultaneously:
  * **Top-down view**: Design and analyze architectural structures that meet quality requirements and make appropriate trade-offs
  * **Bottom-up view**: Analyze wide array of implementation- and environment-specific constraints and find solutions to them
* The latter activities address questions (= project risks) that can better be answered experimentally than analytically
* Experiments / prototypes to eliminate these risks are the focus of “spikes” in agile planning


### Technical Debt

* Technical debt is most commonly incurred by quick-and-dirty implementations  
that incur penalties in the future, in terms of increased maintenance costs.
* The longer technical debt remains in the system, the higher the necessary
maintenances costs due to the amount of code that needs to be refactored.
  * “As an evolving program is continually changed, its complexity, reflecting deteriorating
  structure, increases unless work is done to maintain or reduce it.” (Meir M. Lehman, 1980)
  * “Shipping first time code is like going into debt. A little debt speeds development so long as it
  is paid back promptly with a rewrite... The danger occurs when the debt is not repaid. Every
  minute spent on not-quite-right code counts as interest on that debt.” (Ward Cunningham,
  1992)
  * “Developers who evolve such systems suffer [due to] never being able to write quality code
  because they are always trying to catch up. Users suffer the consequences of capricious
  complexity, delayed improvements, and insufficient incremental change.” (Grady Booch,
  2014)

![technical-debt](notes-screens/technical-debt.png)


### Suggested Architecture Focus in Different Project Types

* Large and complex systems with relatively stable, well-understood requirements
  * Perform a large amount of architecture work up front
  * Low risk of changes, high pay-off in quality
* Big projects with vague or unstable requirements
  * Start by designing a complete candidate architecture (even if sketchy and omitting details)
  * Make sure it’s enough to demonstrate end-to-end functionality and link major functionality
  * Be prepared to elaborate and change this as necessary
  * Necessity for changes should be determined through spikes and prototypes
  * Architecture will not be perfect, but help guide understanding, development
* Smaller, less critical projects with uncertain requirements
  * Get agreement on central patterns to be employed
  * But don’t spend too much time on up-front architecture analysis and documentation


### The Pivotal Role of Software Architecture

* Having a good software architecture is a key to a project’s success because:
  * An architecture will **inhibit or enable a system’s driving quality attributes**.
  * Architecture analysis enables early prediction of a system’s qualities.
  * A documented architecture enhances communication among stakeholders.
  * An architecture carries the earliest, most fundamental, **hardest-to-change** design decisions.
  * An architecture defines a **set of constraints on subsequent implementation**.
  * An architecture **dictates the structure of an organization**, or vice versa.
  * An architecture can provide the basis for evolutionary prototyping.
  * Architecture decisions allow you to reason about and manage system change and evolution.
  * An architecture is the key artifact enabling reasoning about cost and schedule.
  * An architecture **can be a transferable, reusable model** at the heart of a product line.
  * An architecture focuses **attention on assembly** rather than just creation of components.
  * An architecture **reduces design and system complexity** by restricting design alternatives.
  * An architecture can be the **starting point for introducing new team members** to a project.





