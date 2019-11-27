---
title: Meta gegevens van de configuratie van Azure Block Chain workbench
description: Overzicht van de meta gegevens van Azure Block Chain Workbench Preview voor toepassings configuratie.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 2ee1d1da1a9a5d8e890a6578eaec42cc6bf9f3ed
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326066"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Configuratie referentie voor Azure Block Chain workbench

Azure Blockchain Workbench-toepassingen zijn werkstromen voor meerdere partijen die zijn gedefinieerd door de metagegevens in de configuratie en code in een smart contract. Configuratiemetagegevens definiëren de werkstromen van hoog niveau en het interactiemodel van de blockchain-toepassing. Smart contracts definiëren de bedrijfslogica van de blockchain-toepassing. Workbench gebruikt de configuratie en smart contractcode voor het genereren van de gebruikerservaringen in blockchain-toepassingen.

De metagegevens in de configuratie bevat de volgende informatie voor elke blockchain-toepassing:

* Naam en beschrijving van de Block Chain-toepassing
* De unieke functies die gebruikers kunnen uitvoeren of waar gebruikers aan kunnen deelnemen in de blockchain-toepassing
* Een of meer werk stromen. Elke werk stroom fungeert als een status computer om de stroom van de bedrijfs logica te beheren. Werkstromen kunnen onafhankelijk zijn of met elkaar communiceren.

Met elke gedefinieerde werk stroom geeft u het volgende op:

* Naam en beschrijving van de werk stroom
* Statussen van de werk stroom.  Elke status is een fase in de controlestroom van de bedrijfslogica. 
* Acties voor overgang naar de volgende status
* Gebruikersrollen die zijn toegestaan om een actie te starten
* Smart contracts die de bedrijfslogica in codebestanden beschrijven

## <a name="application"></a>Toepassing

Een blockchain-toepassing bevat configuratie-metagegevens, werkstromen en gebruikersrollen die kunnen handelen of deelnemen in de toepassing.

| Veld | Beschrijving | Vereist |
|-------|-------------|:--------:|
| ApplicationName | Unieke toepassings naam. Het bijbehorende slimme contract moet dezelfde **ApplicationName** gebruiken voor de toepasselijke contract klasse.  | Ja |
| DisplayName | Beschrijvende weergave naam van de toepassing. | Ja |
| Beschrijving | Beschrijving van de toepassing. | Nee |
| applicationRoles | Verzameling van [ApplicationRoles](#application-roles). Gebruikersrollen die kunnen handelen of deelnemen in de toepassing.  | Ja |
| Werkstromen | Verzameling [werk stromen](#workflows). Elke werk stroom fungeert als een status computer om de stroom van de bedrijfs logica te beheren. | Ja |

Zie het voor beeld van een [configuratie bestand](#configuration-file-example)voor een voor beeld.

## <a name="workflows"></a>Werkstromen

De bedrijfslogica van een toepassing kan worden gezien als een toestandsmachine waar een actie ervoor zorgt dat de stroom van de bedrijfslogica van de ene status naar een andere verandert. Een werkstroom is een verzameling van dergelijke statussen en acties. Elke werkstroom bestaat uit een of meer smart contracts die de bedrijfslogica in codebestanden vertegenwoordigen. Een uitvoerbaar contract is een instantie van een werkstroom.

| Veld | Beschrijving | Vereist | Maximum lengte |
|-------|-------------|:--------:|-----------:|
| Naam | Unieke werk stroom naam. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke contract klasse. | Ja | 50 |
| DisplayName | Beschrijvende weergave naam van de werk stroom. | Ja | 255 |
| Beschrijving | Beschrijving van de werk stroom. | Nee | 255 |
| Initia tors | Verzameling van [ApplicationRoles](#application-roles). Functies die zijn toegewezen aan gebruikers die zijn gemachtigd voor het maken van contracten in de werkstroom. | Ja | |
| StartState | De naam van de begin status van de werk stroom. | Ja | |
| Eigenschappen | Verzameling [id's](#identifiers). Vertegenwoordigt gegevens die kunnen worden gelezen buiten de chain of  gevisualiseerd in een hulpprogramma voor de gebruiker. | Ja | |
| Constructor | Hiermee definieert u invoerparameters die zijn opgegeven voor het maken van een instantie van de werkstroom. | Ja | |
| Functions | Een verzameling [functies](#functions) die in de werk stroom kunnen worden uitgevoerd. | Ja | |
| Staten | Een verzameling werk stroom [statussen](#states). | Ja | |

Zie het voor beeld van een [configuratie bestand](#configuration-file-example)voor een voor beeld.

## <a name="type"></a>Type

Ondersteunde gegevens typen.

| Type | Beschrijving |
|-------|-------------|
| address  | Block Chain adres type, zoals *contracten* of *gebruikers*. |
| matrix    | Eén niveau matrix van het type geheel getal, BOOL, Money of time. Matrices kunnen statisch of dynamisch zijn. Gebruik **element type** om het gegevens type van de elementen in de matrix op te geven. Zie [voorbeeld configuratie](#example-configuration-of-type-array). |
| bool     | Boolean-gegevens type. |
| aanbesteding | Adres van het type contract. |
| enum     | Geïnventariseerde set van benoemde waarden. Wanneer u het type enum gebruikt kunt u ook een lijst met EnumValues opgeven. Elke waarde is beperkt tot 255 tekens. Geldige tekens zijn hoofdletters en kleine letters (A-Z, a-z) en getallen (0-9). Zie [voorbeeld configuratie en gebruik in volheid](#example-configuration-of-type-enum). |
| int      | Gegevens type integer. |
| money    | Gegevenstype voor geldwaarden. |
| state    | Werk stroom status. |
| tekenreeks  | Het gegevenstype string. Maximaal 4000 tekens. Zie [voorbeeld configuratie](#example-configuration-of-type-string). |
| user     | Adres van het type gebruiker. |
| tijd     | Tijd gegevens type. |
|`[ Application Role Name ]`| Een naam die is opgegeven in de toepassingsrol. Beperkt gebruikers tot enkel van dat type rol. |

### <a name="example-configuration-of-type-array"></a>Voorbeeld configuratie van het type matrix

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Een eigenschap van het type matrix gebruiken

Als u een eigenschap van het type array in de configuratie definieert, moet u een expliciete get-functie invullen voor de publieke toegang tot deze eigenschap van dit type in Solidity. Bijvoorbeeld:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Voorbeeldconfiguratie van het type string

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Voorbeeldconfiguratie van het type enum

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Gebruik van het type enum in Solidity

Zodra een enum-waarde is gedefinieerd in de configuratie, kunt u het type enum gebruiken in Solidity. U kunt bijvoorbeeld een enum met de naam PropertyTypeEnum definiëren.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

De lijst met teken reeksen moet overeenkomen tussen de configuratie en het slimme contract om geldige en consistente declaraties in Block Chain Workbench te zijn.

Voor beeld van toewijzing:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Voorbeeld van een functieparameter: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Constructor

Hiermee definieert u invoerparameters voor een instantie van een werkstroom.

| Veld | Beschrijving | Vereist |
|-------|-------------|:--------:|
| Parameters | Verzameling van [id's](#identifiers) die vereist zijn om een slim contract te initiëren. | Ja |

### <a name="constructor-example"></a>Voor beeld van constructor

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

Definieert functies die kunnen worden uitgevoerd in de werk stroom.

| Veld | Beschrijving | Vereist | Maximum lengte |
|-------|-------------|:--------:|-----------:|
| Naam | De unieke naam van de functie. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de desbetreffende functie. | Ja | 50 |
| DisplayName | Beschrijvende weergave naam van de functie. | Ja | 255 |
| Beschrijving | Beschrijving van de functie | Nee | 255 |
| Parameters | Verzameling van [id's](#identifiers) die overeenkomen met de para meters van de functie. | Ja | |

### <a name="functions-example"></a>Functions-voor beeld

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Staten

Een verzameling unieke statussen binnen een werk stroom. Elke status bevat een stap in de besturingsstroom van de bedrijfslogica. 

| Veld | Beschrijving | Vereist | Maximum lengte |
|-------|-------------|:--------:|-----------:|
| Naam | De unieke naam van de status. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de betreffende status. | Ja | 50 |
| DisplayName | Beschrijvende weergave naam van de status. | Ja | 255 |
| Beschrijving | De beschrijving van de status. | Nee | 255 |
| PercentComplete | Een integer-waarde die wordt weer gegeven in de Block Chain Workbench-gebruikers interface om de voortgang in de bedrijfs logica controle stroom weer te geven. | Ja | |
| Style | Visuele hint die aangeeft of de status een succes- of faaltoestand vertegenwoordigt. Er zijn twee geldige waarden: `Success` of `Failure`. | Ja | |
| Transitions | Verzameling van beschik bare [overgangen](#transitions) van de huidige status naar de volgende set statussen. | Nee | |

### <a name="states-example"></a>Voorbeeld van statussen

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transitions

Beschikbare acties voor de volgende status. Een of meer gebruikersrollen kunnen een actie uitvoeren bij elke status, waarbij een status kan overgaan van de ene naar een andere in de werkstroom. 

| Veld | Beschrijving | Vereist |
|-------|-------------|:--------:|
| AllowedRoles | Lijst met toepassingensrollen die een overgang mogen starten. Alle gebruikers van de opgegeven rol kunnen  actie uitvoeren. | Nee |
| AllowedInstanceRoles | Lijst met deelnemende of opgegeven gebruikersrollen in het smart contract die een overgang mogen starten. Exemplaar rollen worden gedefinieerd in **Eigenschappen** binnen werk stromen. AllowedInstanceRoles vertegenwoordigen een gebruiker die deel uitmaakt van een instantie van een smart contract. AllowedInstanceRoles bieden u de mogelijkheid om de uitvoering van een actie te beperken tot een gebruikersrol in een instantie van het contract.  U kunt bijvoorbeeld alleen de gebruiker die het contract gemaakt heeft (InstanceOwner) deze laten beëindigen in plaats van alle gebruikers in roltype (Owner) als u deze rol opgeeft in AllowedRoles. | Nee |
| DisplayName | Beschrijvende weergave naam van de overgang. | Ja |
| Beschrijving | Beschrijving van de overgang. | Nee |
| Functie | De naam van de functie om de overgang te initiëren. | Ja |
| NextStates | Een verzameling van mogelijke volgende statussen na een geslaagde overgang. | Ja |

### <a name="transitions-example"></a>Overgangen-voor beeld

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Toepassingsrollen

Toepassingsrollen definiëren een groep van rollen die kunnen worden toegewezen aan gebruikers die in de toepassing acties kunnen uitvoeren of eraan deelnemen. Toepassingsrollen kunnen worden gebruikt voor het beperken van acties en deelname in de blockchain-toepassing en de bijbehorende werkstromen. 

| Veld | Beschrijving | Vereist | Maximum lengte |
|-------|-------------|:--------:|-----------:|
| Naam | De unieke naam van de toepassingsrol. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke rol. Namen van basis typen zijn gereserveerd. U kunt geen toepassingsrol een naam met dezelfde naam als het [type](#type)| Ja | 50 |
| Beschrijving | Beschrijving van de toepassingsrol. | Nee | 255 |

### <a name="application-roles-example"></a>Voor beeld van toepassings rollen

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identifiers

Identifiers omvatten een verzameling van gegevens die wordt gebruikt voor het beschrijven van werkstroomeigenschappen, de  constructor en functieparameters. 

| Veld | Beschrijving | Vereist | Maximum lengte |
|-------|-------------|:--------:|-----------:|
| Naam | De unieke naam van de eigenschap of para meter. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke eigenschap of para meter. | Ja | 50 |
| DisplayName | Beschrijvende weergave naam voor de eigenschap of para meter. | Ja | 255 |
| Beschrijving | Beschrijving van de eigenschap of para meter. | Nee | 255 |

### <a name="identifiers-example"></a>Voor beelden van id's

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Voor beeld van configuratie bestand

Asset-overdracht is een smart contract-scenario voor het kopen en verkopen van waardevolle activa, waarvoor een inspecteur en taxateur nodig is. Verkopers kunnen hun activa opgeven door het instantiëren van een smart contract voor overdracht van activa. Kopers kunnen een bod doen door een actie te ondernemen op het smart contract en andere partijen kunnen via acties de activa  controleren of de activa beoordelen. Zodra de activa is gemarkeerd als zowel gecontroleerd als beoordeeld, bevestigt de koper en de verkoper de verkoop opnieuw voordat het contract is aangemerkt als voltooid. Op elk moment in het proces hebben alle deel nemers inzicht in de status van het contract wanneer het wordt bijgewerkt. 

Zie voor [beeld van Asset-overdracht voor Azure Block Chain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer) voor meer informatie, waaronder de code bestanden

Het volgende configuratiebestand hoort bij het voorbeeld voor de overdracht van activa:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over de REST-API van Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

