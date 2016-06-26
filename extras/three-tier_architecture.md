## Three-Tier Architecture

Three-tier architecture is an architectural deployment style that describe the separation of functionality into layers with each segment being a tier that can be located on a physically separate computer.

Three-tier application architecture is characterized by the functional decomposition of applications, service components, and their distributed deployment, providing improved scalability, availability, manageability, and resource utilization. 


### Structure
Using this architecture the software is divided into 3 different tiers: **Presentation**, **Logic** (also refereed to as "business logic", "data access tier", or "middle tier"), and **Data**. Each tier is developed and maintained as an independent tier.

#### Presentation tier
This is the topmost level of the application. The presentation layer provides the application's user interface (UI). Typically, this involves the use of Graphical User Interface for smart client interaction, and Web based technologies for browser-based interaction. The presentation tier displays information related to such services as browsing merchandise, purchasing, and shopping cart contents. It communicates with other tiers by outputting results to the browser/client tier and all other tiers in the network.

#### Logic tier 
The Logic tier controls an application's functionality by performing detailed processing. Logic tier is where mission-critical business problems are solved. 
#### Data tier
Here information is stored and retrieved. This tier keeps data neutral and independent from application servers or business logic. Giving data its own tier also improves scaleability and performance.


### Benefits
* **Maintainability** - Since each tier is independent of the other tiers, updates or changes can be carried out without affecting the application as a whole.
* **Scalability** - Because tiers are based on the deployment of layers, scaling out an application is reasonably straightforward.
* **Flexibility** - Because each tier can be managed or scaled independently, flexibility is increased.
* **Availability** - Applications can exploit the modular architecture of enabling systems using easily scalable components, which increases availability.



