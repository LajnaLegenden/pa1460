# 2022-01-21 Use case

**Use Case**: Namn på use caset

**Actor**: Vem som är inblandad

**Description** Hur användningsfallet går till

```plantuml
@startuml
left to right direction
actor User
actor Admin
rectangle Forum {
User -- (Read Category)
User -- (Create Thread)
User -- (Create post)
User -- (Read post)
User -- (Read Thread)
User -- (Update post)
(Update post) -- Admin
(Delete post) -- Admin
(Create Category) -- Admin
}

rectangle Auth {
    User -- (Authenticate Session)
}

@enduml
```

Olika system för olika usecases.\
Skriva beskrivning för alla use cases

---

## **Use Case** Create Thread
**Actor** User\
**Description** User selects to create new thread with a name

---

## **Use Case** Create Post
**Actor** User\
**Description** User creates a new post and selects what thread to post in. User writes the content and the system shows a preview of the post and then the user confirms.

| User                                     | System                                                  |
| ---------------------------------------- | ------------------------------------------------------- |
| 1. User selects "Create new post"        |                                                         |
|                                          | 2. System lists available threads                       |
| 3. User selects a thread to post in      |                                                         |
|                                          | 4. System asks for the contents of the post             |
| 5. User writes a post and clicks preview |                                                         |
|                                          | 6. System renders post and displays it                  |
| 7.1. User reads the previewed post       |                                                         |
| 7.2. User selects "Submit"               |                                                         |
|                                          | 8. System stores the post and displays it in the thread |



### **Alternative flow**

2 No available threads\
1--7 User selects cancel
# 2022-01-22 Domain Model

## Type of test

Types of test and frequency

| Type of test | Test Construction | Test every build | Test Every Hour | Test Every Hour | Test Every Release |
| ------------ | ----------------- | ---------------- | --------------- | --------------- | ------------------ |
| Manual       | GUI, Use cases    |                  |                 |                 | Checklist based    |
| Semi Manual  | fun() class       | Developer        |                 |                 |                    |
| Automated    | fun() class       | !                | ?               | ?               | !!                 |

## Test function

```cpp
int exp(int x,int e);
```

Test can look like:

```cpp
//Test known values
expect(exp(1,1),1);
expect(exp(2,2),4);
expect(exp(3,3),27);

//Test for failures
expect(exp(2,-1), NULL);

```

### Test class

Class has state:

- Run tests in order

Class has no state:

Phases:

- Set up
- Test
- Teardown

```js
//Setup
let testObj = ClassToTest();
testObj.connectDb();

//Run tests

assert(testObj.getData().length > 1);

//Teardown

testObj.close();
```
# 2022-01-21 Testing


## For each use case
1. Basic Description 
2. Conceptual model (using nouns from the use case)
3. System sequence diagram (from system events in use case)
4. Sequence diagram



## Example sequence diagram (incomplete)
```plantuml
@startuml seqDiagram

hide footbox
title Example Sequence Diagram (incomplete)
/' [-> pos : enterItem(upc,qty)]
activate pos
activate cs
pos --> cs : asd()
deactivate pos '/
create participant "current:Sale" as cs
create participant "si:SaleItem" as si

pos --> si : create()
pos -> si : setProductId()
activate si
deactivate si
pos -> si : setQuantity(qty)
activate si
deactivate si
pos -> cs : addItem(si)
activate cs
create participant "info:Warehouse" as info
cs --> info : create()
cs -> info : theDescription=getDescription(si -> getUpc)
activate info
cs -> info : thePrice=getPrice(si->getUpc())
deactivate info
cs -> si : setPrice(thePrice)


/' 
create participant "pos:PoS" as pos
activate pos

 deactivate pos

pos --> si : create()
pos -> si : setProductId(upc)
activate si
deactivate si

 '/
@enduml
```
## Example class diagram

```plantuml
@startuml seqDiagram

class PoS
class Sale
class SaleItem
class Warehouse

PoS : +enterItem(upc,qty)

Sale : +addItem(salesItem)
Sale : +increaseTotal(int price)
SaleItem : +setProductId(upc)
SaleItem : +setQuantity(qty)
SaleItem : +setPrice(price)
SaleItem : +getUpc()

Warehouse : +getDescription(upc)
Warehouse : +getPrice(upc)

PoS -- SaleItem
PoS -- Sale

Sale -- SaleItem
Sale -- Warehouse



@enduml
```
## Fixed example sequence diagram

```plantuml
@startuml newSeqDiagram
title Fixed example sequence diagram
hide footbox

participant ":Pos" as pos order 1
participant "current:Sale" as current order 2



?-> pos : enterItem(upc,qty)]
activate pos
pos -> current : addItem(upc,qty)
activate current
participant "si:SaleItem" as si order 3
current --> si : create()
current -> si : setProductId(upc)
activate si
participant "info:Warehouse" as info order 4
si --> info : get()
si -> info : getDescription(upc)
si -> info : getPrice(upc)
si--> info:destroy()
destroy info
deactivate si
current -> si : setQuantity(qty)
current -> current : increaseTotal(si->getPrice())
activate current

current -> si : theDescription = getDescription(si->getUpc())
current --> pos : theDescription

deactivate current

deactivate current
deactivate pos

@enduml
```
## Fixed example class diagram
```plantuml
@startuml
title Fixed example class diagram
class PoS
class Sale
class SaleItem
class Warehouse
PoS : +enterItem(upc,qty)
PoS : -Sale current
Sale : +addItem(upc,qty)
Sale : +increase Total(the Price)
Sale : -Vector<SaleItem> myItems
SaleItem : +setProductId(upc)
SaleItem : +setQuantity(qty)
SaleItem : +getPrice()
SaleItem : +getDescription()
SaleItem : -int myQuantity
SaleItem : -int myPrice
Warehouse : +getDescription(upc)
Warehouse : +getPrice(upc)
PoS - Sale
Sale - SaleItem
SaleItem --Warehouse

@enduml

```
# 2022-01-21 GRASP Design Pattens
## GRASP Patterns
### Low Coupling 
- Lite kopplingar, enklare att förstå / testa
### High Cohesion 
- Tydligt ansvarsområde

### Information Expert
  - Har klassen datan, då ska klassen har metoderna
  - Domain Information
  - Design Information
  - Interface Information (veta hur ett gränssnitt fungerar)
    - Vilka medtoder som finns under mig (typ basklass)
### Creator
  - Vem skapar objekten?
  - Klasses som har informationen för att skapa objekten,  skapar också dem
  - Special fall av Information expert
 

```plantuml
@startuml
class Sale
abstract class SaleItem

SaleItem <|-- GiftCard
SaleItem <|-- PoisonousSubstance
SaleItem <|-- FoodItem

Sale : +addItem(upc,qty)

SaleItemFactory : +createItem(upc,qty)
Sale -- SaleItemFactory
SaleItemFactory -- SaleItem : creates >
Sale - "*" SaleItem
SaleItem : +calculateVAT() = 0

GiftCard : +calculateVAT()
PoisonousSubstance : +calculateVAT()
FoodItem : +calculateVAT()

@enduml


```
 - Sale = Informaton Expert
  - SaleItemFactory = Creator
### Controller
  - Hanterar usecases

### Polymorphism
- Låt kompilarorn göra sitt jobb, köra rätt funktion
```c++
    Sale::WrongCalculateVAT(){
            int vat = .25
        foreach(item: mySaleItemList){
            int type item->getType();
            switch(type){
                case 1:
                    vat = 0.25;
                    break;
                case 2:
                    vat = 0.08;
                    break;
            }
        }
    }

    Sale::calculateVAT(){
        int vat = 0.25
        foreach(item: mySaleItemList){
            vat = item->calculateVAT();
        }
    }
```
### Indirection
- Få så lösa kopplingar som möjligt
### Protected variations
- Göm skillnad i kod bakom arv eller ett interface
### Pure Fabrication


## Multiplicity
```plantuml
@startuml 220127-83
left to right direction
Sale1 ----- "(*) 0 eller med" SaleItem1
Sale2 ----- "(+) 1 eller mer" SaleItem2

Sale3 ----- "(4..10) 4 -10 st" SaleItem3
Sale4 ----- "(4,10) 4 eller 10 st" SaleItem4
@enduml
```
# 2022-01-28 Complete Example -- Burger order 
## Use cases
### Order Meal
  - **Actors**: Customer
  - **Desc**: Customer arrives at the BurgerOrder. Customer selects a meal, then configure the burger, and orders it.
### Main course of events
| Actor                                                         | System                                            |
| ------------------------------------------------------------- | ------------------------------------------------- |
| 1. Customer arrives as the BurgerOrder and starts a new order |                                                   |
|                                                               | 2. System presents valid categories               |
| 3. Customer selects "Meal"                                    |                                                   |
|                                                               | 4. System presents available meal types           |
| 5. Customer selects what meal type                            |                                                   |
|                                                               | 6. System adds selected meal type to order        |
|                                                               | 7. System presents configurations                 |
| 8. Customer selects "no onions"                               |                                                   |
|                                                               | 9. System adds "no onions" to order               |
| 10. Customer confirms order.                                  |                                                   |
|                                                               | 11. System triggers usecase  <u>pay for order</u> |
|                                                               | 12. System sends order to <u>kitchen</u>          |
|                                                               | 12. System prints receipt                         |

### Alternative flows
* 1-10: Customer cancels the order
- 6: Meal us currently not available. System informs user and asks for a new Meal Type

### Methods
* startNewOrder()
* selectOrderCategory()
* selectOrder()
* selectConfigurationOption(****)
* confirmOrder()
### UML SSD
```plantuml
@startuml 220128-34
title System Sequence Diagram For UseCase: Order Meal
actor cus as "Customer"
participant ":BurgerOrder" as sys
cus -> sys: startNewOrder()
sys --> cus : list of meal categories
cus -> sys : selectOrderCategory(type)
sys --> cus: list of available order types
cus -> sys : selectOrder(orderType)
sys --> cus : list of config options
cus -> sys :selectConfigOptions(options)
sys --> cus : confirms configurations
cus -> sys : confirmOrder()
sys --> cus : order confirmation


@enduml
```
### Sequence diagram for method calls
#### **startNewOrder()**
```plantuml
@startuml 220128-55
participant ":BurgerOrder" as sys
[-> sys : startNewOrder()
activate sys
create participant "current:Order" as order
sys --> order : create()
sys -> ":OrderCategoryManager" as odb : listOrderCategories()
[<-- sys : list of order categories

deactivate sys
@enduml
```
---
#### **selectOrderCategory(category)**
```plantuml
@startuml 220128-69
participant ":BurgerOrder" as sys
[-> sys : selectOrderCategory(category)
activate sys
sys -> ":OrderCategoryManager" : oc = getOrderCategory(category)
activate ":OrderCategoryManager"
deactivate ":OrderCategoryManager"
participant "oc:OrderCategories" as oc

sys -> oc :listOrderTypes()
activate oc
deactivate oc
[<-- sys :list of available order types
deactivate sys

@enduml
```
---
#### **selectOrder(orderType)**
```plantuml
@startuml 220128-89
participant ":BurgerOrderer" as sys
[-> sys : selectOrderType(theOrderTypeName)
activate sys
sys -> ":OrderTypeManager" : createOrderType(theOrderTypeName)
activate ":OrderTypeManager"
create participant "ot:OrderType"
":OrderTypeManager" --> "ot:OrderType" : create()
deactivate ":OrderTypeManager"
sys -> "current:Order" : append(ot)
activate "current:Order"
deactivate "current:Order"
sys -> "ot:OrderType" : listConfigurationOptions()
activate "ot:OrderType"
deactivate "ot:OrderType"

[<-- sys : list of config options
@enduml
```
---
#### **selectConfigurationOption(theConfigurationOptionName)**
```plantuml
@startuml 220128-110
participant ":BurgerOrderer" as sys
[-> sys : selectConfigurationOption(theConfigurationOptionName)
activate sys

sys -> "currentOrderType:OrderType" : co = createConfigurationOption(theConfigurationOptionName)
activate "currentOrderType:OrderType"
create participant "co:ConfigurationOption"
"currentOrderType:OrderType" --> "co:ConfigurationOption" : create()
deactivate "currentOrderType:OrderType"
sys -> "current:Order" : append(co)
[<-- sys : confirms selection
deactivate sys
@enduml
```
---
#### **confirmOrder()**
```plantuml
@startuml 220128-130
participant ":BurgerOrderer" as sys
[-> sys : confirmOrder()
activate sys
sys -> "current:Order" : price = calculateTotal()
actor ":PaymentManager"
actor ":KitchenManager"
sys -> ":PaymentManager" : startPayment(price)
sys -> ":KitchenManager" : startProcessing(current)
actor ":PrinterManager"
sys -> "current:Order" : rec = getFormattedReceipt()
sys -> ":PrinterManager" : printReceipt(rec)
deactivate sys
@enduml
```