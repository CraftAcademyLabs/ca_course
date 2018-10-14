## Complexity

Once all features and chores has been grouped into epics and the acceptance criteria for each one of them has been defined, it's time to review all of them and talk about how complex and resource consuming the implementation of each feature will be.

We will use a fairly straight forward way of assessing complexity - making use of a three point scale.

A task that is deemed pretty **simple** and will only take a few hours to both implement and test should be given **1 complexity point**.

A task that will require more than a day to develop, test and ship, or a feature that relies on a third party API or service, should be approach with caution, deemed to be of intermediate complexity and given **2 complexity points**.

A task that will require implementation through the entire stack (new models with relation, controllers and views) and features that relies on many conditions that needs to be met, should be considered **complex** and given **3 points**.

Note that during this process you might find yourselves in a situation where the complexity of a specific feature or a chore won't fit in the three point scale. In this case you need to split the task into two or more separate features or chores and assess them individually.