---
title: Naslaginformatie over azure Blockchain Workbench-configuratiemetagegevens
description: Overzicht van metagegevens van azure Blockchain Workbench Preview-toepassingsconfiguratie.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 661e795f0e85f872b1072a8f641b8938115c5d7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252192"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referentie voor Azure Blockchain Workbench-configuratie

Azure Blockchain Workbench-toepassingen zijn meerpartijenwerkstromen die worden gedefinieerd door configuratiemetagegevens en slimme contractcode. Configuratiemetagegevens definiëren de werkstromen van hoog niveau en het interactiemodel van de blockchain-toepassing. Slimme contracten definiëren de bedrijfslogica van de blockchain-toepassing. Workbench gebruikt configuratie en slimme contractcode om gebruikerservaringen van blockchain-toepassingen te genereren.

Configuratiemetagegevens geven de volgende informatie op voor elke blockchain-toepassing:

* Naam en beschrijving van de blockchain-toepassing
* Unieke rollen voor gebruikers die kunnen handelen of deelnemen aan de blockchain-applicatie
* Een of meer werkstromen. Elke werkstroom fungeert als een statusmachine om de stroom van de bedrijfslogica te beheren. Werkstromen kunnen onafhankelijk zijn of met elkaar communiceren.

Elke gedefinieerde werkstroom geeft het volgende op:

* Naam en beschrijving van de werkstroom
* Status van de werkstroom.  Elke status is een fase in de controlestroom van de bedrijfslogica. 
* Acties voor de overgang naar de volgende status
* Gebruikersrollen toegestaan om elke actie te starten
* Slimme contracten die bedrijfslogica in codebestanden weergeven

## <a name="application"></a>Toepassing

Een blockchain-toepassing bevat configuratiemetagegevens, werkstromen en gebruikersrollen die kunnen handelen of deelnemen aan de toepassing.

| Veld | Beschrijving | Vereist |
|-------|-------------|:--------:|
| ApplicationName | Unieke toepassingsnaam. Het bijbehorende slimme contract moet dezelfde **ApplicationName** gebruiken voor de toepasselijke contractklasse.  | Ja |
| DisplayName | Vriendelijke weergavenaam van de toepassing. | Ja |
| Beschrijving | Beschrijving van de aanvraag. | Nee |
| Toepassingsrollen | Verzameling [toepassingsrollen](#application-roles). Gebruikersrollen die kunnen handelen of deelnemen aan de toepassing.  | Ja |
| Werkstromen | Verzameling van [werkstromen](#workflows). Elke werkstroom fungeert als een statusmachine om de stroom van de bedrijfslogica te beheren. | Ja |

Zie bijvoorbeeld het [voorbeeld van configuratiebestand](#configuration-file-example).

## <a name="workflows"></a>Werkstromen

De bedrijfslogica van een toepassing kan worden gemodelleerd als een statusmachine waarbij het uitvoeren van een actie ervoor zorgt dat de stroom van de bedrijfslogica van de ene status naar de andere wordt verplaatst. Een werkstroom is een verzameling van dergelijke statussen en acties. Elke werkstroom bestaat uit een of meer slimme contracten, die de bedrijfslogica in codebestanden weergeven. Een uitvoerbaar contract is een instantie van een werkstroom.

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | Unieke werkstroomnaam. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke contractklasse. | Ja | 50 |
| DisplayName | Vriendelijke weergavenaam van de werkstroom. | Ja | 255 |
| Beschrijving | Beschrijving van de werkstroom. | Nee | 255 |
| Initiatiefnemers | Verzameling [toepassingsrollen](#application-roles). Rollen die zijn toegewezen aan gebruikers die gemachtigd zijn om contracten in de werkstroom te maken. | Ja | |
| StartState (StartState) | Naam van de oorspronkelijke status van de werkstroom. | Ja | |
| Eigenschappen | Verzameling van [id's](#identifiers). Vertegenwoordigt gegevens die off-chain kunnen worden afgelezen of gevisualiseerd in een user experience tool. | Ja | |
| Constructor | Hiermee definieert u invoerparameters voor het maken van een instantie van de werkstroom. | Ja | |
| Functions | Een verzameling [functies](#functions) die in de werkstroom kunnen worden uitgevoerd. | Ja | |
| Staten | Een verzameling [werkstroomstatussen](#states). | Ja | |

Zie bijvoorbeeld het [voorbeeld van configuratiebestand](#configuration-file-example).

## <a name="type"></a>Type

Ondersteunde gegevenstypen.

| Type | Beschrijving |
|-------|-------------|
| adres  | Blockchain-adrestype, zoals *contracten* of *gebruikers.* |
| matrix    | Single level array van het type geheel getal, bool, geld, of tijd. Arrays kunnen statisch of dynamisch zijn. Gebruik **ElementType** om het gegevenstype van de elementen binnen de array op te geven. Zie [voorbeeldconfiguratie](#example-configuration-of-type-array). |
| Booleaanse waarde     | Booleaanse gegevenstype. |
| Contract | Adres van het typecontract. |
| Enum     | Opgesomde set benoemde waarden. Wanneer u het enumtype gebruikt, geeft u ook een lijst met EnumValues op. Elke waarde is beperkt tot 255 tekens. Geldige waardetekens zijn hoofdletters (A-Z, a-z) en getallen (0-9). Zie [voorbeeldconfiguratie en -gebruik in Solidity](#example-configuration-of-type-enum). |
| int      | Geheel getal gegevenstype. |
| Geld    | Geldgegevenstype. |
| state    | Werkstroomstatus. |
| tekenreeks  | Tekenreeksgegevenstype. Maximaal 4000 tekens. Zie [voorbeeldconfiguratie](#example-configuration-of-type-string). |
| gebruiker     | Adres van de gebruiker van het type. |
| tijd     | Tijdgegevenstype. |
|`[ Application Role Name ]`| Elke naam die is opgegeven in de toepassingsrol. Hiermee beperkt u gebruikers om van dat roltype te zijn. |

### <a name="example-configuration-of-type-array"></a>Voorbeeldconfiguratie van typearray

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

#### <a name="using-a-property-of-type-array"></a>Een eigenschap van typearray gebruiken

Als u een eigenschap definieert als typearray in configuratie, moet u een expliciete functie voor het selecteren van de openbare eigenschap van het arraytype in Soliditeit retourneren. Bijvoorbeeld:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Voorbeeldconfiguratie van teksttekenreeks

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

### <a name="example-configuration-of-type-enum"></a>Voorbeeldconfiguratie van typeenum

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

#### <a name="using-enumeration-type-in-solidity"></a>Het type opsomming gebruiken in Soliditeit

Zodra een enum in configuratie is gedefinieerd, u opsommingstypen gebruiken in Solidity. U bijvoorbeeld een enum met de naam PropertyTypeEnum definiëren.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

De lijst met tekenreeksen moet overeenkomen tussen de configuratie en het slimme contract om geldige en consistente declaraties te zijn in Blockchain Workbench.

Voorbeeld van toewijzing:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Functieparametervoorbeeld: 

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
| Parameters | Verzameling van [id's](#identifiers) die nodig zijn om een slim contract te starten. | Ja |

### <a name="constructor-example"></a>Voorbeeld van constructor

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

Hiermee definieert u functies die in de werkstroom kunnen worden uitgevoerd.

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | De unieke naam van de functie. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke functie. | Ja | 50 |
| DisplayName | Vriendelijke weergavenaam van de functie. | Ja | 255 |
| Beschrijving | Beschrijving van de functie | Nee | 255 |
| Parameters | Verzameling van [id's](#identifiers) die overeenkomen met de parameters van de functie. | Ja | |

### <a name="functions-example"></a>Voorbeeld van functies

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

Een verzameling unieke statussen binnen een werkstroom. Elke status legt een stap vast in de controlestroom van de bedrijfslogica. 

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | Unieke naam van de staat. Het bijbehorende slimme contract moet dezelfde **naam gebruiken** voor de toepasselijke status. | Ja | 50 |
| DisplayName | Vriendelijke weergavenaam van de staat. | Ja | 255 |
| Beschrijving | Beschrijving van de staat. | Nee | 255 |
| PercentageCompleet | Een gehele waarde die wordt weergegeven in de blockchain-werkbank-gebruikersinterface om de voortgang binnen de controlestroom voor bedrijfslogica weer te geven. | Ja | |
| Stijl | Visuele hint die aangeeft of de status een succes- of foutstatus vertegenwoordigt. Er zijn twee `Success` geldige `Failure`waarden: of . | Ja | |
| Overgangen | Verzameling van beschikbare [overgangen](#transitions) van de huidige status naar de volgende set staten. | Nee | |

### <a name="states-example"></a>Staten voorbeeld

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

## <a name="transitions"></a>Overgangen

Beschikbare acties naar de volgende status. Een of meer gebruikersrollen kunnen een actie uitvoeren op elke status, waarbij een actie een status kan overzetten naar een andere status in de werkstroom. 

| Veld | Beschrijving | Vereist |
|-------|-------------|:--------:|
| Toegestane rollen | Lijst met toepassingen rollen toegestaan om de overgang te starten. Alle gebruikers van de opgegeven rol kunnen de actie mogelijk uitvoeren. | Nee |
| ToegestaneInstanceRollen | Lijst met gebruikersrollen die deelnemen aan of zijn opgegeven in het slimme contract dat is toegestaan om de overgang te starten. Instantierollen worden gedefinieerd in **Eigenschappen** binnen werkstromen. AllowedInstanceRoles vertegenwoordigen een gebruiker die deelneemt aan een instantie van een slim contract. AllowedInstanceRoles geeft u de mogelijkheid om het uitvoeren van een actie te beperken tot een gebruikersrol in een contractinstantie.  U bijvoorbeeld alleen toestaan dat de gebruiker die het contract heeft gemaakt (InstanceOwner) kan worden beëindigd in plaats van alle gebruikers in roltype (Eigenaar) als u de rol hebt opgegeven in AllowedRoles. | Nee |
| DisplayName | Vriendelijke weergavenaam van de overgang. | Ja |
| Beschrijving | Beschrijving van de overgang. | Nee |
| Functie | De naam van de functie om de overgang te starten. | Ja |
| NextStates | Een verzameling van potentiële volgende staten na een succesvolle overgang. | Ja |

### <a name="transitions-example"></a>Voorbeeld van overgangen

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

Toepassingsrollen definiëren een set rollen die kunnen worden toegewezen aan gebruikers die willen handelen of deelnemen aan de toepassing. Toepassingsrollen kunnen worden gebruikt om acties en deelname binnen de blockchain-toepassing en bijbehorende workflows te beperken. 

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | De unieke naam van de toepassingsrol. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke rol. Basistypenamen zijn gereserveerd. U een toepassingsrol met dezelfde naam als Type niet een naam [geven](#type)| Ja | 50 |
| Beschrijving | Beschrijving van de toepassingsrol. | Nee | 255 |

### <a name="application-roles-example"></a>Voorbeeld van toepassingsrollen

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
## <a name="identifiers"></a>Identificatiemiddelen

Id's vertegenwoordigen een verzameling informatie die wordt gebruikt om werkstroomeigenschappen, constructor- en functieparameters te beschrijven. 

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | De unieke naam van de eigenschap of parameter. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke eigenschap of parameter. | Ja | 50 |
| DisplayName | Vriendelijke weergavenaam voor de eigenschap of parameter. | Ja | 255 |
| Beschrijving | Beschrijving van de eigenschap of parameter. | Nee | 255 |
| Type | Type [eigenschappengegevens](#type). | Ja |

### <a name="identifiers-example"></a>Voorbeeld van id's

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

## <a name="configuration-file-example"></a>Voorbeeld van configuratiebestand

Overdracht van activa is een slim contractscenario voor het kopen en verkopen van waardevolle activa, waarvoor een inspecteur en taxateur vereist zijn. Verkopers kunnen hun activa aanbieden door een slim contract voor assettransfer te instantiën. Kopers kunnen aanbiedingen doen door een actie te ondernemen op het slimme contract, en andere partijen kunnen acties ondernemen om het actief te inspecteren of te beoordelen. Zodra het actief is gemarkeerd, zowel geïnspecteerd als beoordeeld, zullen de koper en verkoper de verkoop opnieuw bevestigen voordat het contract is voltooid. Op elk punt in het proces hebben alle deelnemers inzicht in de status van het contract terwijl het wordt bijgewerkt. 

Zie [voorbeeld van assettransfer voor Azure Blockchain Workbench voor](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer) meer informatie, waaronder de codebestanden.

Het volgende configuratiebestand is voor het voorbeeld van assettransfer:

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

