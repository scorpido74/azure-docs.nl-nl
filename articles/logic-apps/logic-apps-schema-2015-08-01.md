---
title: Schema-updates voor de preview-preview van augustus-1-2015
description: Bijgewerkte schemaversie 2015-08-01-preview voor logische app-definities in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792839"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Schema-updates voor Azure Logic Apps - voorbeeld van 1 augustus 2015

Deze schema- en API-versie voor Azure Logic Apps bevat belangrijke verbeteringen die logische apps betrouwbaarder en gebruiksvriendelijker maken:

* Het **actietype APIApp** heet nu [**APIConnection**](#api-connections).
* De **actie Herhalen** heet nu [**Foreach**](#foreach).
* De [ **HTTP Listener** API App](#http-listener) is niet langer nodig.
* Met onderliggende werkstromen wordt een [nieuw schema gebruikt.](#child-workflows)

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Naar API-verbindingen gaan

De grootste wijziging is dat u API-apps niet langer hoeft te implementeren in uw Azure-abonnement, zodat u API's gebruiken. Dit zijn de manieren waarop u API's gebruiken:

* Beheerde API's
* Uw aangepaste web-API's

Elke manier wordt iets anders behandeld omdat hun management- en hostingmodellen verschillend zijn. Een voordeel van dit model is dat u niet langer beperkt bent tot resources die zijn geïmplementeerd in uw Azure-brongroep. 

### <a name="managed-apis"></a>Beheerde API's

Microsoft beheert namens u enkele API's, zoals Office 365, Salesforce, Twitter en FTP. U sommige beheerde API's gebruiken zoals Bing Translate, terwijl anderen configuratie vereisen, ook wel een *verbinding*genoemd.

Wanneer u bijvoorbeeld Office 365 gebruikt, moet u een verbinding maken met uw office 365-aanmeldingstoken. Uw token wordt veilig opgeslagen en vernieuwd, zodat uw logische app altijd de Office 365 API kan aanroepen. Als u verbinding wilt maken met uw SQL- of FTP-server, moet u een verbinding maken met de verbindingstekenreeks. 

In deze definitie worden `APIConnection`deze acties genoemd . Hier is een voorbeeld van een verbinding die Office 365 oproept om een e-mail te verzenden:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

Het `host` object is een deel van de ingangen dat uniek `api` `connection`is voor API-verbindingen en deze onderdelen bevat: en . Het `api` object geeft de runtime-URL op voor waar die beheerde API wordt gehost. U alle beschikbare beheerde API's bekijken door deze methode aan te roepen:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Wanneer u een API gebruikt, kan die API al dan niet *verbindingsparameters*hebben gedefinieerd. Als de API deze parameters niet definieert, is er dus geen verbinding vereist. Als de API deze parameters definieert, moet u een verbinding maken met een opgegeven naam.  
Vervolgens verwijst u naar `connection` die `host` naam in het object in het object. Als u een verbinding in een resourcegroep wilt maken, roept u deze methode aan:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Met de volgende instantie:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Beheerde API's implementeren in een Azure Resource Manager-sjabloon

Wanneer interactieve aanmelding niet vereist is, u een volledige app maken met behulp van een resourcemanagersjabloon.
Als aanmelden vereist is, u nog steeds een Resource Manager-sjabloon gebruiken, maar u moet de verbindingen autoriseren via de Azure-portal. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

U in dit voorbeeld zien dat de verbindingen slechts bronnen zijn die in uw resourcegroep leven. Ze verwijzen naar de beheerde API's die voor u beschikbaar zijn in uw abonnement.

### <a name="your-custom-web-apis"></a>Uw aangepaste web-API's

Als u uw eigen API's gebruikt in plaats van door Microsoft beheerde API's, gebruikt u de ingebouwde **HTTP-actie** om uw API's aan te roepen. Idealiter moet u een Swagger-eindpunt voor uw API bieden. Met dit eindpunt kan Logic App Designer de ingangen en uitvoer van uw API weergeven. Zonder een Swagger-eindpunt kan de ontwerper alleen de ingangen en uitgangen weergeven als ondoorzichtige JSON-objecten.

Hier is een voorbeeld `metadata.apiDefinitionUrl` van de nieuwe eigenschap:

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Als u uw Web API host in Azure App Service, wordt uw web-API automatisch weergegeven in de lijst met acties die beschikbaar zijn in de ontwerper. Als dit niet het zo is, moet u de URL rechtstreeks plakken. Het Swagger-eindpunt moet niet zijn geverifieerd om bruikbaar te zijn in de Logic App Designer, hoewel u de API zelf beveiligen met alle methoden die Swagger ondersteunt.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Geïmplementeerde API-apps bellen met 2015-08-01-preview

Als u eerder een API-app hebt geïmplementeerd, u die app bellen met de **HTTP-actie.**
Als je bijvoorbeeld Dropbox gebruikt om bestanden weer te geven, kan je schemaversiedefinitie **voor 2014-2012-2012-2012-2011** ongeveer als:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Nu u een vergelijkbare HTTP-actie maken en `parameters` de sectie van de logische app-definitie ongewijzigd laten, bijvoorbeeld:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Wandelen door deze eigenschappen een-voor-een:

| Actie, eigenschap | Beschrijving |
| --- | --- |
| `type` | `Http`In plaats van`APIapp` |
| `metadata.apiDefinitionUrl` | Als u deze actie wilt gebruiken in de Logic App Designer, neemt u het eindpunt met metagegevens op, dat is opgebouwd uit:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Opgebouwd uit:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Altijd`POST` |
| `inputs.body` | Hetzelfde als de API-app-parameters |
| `inputs.authentication` | Hetzelfde als de API-app-verificatie |

Deze aanpak moet werken voor alle API-app-acties. Houd er echter rekening mee dat deze eerdere API-apps niet langer worden ondersteund. Dus je moet verhuizen naar een van de twee andere vorige opties, een beheerde API of hosting van uw aangepaste Web API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Omgedoopt tot 'repeat' naar 'foreach'

Voor de vorige schemaversie kregen we veel feedback van klanten dat de **actienaam Repeat** verwarrend was en niet goed vastte dat **Repeat** echt een voor-elke lus was. Dus hebben we `repeat` `foreach`ons hernoemd naar. Voorheen schreef je deze actie als dit voorbeeld:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Nu zou je deze versie in plaats daarvan schrijven:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

Ook wordt `repeatItem()` de functie, die verwijst naar het item dat de lus `item()`verwerkt tijdens de huidige iteratie, nu anders genoemd . 

### <a name="reference-outputs-from-foreach"></a>Referentie-output van "foreach"

Voor vereenvoudiging worden de `foreach` uitvoer van acties niet langer `repeatItems`verpakt in een object met de naam . Ook met deze wijzigingen `repeatItem()` `repeatBody()`worden `repeatOutputs()` de , , en functies verwijderd.

Dus, met `repeat` behulp van het vorige voorbeeld, krijg je de volgende uitgangen:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Nu krijg je deze uitgangen in plaats daarvan:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Voorheen, om `body` de van de actie bij het verwijzen naar deze uitgangen:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Nu u deze versie in plaats daarvan gebruiken:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Native HTTP-listener

HTTP-listenerfuncties zijn nu ingebouwd, zodat u geen HTTP Listener API-app hoeft te implementeren. Voor meer informatie, meer informatie over hoe [u uw logic app endpoint callable maken.](../logic-apps/logic-apps-http-endpoint.md) 

Met deze wijzigingen vervangt `@accessKeys()` Logic Apps `@listCallbackURL()` de functie door de functie, die het eindpunt krijgt wanneer dat nodig is. U moet nu ook ten minste één trigger definiëren in uw logische app. Als u `/run` de werkstroom wilt, moet u een `Manual` `ApiConnectionWebhook`van deze triggertypen gebruiken: , of`HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Onderliggende werkstromen oproepen

Voorheen vereist het aanroepen van onderliggende werkstromen om naar de werkstroom te gaan, het toegangstoken op te halen en het token te plakken in de definitie van de logische app waar u die onderliggende werkstroom wilt aanroepen. Met dit schema genereert de Logic Apps-engine automatisch een SAS tijdens runtime voor de onderliggende werkstroom, zodat u geen geheimen in de definitie hoeft te plakken. Hier volgt een voorbeeld:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Ook krijgen onderliggende werkstromen volledige toegang tot de binnenkomende aanvraag. U parameters dus `queries` doorgeven in `headers` de sectie en in het object. U ook het `body` hele gedeelte volledig definiëren.

Ten slotte hebben onderliggende werkstromen deze vereiste wijzigingen. Hoewel u eerder en rechtstreeks een onderliggende werkstroom aanroepen, moet u nu een triggereindpunt definiëren in de werkstroom voor de bovenliggende oproep. Over het algemeen voegt u `Manual` een trigger toe die tekst heeft en gebruikt u die trigger vervolgens in de bovenliggende definitie. De `host` eigenschap heeft `triggerName` specifiek een omdat u altijd de trigger moet opgeven die u belt.

## <a name="other-changes"></a>Andere wijzigingen

### <a name="new-queries-property"></a>Nieuwe eigenschap 'queries'

Alle actietypen ondersteunen nu `queries`een nieuwe invoer genaamd . Deze invoer kan een gestructureerd object zijn, in plaats van dat u de tekenreeks met de hand moet monteren.

### <a name="renamed-parse-function-to-json"></a>Omgedoopt tot 'parse()' functie naar 'json()'

De `parse()` functie wordt nu `json()` omgedoopt tot de functie voor toekomstige inhoudstypen.

## <a name="enterprise-integration-apis"></a>API's voor bedrijfsintegratie

Dit schema biedt nog geen ondersteuning voor beheerde versies voor Enterprise Integration API's, zoals AS2. U echter bestaande geïmplementeerde BizTalk-API's gebruiken via de HTTP-actie. Zie 'Uw reeds geïmplementeerde API-apps gebruiken' voor meer informatie in de [integratieroadmap.](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/) 
