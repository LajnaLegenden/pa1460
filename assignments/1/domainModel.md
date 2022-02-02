```plantuml
@startuml
title Domain Model
Game -- "*" Scene
Checkpoint -- "*" GameElement : stores >
Checkpoint -- Inventory : stores >
Checkpoint - Game : stores >
Character <|-- Secretary : is a type of
GameElement <|-- GameObject : is a
GameElement <|-- Character : is a
Scene - "*" GameElement : contains >
Scene <|-- Inventory : is a type of
Inventory -- "*" GameObject : contains >
GameElement -- Event : generates >
Event -- Notification : generates >
Event -- GameElement : manipulates >
Secretary -- Notification : announces >
GameObject <|-- HelpObject : is a type of
GameObject <|-- Notification : is a
GameObject -- "*" InteractionType : has >
@enduml```