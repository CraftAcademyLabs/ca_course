## Design Sprint
As a first step your team needs to gather for a working session called the Design Sprint. In real projects this is done over several days (2 - 5 days approximately). In this simulation we will set aside one day to this task. 

Normally, this sprint includes a lot of input from the Client (Product Owner) and relies on his participation. We will rely in the information provided in the previous chapter and any questions we have will have to be answered by the team or, in the uncertainty is to high, just ignored. 

The main objective of this workshop/session is create a Backlog of feature requests that are described in a structured way, discuss each feature, define the **definition of done**, and **assess the complexity** of the implementation. 
### Epics
The first step is to create a series of **user stories** for the main functions outlined in the system requirements. Initially these will be on a relatively high level and be easily mapped to the overall business objectives of the system - we will refer to them as **Epics**. 

### Features and Chores
Each epic will in turn consist of **several features** each one of them described in the form of a user story.  Apart from features, that by definition need to deliver some business value and a visible result, we will also define and describe several chores. Chores are development work not resulting in tangible product changes but still needs to be performed by the team.  

### Acceptance Criteria
Once we have defined a set of features and chores for each epic, it is time to start thinking about the **definition of done**. We create a list of **Acceptance Criteria** for each entry in our backlog. This list is crucial for setting a scope for the feature and defining when the feature should be considered done and delivered. (In real projects this criteria are agreed upon with the client. In our simulation, we will be responsible for setting them within the team.)

### Complexity
Once all features and chores has been grouped into epics and the acceptance criteria for each one of them has been defined, it's time to review all of them and talk about how complex and resource consuming the implementation of each feature will be. 

We will use a fairly straight forward way of assessing complexity - making use of a three point scale. 

A task that is deemed pretty **simple** and will only take a few hours to both implement and test should be given **1 complexity point**.

A task that will require more than a day to develop, test and ship, or a feature that relies on a third party API or service, should be approach with caution, deemed to be of **intermediate complexity** and  given **2 complexity points**.

A task that will require implementation through the entire stack (new models with relation, controllers and views) and features that relies on many conditions that needs to be met, should be considered **complex** and given **3 points**.

Note that during this process you might find yourselves in a situation where the complexity of a specific feature or a chore won't fit in the three point scale. In this case you need to split the task into two or more separate features or chores and assess them individually. 













