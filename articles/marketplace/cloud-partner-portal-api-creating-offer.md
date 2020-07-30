---
title: Een aanbieding maken of wijzigen-Azure Marketplace
description: API voor het maken van een nieuwe of het bijwerken van een bestaande aanbieding.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420223"
---
# <a name="create-or-modify-an-offer"></a>Een aanbieding toevoegen of veranderen

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Met deze oproep wordt een specifieke aanbieding binnen de naam ruimte van de uitgever bijgewerkt of wordt een nieuwe aanbieding gemaakt.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-para meters

|  **Naam**         |  **Beschrijving**                      |  **Gegevenstype**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  Uitgevers-id, bijvoorbeeld`contoso` |   Tekenreeks |
| offerId           |  Aanbiedings-id                     |   Tekenreeks        |
| api-versie       |  Nieuwste versie van de API            |   Date           |
|  |  |  |

## <a name="header"></a>Header

|  **Naam**        |  **Waarde**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| Autorisatie    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>Voor beeld van tekst

In het volgende voor beeld wordt een aanbieding met offerID van gemaakt `contosovirtualmachine` .

### <a name="request"></a>Aanvraag

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Reactie

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> Als u deze aanbieding wilt wijzigen, voegt u een **if-match-** header toe aan * op de bovenstaande aanvraag. Gebruik dezelfde hoofd tekst als hierboven, maar wijzig de waarden naar wens. 

### <a name="response-status-codes"></a>Antwoord status codes

| **Code**  |  **Beschrijving**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. De aanvraag is verwerkt en de aanbieding is gewijzigd.           |
|  201      | `Created`. De aanvraag is verwerkt en de aanbieding is gemaakt.   |
|  400      | `Bad/Malformed request`. De hoofd tekst van het fout bericht kan meer informatie geven.            |
|  403      | `Forbidden`. De client heeft geen toegang tot de aangevraagde naam ruimte.                     |
|  404      | `Not found`. De entiteit waarnaar wordt verwezen door de client, bestaat niet.                           |
|  412      | De server voldoet niet aan een van de voor waarden die de aanvrager in de aanvraag heeft opgegeven. De client moet controleren of de ETAG met de aanvraag is verzonden. |
|  |  |

## <a name="uploading-artifacts"></a>Artefacten uploaden

Artefacten, zoals afbeeldingen en logo's, moeten worden gedeeld door ze te uploaden naar een toegankelijke locatie op het web en vervolgens als een URI in de PUT-aanvraag, zoals in het bovenstaande voor beeld. Het systeem detecteert dat deze bestanden niet aanwezig zijn in de Azure Marketplace-opslag en downloaden deze bestanden niet naar opslag.  Als gevolg hiervan zult u merken dat toekomstige GET-aanvragen een URL voor de Azure Marketplace-service retour neren voor deze bestanden.

## <a name="categories-and-industries"></a>Categorieën en industrieën

Wanneer u een nieuwe aanbieding maakt, moet u in Marketplace een categorie voor uw aanbieding opgeven. Voor sommige aanbiedings typen kunt u eventueel ook branches opgeven. Op basis van het aanbiedings type geeft u de categorieën/branches op die van toepassing zijn op de aanbieding met behulp van specifieke sleutel waarden uit de volgende tabellen.

### <a name="azure-marketplace-categories"></a>Azure Marketplace-Categorieën

Deze categorieën en de bijbehorende sleutels zijn van toepassing op Azure-apps, Virtual Machines, kern Virtual Machines, containers, container-apps, IoT Edge modules en SaaS-aanbiedings typen. Items die cursief worden weer gegeven (zoals ***Analytics***), zijn categorieën en standaard tekst items (zoals gegevens inzichten) worden hieronder subcategorieën. Gebruik de exacte sleutel waarden zonder de afstand of het hoofdletter gebruik te wijzigen.

| Categorie | SaaS-sleutels | Azure-app sleutels | Virtuele machine, containers, container-apps, IoT Edge module, sleutels van de kern virtuele machine |
| --- | --- | --- | --- |
| ***Analyse*** | ***analyse*** | ***Analytics-Azure-apps*** | ***analyse-amp*** |
| Gegevens inzichten | gegevens-inzichten | gegevens-inzichten | gegevens-inzichten |
| Gegevensanalyse | gegevens analyse | gegevens analyse | gegevens analyse |
| Big data | Big Data | bigData | Big Data |
| Predictive analytics | voorspellende analyse | voorspellende analyse | voorspellende analyse |
| Analyse in realtime/streamen | Realtime-Streaming-analyses | Realtime-Streaming-analyses | Realtime-Streaming-analyses |
| Overig | andere | overige-analyses | andere |
| ***AI + Machine Learning*** | ***ArtificialIntelligence*** | ***AI-plus-machine-learning*** | ***AI-plus-machine-learning*** |
| Bot-Services | bot-Services | bot-Services | bot-Services |
| Cognitive Services | cognitieve Services | cognitieve Services | cognitieve Services |
| ML-service | ml-service | ml-service | ml-service |
| Geautomatiseerde machine learning | automatische ml | automatische ml | automatische ml |
| Automatisering van zakelijke/geautomatiseerde processen | Business-automatiseerd proces-automatisering | Business-automatiseerd proces-automatisering | Business-automatiseerd proces-automatisering |
| Gegevens etiket tering | gegevens labelen | gegevens labelen | gegevens labelen |
| Gegevensvoorbereiding | gegevens voorbereiding | gegevens voorbereiding | gegevens voorbereiding |
| Kennis analyse | kennis-analyse | kennis-analyse | kennis-analyse |
| ML-bewerkingen | ml-bewerkingen | ml-bewerkingen | ml-bewerkingen |
| Overig | overige-AI-plus-machine-learning | andere | andere |
| ***Blockchain*** | ***Block Chain*** | ***Block Chain*** | ***Block Chain*** |
| App-Accelerators | app-Accelerators | app-Accelerators | app-Accelerators |
| Groot boek met één knoop punt | Eén knoop punt-groot boek | Eén knoop punt-groot boek | Eén knoop punt-groot boek |
| Meerdere knoop punten in het groot boek | meerdere knoop punten-groot boek | meerdere knoop punten-groot boek | meerdere knoop punten-groot boek |
| Hulpprogramma's | tools | tools | tools |
| Overig | andere | andere | andere |
| ***Compute*** | ***compute-SaaS*** | ***compute-Azure-apps*** | ***bepalen*** |
| Toepassings infrastructuur | appInfra | appInfrastructure | toepassings infrastructuur |
| Besturingssystemen | clientOS | clientOS | besturings systemen |
| Cache | cache | cache | cache |
| Overig | andere compute | andere compute | andere |
| ***Containers*** | ***containers*** | ***containers*** | ***containers*** |
| Container-apps | container-apps | container-apps | container-apps |
| Container installatie kopieën | container-installatie kopieën | container-installatie kopieën | container-installatie kopieën |
| Aan de slag met containers | Get-Started-with-containers | Get-Started-with-containers | Get-Started-with-containers |
| Overig | andere | andere | andere |
| ***Databases*** | ***data bases-SaaS*** | ***enddatabase*** | ***vinden*** |
| NoSQL-data bases | nosql-data bases | nosql-data bases | nosql-data bases |
| Relationele databases | relationele data bases | relationele data bases | relationele data bases |
| Groot boek-Block chain data bases | groot boek-Block chain-data bases | groot boek-Block chain-data bases | groot boek-Block chain-data bases |
| Data meren | Data-meren | Data-meren | Data-meren |
| Data Warehouse | Data Warehouse | Data Warehouse | Data Warehouse |
| Overig | andere-data bases | andere-data bases | andere |
| ***Hulpprogramma's voor ontwikkelaars*** | ***Ontwikkel aars-hulp middelen-SaaS*** | ***Ontwikkel aars-hulpprogram ma's-Azure-apps*** | ***Ontwikkel aars-hulpprogram ma's*** |
| Hulpprogramma's | hulpprogram ma's-hulpprogram ma's voor ontwikkel aars | hulpprogram ma's-hulpprogram ma's voor ontwikkel aars | hulpprogram ma's-hulpprogram ma's voor ontwikkel aars |
| Scripts | scriptmap | scriptmap | scriptmap |
| Ontwikkelaars service | devService | devService | Developer-Service |
| Overig | overige-ontwikkel hulpprogramma's | overige-ontwikkel hulpprogramma's | andere |
| ***DevOps*** | ***devops*** | ***devops*** | ***devops*** |
| Overig | andere | andere | andere |
| ***Identiteit*** | ***identity*** | ***identity*** | ***identity*** |
| Toegangsbeheer | Toegangs beheer | Toegangs beheer | Toegangs beheer |
| Overig | andere | andere | andere |
| ***Integratie*** | ***opneming*** | ***opneming*** | ***opneming*** |
| Berichten | mede | mede | mede |
| Overig | andere | andere | andere |
| ***Internet of Things*** | ***IoT*** | ***Internet-der-dingen-Azure-apps*** | ***Internet-der-dingen*** |
| IoT-kern Services | N.v.t. | IOT-Core-Services | IOT-Core-Services |
| IoT Edge modules | N.v.t. | IOT-Edge-modules | IOT-Edge-modules |
| IoT-oplossingen | IOT-oplossingen | IOT-oplossingen | IOT-oplossingen |
| Visualisatie van gegevens analyse & | gegevens analyse-en-visualisatie | gegevens analyse-en-visualisatie | gegevens analyse-en-visualisatie |
| IoT-connectiviteit | IOT-connectiviteit | IOT-connectiviteit | IOT-connectiviteit |
| Overig | overige-Internet-van-dingen | overige-Internet-van-dingen | andere |
| ***&-beheer Hulpprogramma's*** | ***ITandAdministration*** | ***IT-en-management-tools-Azure-apps*** | ***IT-en-beheer-hulpprogram ma's*** |
| Beheeroplossingen | beheer-oplossingen | beheer-oplossingen | beheer-oplossingen |
| Zakelijke toepassingen | businessApplication | businessApplication | zakelijke toepassingen |
| Overig | andere hulpprogram ma's voor IT-beheer | andere hulpprogram ma's voor IT-beheer | andere |
| ***& diagnostische gegevens controleren*** | ***bewaking en diagnostische gegevens*** | ***bewaking en diagnostische gegevens*** | ***bewaking en diagnostische gegevens*** |
| Overig | andere | andere | andere |
| ***Media*** | ***tussenliggend*** | ***tussenliggend*** | ***tussenliggend*** |
| Media Services | media-services | media-services | media-services |
| Content Protection | beveiliging van inhoud | beveiliging van inhoud | beveiliging van inhoud |
| Live & on-demand streaming | Live-and-on-demand-streaming | Live-and-on-demand-streaming | Live-and-on-demand-streaming |
| Overig | andere | andere | andere |
| ***Migratie*** | ***virtuelemachinemigratie*** | ***virtuelemachinemigratie*** | ***virtuelemachinemigratie*** |
| Gegevensmigratie | gegevens migratie | gegevens migratie | gegevens migratie |
| Overig | andere | andere | andere |
| ***Mixed Reality*** | ***Mixed-Reality*** | ***Mixed-Reality*** | ***Mixed-Reality*** |
| Overig | andere | andere | andere |
| ***Netwerken*** | ***inbel*** | ***inbel*** | ***inbel*** |
| Toestel beheerders | apparaat-managers | apparaat-managers | apparaat-managers |
| Connectiviteit | connectiviteit | connectiviteit | connectiviteit |
| Firewalls | brand | brand | brand |
| Load balancers | load balancers | load balancers | load balancers |
| Overig | andere | andere | andere |
| ***Beveiliging*** | ***beveiligingsprincipal*** | ***beveiligingsprincipal*** | ***beveiligingsprincipal*** |
| Toegangs beheer voor identiteits & | identiteits-en toegangs beheer | identiteits-en toegangs beheer | identiteits-en toegangs beheer |
| Bedreigingsbeveiliging | bedreiging beveiliging | bedreiging beveiliging | bedreiging beveiliging |
| Information Protection | gegevens beveiliging | gegevens beveiliging | gegevens beveiliging |
| Overig | andere | andere | andere |
| ***Storage*** | ***opslag-SaaS*** | ***opslag-Azure-apps*** | ***opslagpad*** |
| Back-up & herstellen | Back-up | Back-up | back-up en herstel |
| Hybride opslag voor ondernemingen | Enter prise-Hybrid-Storage | Enter prise-Hybrid-Storage | Enter prise-Hybrid-Storage |
| Bestanden delen | bestanden delen | bestanden delen | bestanden delen |
| Beheer van gegevens levenscyclus | gegevens levenscyclus beheer | gegevens levenscyclus beheer | gegevens levenscyclus beheer |
| Overig | andere opslag | andere opslag | andere |
| ***Web*** | ***instellingen*** | ***instellingen*** | ***instellingen*** |
| Blogs & CMSs | blogs-en-CMSS | blogs-en-CMSS | blogs-en-CMSS |
| Starter Web Apps | Starter-web-apps | Starter-web-apps | Starter-web-apps |
| Commerce | Commerce | Commerce | Commerce |
| Web Apps Frameworks | Web-apps-Frameworks | Web-apps-Frameworks | Web-apps-Frameworks |
| Web Apps | Web-apps | Web-apps | Web-apps |
| Overig | andere | andere | andere |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource Categorieën

Deze categorieën en de bijbehorende sleutels zijn van toepassing op SaaS-, Power bi-apps, Dynamics 365 Business Central, Dynamics 365 voor klant betrokkenheid en Dynamics 365 voor typen bewerkings aanbiedingen. Items die cursief worden weer gegeven (zoals ***Analytics***) zijn categorieën en standaard tekst items (zoals geavanceerde analyse) zijn subcategorieën daaronder. Gebruik de exacte sleutel waarden zonder de afstand of het hoofdletter gebruik te wijzigen.

| Categorie | SaaS-sleutels | Dynamics 365 Business Central, Dynamics 365 voor klant betrokkenheid, Dynamics 365 voor bewerkings sleutels | Power bi-app-sleutels |
| --- | --- | --- | --- |
| ***Analyse*** | ***analyse*** | ***Analyse*** | ***Analyse*** |
| Geavanceerde analyse | Geavanceerd-analyse | Geavanceerd-analyse | Geavanceerd-analyse |
| Visualisatie & rapportage | visualisatie-rapportage | visualisatie-rapportage | visualisatie-rapportage |
| Overig | andere | overige-analyses | overige-analyses |
| ***AI + Machine Learning*** | ***ArtificialIntelligence*** | ***AI-plus-machine learning-Dynamics*** | ***AI-plus-machine learning-appsource*** |
| AI voor bedrijven | AI-for-Business | AI-for-Business | AI-for-Business |
| Bot-apps | bot-apps | bot-apps | bot-apps |
| Overig | overige-AI-plus-machine-learning | overige-AI-plus-machine-learning | overige-AI-plus-machine-learning |
| ***Samenwerking*** | ***Samenwerking*** | ***Samenwerking*** | ***werking*** |
| Contact opnemen met & personen | contact persoon-personen | contact persoon-personen | contact persoon-en-personen |
| Beheer van vergaderingen | vergadering-beheer | vergadering-beheer | vergadering-beheer |
| Beheer van site ontwerp & | site-ontwerp-beheer | site-ontwerp-beheer | site-ontwerp-en-beheer |
| Taak & project beheer | taak-Project-beheer | taak-Project-beheer | taak-en project beheer |
| Video vergaderingen met spraak & | spraak-video vergaderingen | spraak-video vergaderingen | spraak-en video vergaderingen |
| Overig | overige samen werking | overige samen werking | andere |
| ***Naleving & juridisch*** | ***acht*** | ***acht*** | ***naleving-en-juridisch*** |
| Controle van belasting & | belasting controle | belasting controle | belasting en controle |
| Juridisch | Juridisch | Juridisch | rechtmatig |
| Gegevens, het governance & privacy | Data-Governance-privacy | Data-Governance-privacy | Data-Governance-en-privacy |
| Gezondheids & veiligheid | gezondheids veiligheid | gezondheids veiligheid | status en veiligheid |
| Overig | overige-naleving-juridisch | overige-naleving-juridisch | andere |
| ***Customer Service*** | ***CustomerService*** | ***CustomerService*** | ***klant-service*** |
| Contact centrum | contact persoon-centrum | contact persoon-centrum | contact persoon-centrum |
| Gezicht to face-service | face-to-face-service | face-to-face-service | face-to-face-service |
| Back-up van Office & werknemers service | Back-Office-werk nemer-service | Back-Office-werk nemer-service | Back-Office-and-employee-service |
| Case beheer kennis & | kennis-Case-beheer | kennis-Case-beheer | kennis-en case beheer |
| Omnichannel-betrokkenheid van sociale media & | sociale media-omnichannel-engagement | sociale media-omnichannel-engagement | sociale media-en-omnichannel-betrokkenheid |
| Overig | overige-klanten service | overige-klanten service | andere |
| ***Financieel*** | ***Financieel*** | ***Financieel*** | ***projectfinancierings*** |
| Boekhouding | boekhouder | boekhouder | boekhouder |
| Asset-management | Asset-Management | Asset-Management | Asset-Management |
| Analyse, consolidatie & rapportage | Analytics-consolidatie-rapportage | Analytics-consolidatie-rapportage | analyse-consolidatie en rapportage |
| Tegoed & verzamelingen | Credit verzamelingen | Credit verzamelingen | tegoeden en verzamelingen |
| Nalevings & risico beheer | naleving-risico beheer | naleving-risico beheer | naleving en risico beheer |
| Overig | overige Financiën | overige Financiën | andere |
| ***Human Resources*** | ***Human*** | ***Human*** | ***Human resources*** |
| Verwerving van talen | talen aan de verwerving | talen aan de verwerving | talen aan de verwerving |
| Talen beheer | talen beheer | talen beheer | talen beheer |
| HR-bewerkingen | HR-bewerkingen | HR-bewerkingen | HR-bewerkingen |
| Personeel planning & Analytics | personeel-planning-Analytics | personeel-planning-Analytics | personeel-planning en analyse |
| Overig | overige-human resources | overige-human resources | andere |
| ***Internet of Things*** | ***IoT*** | ***Internet-van-dingen-Dynamics*** | ***Internet-der-dingen-appsource*** |
| & bewerkingen voor beheer van bedrijfs middelen | Asset-Management-Operations | Asset-Management-Operations | Asset-Management-and-Operations |
| Verbonden producten | verbonden-producten | verbonden-producten | verbonden-producten |
| Intelligente toeleverings keten | Intelligent-supply-keten | Intelligent-supply-keten | Intelligent-supply-keten |
| Predictief onderhoud | voor speld onderhoud | voor speld onderhoud | voor speld onderhoud |
| Externe bewaking | externe controle | externe controle | externe controle |
| Veiligheids & beveiliging | veiligheid: beveiliging | veiligheid: beveiliging | veiligheid-en-beveiliging |
| Slimme infrastructuur resources & | slimme infra structuur-bronnen | slimme infra structuur-bronnen | slimme infra structuur en bronnen |
| Mobiele & mobiliteit | Voer tuigen-mobiliteit | Voer tuigen-mobiliteit | Voer tuigen en mobiliteit |
| Overig | overige-Internet-van-dingen | overige-Internet-van-dingen | andere |
| ***&-beheer Hulpprogramma's*** | ***ITandAdministration*** | ***ITandAdministration*** | ***IT-en-beheer-hulpprogram ma's*** |
| Beheeroplossingen | beheer-oplossingen | beheer-oplossingen | beheer-oplossingen |
| Zakelijke toepassingen | businessApplication | businessApplication | zakelijke toepassingen |
| Overig | andere hulpprogram ma's voor IT-beheer | andere hulpprogram ma's voor IT-beheer | andere |
| ***Marketing*** | ***Marketing*** | ***Marketing*** | ***Marketing*** |
| Bekendmaking | bekendmaking | bekendmaking | bekendmaking |
| Analyse | analyse-marketing | analyse-marketing | analyse-marketing |
| & automatisering van campagne beheer | campagne-beheer-automatisering | campagne-beheer-automatisering | campagne-beheer-en-automatisering |
| E-mail marketing | e-mail-marketing | e-mail-marketing | e-mail-marketing |
| L2-gebeurtenissen & resource beheer | gebeurtenissen-bron beheer | gebeurtenissen-bron beheer | gebeurtenissen-en-bron beheer |
| Onderzoek & analyse | onderzoek-analyse | onderzoek-analyse | onderzoek-en-analyse |
| Sociale media | sociale media | sociale media | sociale media |
| Overig | overige marketing | overige marketing | andere |
| ***Toeleverings keten van operations-&*** | ***OperationsSupplyChain*** | ***OperationsSupplyChain*** | ***Operations-and-supply-keten*** |
| Activum & productie beheer | activa-productie-beheer | activa-productie-beheer | activa en productie-beheer |
| Vraagprognose | vraag-prognose | vraag-prognose | vraag-prognose |
| & connectiviteit voor informatie beheer | informatie-beheer-connectiviteit | informatie-beheer-connectiviteit | informatie-beheer en connectiviteit |
| &-rapportage plannen, kopen | planning-inkoop-rapportage | planning-inkoop-rapportage | planning: aankoop en rapportage |
| Quality &-Service beheer | Quality-Service-beheer | Quality-Service-beheer | kwaliteits-en Service beheer |
| Order beheer voor verkoop & | verkoop order-beheer | verkoop order-beheer | verkoop-en bestel beheer |
| Trans Port-& magazijn beheer | Trans Port-Warehouse-Management | Trans Port-Warehouse-Management | Trans Port-en-Warehouse-beheer |
| Overig | overige-bewerkingen-toeleverings keten | overige-bewerkingen-toeleverings keten | andere |
| ***Productiviteit*** | ***Productiviteit*** | ***Productiviteit*** | ***productiviteit*** |
| & beheer voor het maken van inhoud | content-Creating-Management | content-Creating-Management | inhoud maken en beheren |
| Vertaling van taal & | taal omzetting | taal omzetting | taal en vertaling |
| Documentbeheer | document beheer | document beheer | document beheer |
| E-mail beheer | e-mail beheer | e-mail beheer | e-mail beheer |
| Naslag informatie over & zoeken | Zoek verwijzing | Zoek verwijzing | zoeken en naslag informatie |
| Overig | overige-productiviteit | overige-productiviteit | andere |
| Gamificatie | Gamificatie | Gamificatie | gamificatietoepassing |
| ***Sales*** | ***Sales*** | ***Sales*** | ***Sales*** |
| Televerkoop | televerkoop | televerkoop | televerkoop |
| Configure, Price, quote (CPQ) | configureren-prijs opgave | configureren-prijs opgave | configureren-prijs opgave |
| Contractbeheer | contract-beheer | contract-beheer | contract-beheer |
| VERPLAATST | crm | crm | crm |
| E-commerce | e-commerce | e-commerce | e-commerce |
| Verrijking van zakelijke gegevens | Business-Data-verrijking | Business-Data-verrijking | Business-Data-verrijking |
| Verkoop activering | verkoop-activering | verkoop-activering | verkoop-activering |
| Overig | overige-verkoop | overige-verkoop | overige-verkoop |
| ***Geolocatie*** | ***geolocatie*** | ***geolocatie*** | ***geolocatie*** |
| Kaarten | maps | maps | maps |
| Nieuws & weer | Nieuws-en-weer | Nieuws-en-weer | Nieuws-en-weer |
| Overig | andere geolocatie | andere geolocatie | andere geolocatie |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource branches

Deze branches en hun respectieve sleutels zijn van toepassing op SaaS-, Power bi-apps, Dynamics 365 Business Central, Dynamics 365 voor klant betrokkenheid en Dynamics 365 voor typen bewerkings aanbiedingen. Items die cursief worden weer gegeven (zoals ***auto's***) zijn categorieën en standaard tekst items (zoals AutomotiveL2) zijn subcategorieën daaronder. Gebruik de exacte sleutel waarden zonder de afstand of het hoofdletter gebruik te wijzigen.

| Branche | SaaS, Dynamics 365 Business Central, Dynamics 365 voor klant betrokkenheid, Dynamics 365 voor bewerkings sleutels | Power bi-apps-sleutels |
| --- | --- | --- |
| ***Auto's*** | ***Auto's*** | ***Motor*** |
| Auto's | AutomotiveL2 | AutomotiveL2 |
| ***Landbouw*** | ***Landbouw*** | ***landbouw*** |
| Overige-niet-gesegmenteerd | OtherUnsegmented land bouw \_ | overige-niet-gesegmenteerd |
| ***Distributie*** | ***Distributie*** | ***deel*** |
| Handels | Handels | handels |
| Pakket verzending van Parcel & | ParcelAndPackageShipping | pakje-en-package-Shipping |
| ***Onderwijs*** | ***Onderwijs*** | ***personeel*** |
| Hoger onderwijs | HigherEducation | hoger onderwijs |
| Primair & secundair onderwijs/K-12 | PrimaryAndSecondaryEducationK12 | primair en secundair onderwijs |
| Bibliotheken & musea | LibrariesAndMuseums | bibliotheken-en-musea |
| ***Financiële diensten*** | ***FinancialServices*** | ***Financial-Services*** |
| Bank & kapitaal markten | BankingAndCapitalMarkets | Bank-en kapitaal markten |
| Polis | Polis | polis |
| ***Overheid*** | ***Overheid*** | ***Government*** |
| Verdediging & Intelligence | DefenseAndIntelligence | verdediging en intelligentie |
| Open bare veiligheid & Justitie | PublicSafetyAndJustice | openbaar-veiligheid-en rechtvaardigheid |
| Burger overheid | CivilianGovernment | burger-Government |
| ***Gezondheidszorg*** | ***HealthCareandLifeSciences*** | ***gezondheids zorg*** |
| Status betaler | HealthPayor | status-betaler |
| Health-provider | HealthProvider | Health-provider |
| Pharmaceuticals | Pharmaceuticals | Pharmaceuticals |
| ***Resources voor productie &*** | ***Productie*** | ***productie-en-resources*** |
| Chemische & Agrochemical | ChemicalAndAgrochemical | chemisch en Agrochemical |
| Discrete productie | DiscreteManufacturing | discrete productie |
| Energie | Energie | energy |
| ***Retail & consumenten goederen*** | ***RetailandConsumerGoods*** | ***Retail-en consumenten goederen*** |
| Consumenten goederen | ConsumerGoods | consumenten goederen |
| Handelaren | Handelaren | handelaren |
| ***Media & communicatie*** | ***MediaAndCommunications*** | ***media-en-communicatie*** |
| Media & entertainment | MediaandEntertainment | media-en entertainment |
| Telecommunicatie | Telecommunicatie | Telecom |
| ***Professionele services*** | ***ProfessionalServices*** | ***professionele services*** |
| Juridisch | Juridisch | rechtmatig |
| Partner Professional-Services | PartnerProfessionalServices | partner-Professional-Services |
| ***Architectuur & constructie*** | ***ArchitectureAndConstruction*** | ***architectuur-en-constructie*** |
| Overige-niet-gesegmenteerd | ArchitectureAndConstruction \_ OtherUnsegmented | overige-niet-gesegmenteerd |
| ***Horeca & reizen*** | ***HospitalityandTravel*** | ***horeca*** |
|    Hotels & vrije tijd | HotelsAndLeisure | Hotels-en-vrije tijd |
| Reis & vervoer | TravelAndTransportation | reizen en transport |
| Restaurants & voedsel Services | RestaurantsAndFoodServices | restaurants en voedsel Services |
| ***Andere branches uit de open bare sector*** | ***OtherPublicSectorIndustries*** | ***overige-open bare sector-branches*** |
| Bosbouw & visserij | ForestryAndFishing | bos en visserij |
| Profit organisaties | Profit organisaties | profit organisaties |
| ***Onroerend goed*** | ***RealEstate*** | ***onroerend goed*** |
| Overige-niet-gesegmenteerd | RealEstate \_ OtherUnsegmented | overige-niet-gesegmenteerd |
|||
