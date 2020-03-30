---
title: Automatisch schalen gebruiken om e-mail- en webhook-waarschuwingsmeldingen te verzenden
description: Meer informatie over het gebruik van acties voor automatisch schalen om web-URL's aan te roepen of e-mailmeldingen te verzenden in Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: c82b170bb3801bdc701ed84230db57f5691523ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120688"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Automatische schaalacties gebruiken om e-mail- en webhook-waarschuwingsmeldingen te verzenden in Azure Monitor
In dit artikel ziet u hoe u triggers zo instelt dat u specifieke web-URL's aanroepen of e-mails verzenden op basis van automatisch schalen in Azure.  

## <a name="webhooks"></a>Webhooks
Met webhooks u de Azure-waarschuwingsmeldingen doorsturen naar andere systemen voor nabewerking of aangepaste meldingen. Bijvoorbeeld het routeren van de waarschuwing naar services die een binnenkomend webverzoek kunnen verwerken om sms'jes te verzenden, bugs te registreren, een team op de hoogte te stellen via chat- of berichtenservices, enz. De webhook URI moet een geldig HTTP- of HTTPS-eindpunt zijn.

## <a name="email"></a>Email
E-mail kan worden verzonden naar een geldig e-mailadres. Beheerders en medebeheerders van het abonnement waar de regel wordt uitgevoerd, worden ook op de hoogte gebracht.

## <a name="cloud-services-and-app-services"></a>Cloudservices en App-services
U zich aanmelden via de Azure-portal voor Cloud Services en Server Farms (App Services).

* Kies de **schaal op** metrische statistiek.

![schaal op schaal door](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Schaalsets voor virtuele machines
Voor nieuwere virtuele machines die zijn gemaakt met Resource Manager (Virtual Machine scale sets), u dit configureren met REST API, Resource Manager templates, PowerShell en CLI. Een portal-interface is nog niet beschikbaar.
Wanneer u de SJABLOON REST API of Resource Manager gebruikt, neemt u het element meldingen op in uw [instellingen voor automatisch schalen](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) met de volgende opties.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Veld | Verplicht? | Beschrijving |
| --- | --- | --- |
| Bewerking |ja |waarde moet "Schaal" zijn |
| sendToSubscriptionAdministrator |ja |waarde moet "waar" of "onwaar" zijn |
| sendToSubscriptionCoAdministrators sendToSubscriptionCoAdministrators |ja |waarde moet "waar" of "onwaar" zijn |
| aangepasteE-mails |ja |waarde kan null [] of string array van e-mails zijn |
| webhooks |ja |waarde kan null of geldig Uri |
| serviceUri |ja |een geldige https Uri |
| properties |ja |waarde moet {} leeg zijn of sleutelwaardeparen bevatten |

## <a name="authentication-in-webhooks"></a>Verificatie in webhooks
De webhook kan verifiëren met behulp van token-gebaseerde verificatie, waarbij u de webhook URI opslaat met een token-id als queryparameter. Https bijvoorbeeld:\//mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Autoscale melding webhook payload schema
Wanneer de melding voor automatisch schalen wordt gegenereerd, worden de volgende metagegevens opgenomen in de webhook-payload:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Veld | Verplicht? | Beschrijving |
| --- | --- | --- |
| status |ja |De status die aangeeft dat er een actie voor automatisch schalen is gegenereerd |
| Bewerking |ja |Voor een toename van het aantal gevallen is het "Scale Out" en voor een afname in gevallen zal het "Schaal in" zijn |
| context |ja |De context van de actie automatisch schalen |
| tijdstempel |ja |Tijdstempel wanneer de actie automatisch schalen werd geactiveerd |
| id |Ja |ResourceManager-id van de instelling voor automatisch schalen |
| name |Ja |De naam van de instelling voor automatisch schalen |
| Details |Ja |Uitleg over de actie die de autoscale-service heeft ondernomen en de wijziging in het aantal instance's |
| subscriptionId |Ja |Abonnements-id van de doelbron die wordt geschaald |
| resourceGroupName |Ja |Resourcegroepnaam van de doelbron die wordt geschaald |
| resourceName |Ja |Naam van de doelbron die wordt geschaald |
| resourceType |Ja |De drie ondersteunde waarden: "microsoft.classiccompute/domainnames/slots/roles" - Cloud Service rollen, "microsoft.compute/virtualmachinescalesets" - Virtual Machine Scale Sets, en "Microsoft.Web/serverfarms" - Web App |
| resourceId |Ja |ResourceManager-id van de doelbron die wordt geschaald |
| portalLink portalLink |Ja |Azure-portalkoppeling naar de overzichtspagina van de doelbron |
| oldCapacity |Ja |Het huidige (oude) aantal instance's wanneer Autoscale een schaalactie heeft ondernomen |
| nieuweCapaciteit |Ja |Het aantal nieuwe instantie en dat Autoscale de resource heeft geschaald naar |
| properties |Nee |Optioneel. Set van <Toets, Waarde> paren (bijvoorbeeld Dictionary <String, String>). Het veld Eigenschappen is optioneel. In een aangepaste gebruikersinterface of op logische app gebaseerde workflow u toetsen en waarden invoeren die met behulp van de payload kunnen worden doorgegeven. Een alternatieve manier om aangepaste eigenschappen terug te geven aan de uitgaande webhook call is het gebruik van de webhook URI zelf (als query parameters) |

