## SlowFood Online
In this Week Lab, we will be expanding on the SlowFood challenge we've already worked on and expand it with more functionality. This challenge will differ a bit from the previous ones in the curriculum. We will simulate a real project and go over the entire process of a Design Sprint first, before we start writing our implementation.

###Core functionality
Allow local restaurant operators to publish their menus and accept orders.

Allow the general public to browse local restaurants and place orders for home delivery

#### Location
We want the system to be used in several geographical locations. All Restaurants in the system should be geo-coded by street address. All users should be geo-coded using their browser location or/and their IP address

#### Categories (restaurant) 
Each restaurant in the system should belong to a category (i. e. Thai, Chinese, French, Italian, etc). 

#### Restaurants
We want to be able to host several Restaurants in each geographical location.

A restaurant should specify what radius it will deliver to (5, 10, 20, 30 km).

#### Rating
Every Restaurant should be rated using rating system (1 - 5) with a possibility to add an optional comment.

#### Delivery
All orders from restaurants within a 3 kilometer radius should be able to be delivered together. Orders from restaurant in a wider area will add an extra delivery fee. The system should be able to handle free delivery as fell as charged.

#### Menus
Each restaurant should have one or more menus (lunch, á la carte, etc.). Each menu contains several dishes grouped in configurable categories.

#### Categories (dish) 
The system should have some default categories set up (Starter, Main course, Dessert) but also allow System users and Administrators to define their own. 

#### Dishes
Each dish should be described and have some optional fields containing allergy information, ingredients, calories, etc. If ingredients are active for a dish then the user should be able to choose to if he want to remove a specific ingredient. 

#### Order
An order should take into account all dishes from several restaurants in the area. 

#### User roles
**Administrator** - Can perform all CRUD actions on all objects in the system

**System user** - Can create restaurants with associated objects (see below)

**User** - Can place orders and add rating to Restaurants

#### UI

##### Index/Landing page
The index page of the system should list restaurants in the proximity of the user that visits the system. The page should display a map and ask the user a question "What do you feel like tonight?" The user should be given a set of restaurant categories to choose from. Once a choice has been made, the restaurants should pop up as markers on a map. A separate list (with links) of restaurants should be displayed underneath the map. The list should include the user rating average of the restaurant.

##### The restaurant show page
Clicking on a marker or a restaurant name/description in the list should take the user to the restaurants show page. Apart from the restaurant name, description and address, the page should also include a list of popular/selected dishes with ability to order ("add to order" button). 

A link to the full menu should be placed on the page. (i.e. "see full menu")

##### The menu page
The menu page should show all dishes with pictures and a brief description. A link to full dish show page should be visible on the list. 

##### The dish show page
The dish show page should show a detailed view of the dish with full description and optional information (activated by the System user that owns the Restaurant object) about the dish. 









