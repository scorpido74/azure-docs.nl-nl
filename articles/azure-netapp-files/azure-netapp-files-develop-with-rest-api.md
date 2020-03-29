---
title: Ontwikkelen voor Azure NetApp-bestanden met REST-API | Microsoft Documenten
description: Beschrijft hoe u aan de slag met de AZURE NetApp Files REST API.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65957034"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Ontwikkelen voor Azure NetApp-bestanden met REST-API 

De REST-API voor de Azure NetApp Files-service definieert HTTP-bewerkingen op resources zoals het NetApp-account, de capaciteitsgroep, de volumes en momentopnamen. Met dit artikel u aan de slag met het gebruik van de Azure NetApp Files REST API.

## <a name="azure-netapp-files-rest-api-specification"></a>API-specificatie azure NetApp-bestanden

De REST API-specificatie voor Azure NetApp-bestanden wordt gepubliceerd via [GitHub:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Toegang tot de API voor de REST van Azure NetApp Files  

1. [Installeer de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u dit nog niet hebt gedaan.
2. Een serviceprincipal maken in uw Azure Active Directory (Azure AD):
   1. Controleer of u [over voldoende machtigingen beschikt.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)

   1. Voer de volgende opdracht in Azure CLI in:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      De opdrachtuitvoer is vergelijkbaar met het volgende voorbeeld:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Houd de opdrachtuitvoer.  Je hebt `appId`de `password`, `tenant` , en waarden. 

3. Vraag een OAuth-toegangstoken aan:

    De voorbeelden in dit artikel gebruiken cURL.  U ook gebruik maken van verschillende API-tools zoals [Postman,](https://www.getpostman.com/) [Insomnia](https://insomnia.rest/), en [Paw](https://paw.cloud/).  

    Vervang de variabelen in het volgende voorbeeld door de opdrachtuitvoer uit stap 2 hierboven. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    De uitvoer biedt een toegangstoken dat vergelijkbaar is met het volgende voorbeeld:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Het weergegeven token is 3600 seconden geldig. Daarna moet je een nieuw token aanvragen. 
    Sla het token op in een teksteditor.  Je hebt het nodig voor de volgende stap.

4. Stuur een testoproep en voeg het token toe om uw toegang tot de REST API te valideren:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Voorbeelden met behulp van de API  

In dit artikel wordt de volgende URL gebruikt voor de basislijn van aanvragen. Deze URL verwijst naar de hoofdmap van de naamruimte azure NetApp-bestanden. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

U moet `subID` de `resourceGroups` en waarden in de volgende voorbeelden vervangen door uw eigen waarden. 

### <a name="get-request-examples"></a>Voorbeelden van GET-aanvragen

U gebruikt een GET-verzoek om objecten van Azure NetApp-bestanden in een abonnement op te vragen, zoals in de volgende voorbeelden wordt weergegeven: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>VOORBEELDEN van PUT-aanvragen

U gebruikt een PUT-verzoek om nieuwe objecten te maken in Azure NetApp-bestanden, zoals de volgende voorbeelden laten zien. De hoofdtekst van de PUT-aanvraag kan de JSON-opgemaakte gegevens voor de wijzigingen bevatten of het kan een bestand opgeven om uit te lezen. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>VOORBEELDEN VAN JSON

In het volgende voorbeeld ziet u hoe u een NetApp-account maakt:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

In het volgende voorbeeld ziet u hoe u een capaciteitsgroep maakt: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

In het volgende voorbeeld ziet u hoe u een nieuw volume maakt: 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

In het volgende voorbeeld ziet u hoe u een momentopname van een volume maakt: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> U moet `fileSystemId` opgeven voor het maken van een momentopname.  U `fileSystemId` de waarde verkrijgen met een GET-aanvraag voor een volume. 

## <a name="next-steps"></a>Volgende stappen

[Zie de API-verwijzing naar Azure NetApp Files REST](https://docs.microsoft.com/rest/api/netapp/)
