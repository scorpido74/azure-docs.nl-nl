---
title: Ontwikkel voor Azure NetApp Files met REST API | Microsoft Docs
description: Hierin wordt beschreven hoe u kunt beginnen met het gebruik van de Azure NetApp Files REST API.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "65957034"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Ontwikkel voor Azure NetApp Files met REST API 

De REST API voor de Azure NetApp Files-service definieert HTTP-bewerkingen op basis van resources, zoals het NetApp-account, de capaciteits pool, de volumes en moment opnamen. Dit artikel helpt u aan de slag te gaan met het gebruik van de Azure NetApp Files REST API.

## <a name="azure-netapp-files-rest-api-specification"></a>Specificatie van Azure NetApp Files REST API

De REST API-specificatie voor Azure NetApp Files wordt gepubliceerd via [github](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Toegang tot de Azure NetApp Files REST API  

1. [Installeer de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u dit nog niet hebt gedaan.
2. Een service-principal maken in uw Azure Active Directory (Azure AD):
   1. Controleer of u [voldoende machtigingen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)hebt.

   1. Voer de volgende opdracht in Azure CLI in:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      De uitvoer van de opdracht is vergelijkbaar met het volgende voor beeld:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Behoud de uitvoer van de opdracht.  U hebt de `appId`waarden, `password`en `tenant` nodig. 

3. Een OAuth-toegangs token aanvragen:

    De voor beelden in dit artikel gebruiken krul.  U kunt ook verschillende API-hulpprogram ma's gebruiken, zoals [postman](https://www.getpostman.com/), [slapeloosheid](https://insomnia.rest/)en [paw](https://paw.cloud/).  

    Vervang de variabelen in het volgende voor beeld door de opdracht uitvoer van stap 2 hierboven. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    De uitvoer biedt een toegangs token dat lijkt op het volgende voor beeld:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Het weer gegeven token is 3600 seconden geldig. Daarna moet u een nieuw token aanvragen. 
    Sla het token op in een tekst editor.  U hebt deze nodig voor de volgende stap.

4. Een test oproep verzenden en het token opnemen om uw toegang tot de REST API te valideren:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Voor beelden met behulp van de API  

In dit artikel wordt de volgende URL gebruikt voor de basis lijn van aanvragen. Deze URL verwijst naar de hoofdmap van de naam ruimte Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Vervang de `subID` waarden en `resourceGroups` in de volgende voor beelden door uw eigen waarden. 

### <a name="get-request-examples"></a>Voor beelden van aanvragen ophalen

U gebruikt een GET-aanvraag voor het opvragen van objecten van Azure NetApp Files in een abonnement, zoals in de volgende voor beelden wordt weer gegeven: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Voor beelden van PUT-aanvraag

U gebruikt een PUT-aanvraag om nieuwe objecten te maken in Azure NetApp Files, zoals in de volgende voor beelden wordt weer gegeven. De hoofd tekst van de PUT-aanvraag kan de met JSON opgemaakte gegevens voor de wijzigingen bevatten of kan een bestand opgeven waaruit moet worden gelezen. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>JSON-voor beelden

In het volgende voor beeld ziet u hoe u een NetApp-account maakt:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

In het volgende voor beeld ziet u hoe u een capaciteits groep maakt: 

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

In het volgende voor beeld ziet u hoe u een nieuw volume maakt: 

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

In het volgende voor beeld ziet u hoe u een moment opname maakt van een volume: 

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
> U moet opgeven `fileSystemId` voor het maken van een moment opname.  U kunt de `fileSystemId` waarde ophalen met een GET-aanvraag naar een volume. 

## <a name="next-steps"></a>Volgende stappen

[Zie de naslag informatie voor Azure NetApp Files REST API](https://docs.microsoft.com/rest/api/netapp/)
