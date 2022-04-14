# Drive-my-Car
Database design of an automobile dealership

Overview

In this project, you will expand your auto dealer model from Project 1 to enable more complex vehicle representations. We will represent the idea of a "trim level" for an automobile, as well as optional "packages" that add new "features" to an individual vehicle. Your model will reflect the best-practices we studied regarding repeated attributes, multi-valued attributes, enumerated domains, and many-to-many associations. 

Starting Point

You are welcome to use your Project 1 work as a starting point. You are equally welcome to use my "Project 1 Canonical Solution" to get started on this project. The enterprise here will be similar but not identical to Project 1; either way, you will need to make changes to your starting point to bring it in line with the new enterprise requirements.

Changes to the Enterprise

Customers, PurchaseOrders, SalesContracts, and Production Logs

Remove these classes. (They have no changes in this project. You are welcome to keep them, but I will not grade anything involving these classes.)

Features

Suppose an Internet shopper using the dealer's website is looking for minivans (a body style). We can easily imagine running a SELECT like this to find all automobiles at the dealer that have a minivan body style:

SELECT vin
FROM vehicles
INNER JOIN models USING (model_id)
WHERE body_style = 'minivan'

Now the customer can see all our minivan vehicles. But what if they're looking for a minivan that also has a power-open trunk? (A door that opens under its own power.) That information isn't currently stored in the database... so we'll store it.

Each automobile has a set of Features that describe its component pieces and abilities. A Feature is a very broad term; something physical, like leather seats or an electric engine; it could be software, like integrated Amazon Alexa or Google Assistant; or it could be a combination of hardware and software, like a self-driving mode or hands-free lift gate (a trunk door that opens without touching it). 

The primary focus of this project is associating each Automobile with its cumulative set of Features. With this information, the dealer's website could allow customers to search for vehicles based on their Manufacturer, their Model, or any of their individual Features. 

Some car Models always come "standard" with certain Features. For example, every 2022 Toyota Sienna minivan has a "hybrid engine" that we could store as a Feature. So any Automobile linked to the 2022 Toyota Sienna minivan Model would include this feature. But Features also get associated with an automobile from two other sources: the automobile's Trim, and its Packages.

Trims

There's a lot more to specifying the Features of an individual automobile. Beyond a Model, an automobile also has a "Trim level" that groups together certain Features under a single label and price point. Every Model has its own distinct list of trim "levels", and higher levels of trim add luxury items like leather seats, automatic parking, upgraded entertainment features, etc. Trim levels are described with a name and also include the starting price for an automobile made at that trim.

Example: the 2022 Chrysler Pacifica minivan has four trim levels:

Touring: cloth seats, manual lift gate. Starting at $42,000.
Touring L: leather seats, power lift gate. Starting at $45,000.
Limited: premium leather seats, hands-free lift gate, upgraded sound system, auto-folding side mirrors. Starting at $48,000.
Pinnacle: luxury leather seats, second-row entertainment screens, premium sound system, self-parking mode. Starting at $55,000.
These are just examples for this one specific Manufacturer and Model. Other Models, even those from Chrysler, would have completely different Trim levels; the names of the trims might be the same ("Touring" is a very popular name across manufacturers), but the starting prices would certainly be different. Every Model has at least one Trim.

An Automobile specifies not just the Model and color of the car, but the Trim level as well; for example, a "2022 Toyota Sienna minivan, white color, XLE trim". (XLE being the third-tier trim for a Sienna, above LE but below Limited and Platinum.)

But wait! There's more...

Packages

Not only do cars have a Model and a Trim, but they also select one or more Packages of Features to further customize individual automobiles. A Package is an optional addition of supplemental features that can be added to certain (but not necessarily all) Trim levels of a Model. The features of a Package are usually small, discrete, and interchangeable, as opposed to Trim levels where the features are unified in some broader "vision" of what the Trim should look like and feature. A Package has a name and is limited to certain Trims of a Model; the Package has a cost that depends on which Trim it is added to.

Example: the 2022 Chrysler Pacifica has several package options:

Safety Package: self-parking mode, collision detection, and automatic emergency braking. Costs $2000 when added to Touring or Touring L; and $1500 when added to Limited (which already has some of the package features). Not available for Pinnacle..
S Appearance: changes the colors of certain parts of the car. $900 for Touring, Touring L, or Limited.
Theater Package: second-row entertainment screens, Blue-Ray player, Amazon FireTV. $2900 for either Touring L or Limited trims.
and many more.
When a dealer orders an automobile, they also specify any package(s) to add to the car's Model and Trim; for example, a 2022 Toyota Sienna minivan, white color, XLE trim, with 8-Seat Bench and Entertainment Screen packages.

Automobiles

Automobiles now specify their single Model, their single Trim, and their 0-or-more Packages. Any Features linked to those entities are implicitly linked to the Automobile.

We will no longer explicitly store an Automobile's sticker price as a column. Instead, anyone who wants the sticker price of an automobile must compute it themselves, as the starting price of the vehicle's Trim plus the sum of the costs of the chosen Packages.

Redundancy and Integrity

A major focus of this assignment is on eliminating data redundancy. I cannot possible enumerate every single potential redundancy problem in your design, but I can give a few hints:

An Automobile that already indicates its Trim level doesn't need to also reference a Model, since Trims only belong to a single Model.
No matter how many Models there are with a "sedan" body type, the string "sedan" should only appear once in the database.
Likewise, your design must minimize the potential for data integrity issues. A few hints:

Potentially NULL columns should be minimized. 
An Automobile cannot choose a Trim level that does not apply to its Model.
An Automobile cannot choose a Package that is not compatible with its Trim.
This is extremely difficult to do without using a trigger... so let's use a trigger!
Review the notes from the Complex Modeling slides. You will create a trigger that runs BEFORE INSERT on your junction table between Automobiles and Packages. The triggered function should find the Trim associated with the Automobile that is being inserted into the junction table, then check to see if there is a row in your Trim--Packages junction with the matching trim ID and package ID from the NEW row. If not, the insert should be aborted.
To see whether a row exists or not, you can SELECT its primary key (which will result in NULL if the row does not exist), or learn the EXISTS function. 

