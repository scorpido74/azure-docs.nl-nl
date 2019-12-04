---
title: Schema-updates voor de preview-versie van augustus-1-2015
description: De schema versie 2015-08-01-preview is bijgewerkt voor de definities van logische apps in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792839"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Schema-updates voor Azure Logic Apps-1 augustus 2015 preview

Deze schema-en API-versie voor Azure Logic Apps bevat belang rijke verbeteringen die het gebruik van logische apps betrouwbaarder en eenvoudiger maken:

* Het actie type **APIApp** heet nu [**APIConnection**](#api-connections).
* De **herhalings** actie heet nu [**foreach**](#foreach).
* De [ **HTTP-listener API-** app](#http-listener) is niet meer vereist.
* Voor het aanroepen van onderliggende werk stromen wordt een [Nieuw schema](#child-workflows)gebruikt.

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Verplaatsen naar API-verbindingen

De grootste wijziging is dat u API Apps niet meer hoeft te implementeren in uw Azure-abonnement, zodat u Api's kunt gebruiken. Hier volgen de manieren waarop u Api's kunt gebruiken:

* Beheerde Api's
* Uw aangepaste web-Api's

Elke manier wordt enigszins anders behandeld, omdat hun beheer-en hosting modellen verschillend zijn. Een voor deel van dit model is dat u niet langer beperkt bent over resources die zijn geïmplementeerd in uw Azure-resource groep. 

### <a name="managed-apis"></a>Beheerde Api's

Micro soft beheert enkele Api's namens u, zoals Office 365, Sales Force, Twitter en FTP. U kunt een aantal beheerde Api's gebruiken, zoals Bing vertalen, terwijl andere moeten worden geconfigureerd, ook wel een *verbinding*.

Wanneer u bijvoorbeeld Office 365 gebruikt, moet u een verbinding maken die uw Office 365-aanmeldings token bevat. Uw token wordt veilig opgeslagen en vernieuwd zodat uw logische app altijd de Office 365-API kan aanroepen. Als u verbinding wilt maken met uw SQL-of FTP-server, moet u een verbinding maken die de connection string heeft. 

In deze definitie worden deze acties de naam `APIConnection`genoemd. Hier volgt een voor beeld van een verbinding die Office 365 aanroept voor het verzenden van een e-mail bericht:

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

Het `host`-object maakt deel uit van de invoer die uniek is voor API-verbindingen en bevat de volgende onderdelen: `api` en `connection`. Het `api`-object geeft de runtime-URL op waar de beheerde API wordt gehost. U kunt alle beschik bare beheerde Api's weer geven door deze methode aan te roepen:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Wanneer u een API gebruikt, is het mogelijk dat de API geen *verbindings parameters*heeft gedefinieerd. Als deze para meters niet worden gedefinieerd in de API, is geen verbinding vereist. Als deze para meters in de API worden gedefinieerd, moet u een verbinding maken met een opgegeven naam.  
U verwijst vervolgens naar die naam in het `connection`-object binnen het `host`-object. Als u een verbinding in een resource groep wilt maken, roept u de volgende methode op:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Met de volgende hoofd tekst:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Beheerde Api's implementeren in een Azure Resource Manager sjabloon

Als interactieve aanmelding niet is vereist, kunt u een volledige app maken met behulp van een resource manager-sjabloon.
Als aanmelding vereist is, kunt u nog steeds een resource manager-sjabloon gebruiken, maar u moet de verbindingen via de Azure Portal autoriseren. 

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

U kunt in dit voor beeld zien dat de verbindingen alleen resources zijn die zich in uw resource groep bevinden. Ze verwijzen naar de beheerde Api's die voor u beschikbaar zijn in uw abonnement.

### <a name="your-custom-web-apis"></a>Uw aangepaste web-Api's

Als u uw eigen Api's gebruikt in plaats van door micro soft beheerde, gebruikt u de ingebouwde **http** -actie om uw api's aan te roepen. In het ideale geval moet u een Swagger-eind punt voor uw API opgeven. Dit eind punt helpt Logic app Designer de invoer en uitvoer van uw API weer te geven. Zonder een Swagger-eind punt kan de ontwerp functie alleen de invoer en uitvoer weer geven als ondoorzichtige JSON-objecten.

Hier volgt een voor beeld van de nieuwe eigenschap `metadata.apiDefinitionUrl`:

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

Als u uw web-API op Azure App Service host, wordt uw web-API automatisch weer gegeven in de lijst met acties die beschikbaar zijn in de ontwerp functie. Als dat niet het geval is, moet u de URL rechtstreeks in een plak bewerking plakken. Het Swagger-eind punt moet niet-geverifieerd zijn zodat het kan worden gebruikt in de ontwerp functie voor logische apps, hoewel u de API zelf kunt beveiligen met alle methoden die Swagger ondersteunt.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Geïmplementeerde API-apps aanroepen met 2015-08-01-preview

Als u eerder een API-app hebt geïmplementeerd, kunt u die app aanroepen met de **http-** actie.
Als u bijvoorbeeld Dropbox gebruikt om bestanden weer te geven, kan de schema versie definitie van uw **2014-12-01-voor beeld** er ongeveer als volgt uitzien:

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

Nu kunt u nu een vergelijk bare HTTP-actie maken en de `parameters` sectie van de Logic app-definitie ongewijzigd laten, bijvoorbeeld:

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

Deze eigenschappen één voor één door lopen:

| Actie-eigenschap | Beschrijving |
| --- | --- |
| `type` | `Http` in plaats van `APIapp` |
| `metadata.apiDefinitionUrl` | Als u deze actie wilt gebruiken in de ontwerp functie voor logische apps, neemt u het eind punt voor meta gegevens op, dat is samengesteld uit: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Samengesteld uit: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Altijd `POST` |
| `inputs.body` | Hetzelfde als de API-app-para meters |
| `inputs.authentication` | Hetzelfde als de API-app-verificatie |

Deze aanpak moet worden gebruikt voor alle API-app-acties. Houd er echter rekening mee dat deze vorige API Apps niet meer worden ondersteund. Daarom moet u overstappen op een van de twee andere eerdere opties, een beheerde API of uw aangepaste web-API hosten.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>' Repeat ' is gewijzigd in ' foreach '

Voor de vorige schema versie hebben we veel feedback van klanten ontvangen dat de naam van de **Herhaal** actie verwarrend was en niet goed werd vastgelegd en dat **herhalen** echt een voor-elke lus was. We hebben de naam van `repeat` gewijzigd in `foreach`. U zou deze actie eerder moeten schrijven, zoals in dit voor beeld:

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

Nu zou u deze versie in plaats daarvan schrijven:

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

De `repeatItem()` functie, waarnaar wordt verwezen naar het item dat de lus verwerkt tijdens de huidige iteratie, is nu hernoemd `item()`. 

### <a name="reference-outputs-from-foreach"></a>Referentie-uitvoer van ' foreach '

Voor vereenvoudiging worden de uitvoer van `foreach` acties niet langer verpakt in een object met de naam `repeatItems`. Daarnaast worden de functies `repeatItem()`, `repeatBody()`en `repeatOutputs()` verwijderd.

Met het voor gaande `repeat`-voor beeld krijgt u de volgende uitvoer:

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

Nu krijgt u deze uitvoer in plaats daarvan:

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

Als u de `body` van de actie wilt ophalen bij het verwijzen naar deze uitvoer, gaat u als volgt te werk:

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

Nu kunt u in plaats daarvan deze versie gebruiken:

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

## <a name="native-http-listener"></a>Systeem eigen HTTP-listener

HTTP-listener-functies zijn nu ingebouwd, dus u hoeft geen HTTP-listener API-app te implementeren. Meer informatie kunt u lezen hoe u [uw logische app-eind punt kunt aanroepen](../logic-apps/logic-apps-http-endpoint.md). 

Met deze wijzigingen wordt Logic Apps de functie `@accessKeys()` vervangen door de functie `@listCallbackURL()`, waarmee het eind punt wordt opgehaald wanneer dat nodig is. U moet nu ook ten minste één trigger definiëren in uw logische app. Als u de werk stroom wilt `/run`, moet u een van de volgende trigger typen gebruiken: `Manual`, `ApiConnectionWebhook`of `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Onderliggende werk stromen aanroepen

Voorheen aanroepen van de onderliggende werk stromen die nodig zijn om naar de werk stroom te gaan, het toegangs token op te halen en het token te plakken in de definitie van de logische app waar u die onderliggende werk stroom wilt aanroepen. Met dit schema genereert de Logic Apps-Engine automatisch een SAS tijdens runtime voor de onderliggende werk stroom, zodat u geen geheimen hoeft te plakken in de definitie. Hier volgt een voorbeeld:

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

Daarnaast krijgen onderliggende werk stromen volledige toegang tot de binnenkomende aanvraag. U kunt daarom para meters door geven in de sectie `queries` en in het `headers`-object. U kunt ook volledig de volledige `body` sectie definiëren.

Ten slotte hebben onderliggende werk stromen deze vereiste wijzigingen. U kunt een onderliggende werk stroom eerder en rechtstreeks aanroepen, maar nu moet u een trigger eindpunt definiëren in de werk stroom voor het bovenliggende item. Over het algemeen voegt u een trigger toe die `Manual` type heeft en die trigger vervolgens gebruiken in de bovenliggende definitie. De eigenschap `host` heeft specifiek een `triggerName` omdat u altijd de trigger moet opgeven die u aanroept.

## <a name="other-changes"></a>Andere wijzigingen

### <a name="new-queries-property"></a>Nieuwe eigenschap query's

Alle actie typen bieden nu ondersteuning voor een nieuwe invoer met de naam `queries`. Deze invoer kan een Structured object zijn, in plaats van de teken reeks hand matig te assembleren.

### <a name="renamed-parse-function-to-json"></a>De naam van de functie parse () is gewijzigd in JSON ()

De naam van de functie `parse()` wordt nu gewijzigd van de functie `json()` voor toekomstige inhouds typen.

## <a name="enterprise-integration-apis"></a>Bedrijfsintegratie-Api's

Dit schema biedt nog geen ondersteuning voor beheerde versies voor Bedrijfsintegratie Api's, zoals AS2. U kunt bestaande geïmplementeerde BizTalk-Api's echter gebruiken via de HTTP-actie. Zie ' uw al geïmplementeerde API-apps gebruiken ' in het [integratie schema](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)voor meer informatie. 
