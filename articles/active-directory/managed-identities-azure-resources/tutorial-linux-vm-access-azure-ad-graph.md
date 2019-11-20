---
title: Zelf studie`:` een beheerde identiteit voor Linux-VM'S gebruiken voor toegang tot Azure AD Graph API
description: Een zelfstudie die u helpt bij het gebruiken van een door het Linux-VM-systeem toegewezen beheerde identiteit voor toegang tot Azure AD Graph-API.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68d560e7d326cc2ddc47ed9f689dc8e31f8ab9ff
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183646"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Zelfstudie: Een door het Linux-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure AD Graph-API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot de Azure Active Directory Graph API met een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM) om de groepslidmaatschappen te kunnen ophalen. Beheerde entiteiten voor Azure-resources worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Microsoft Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen.  

Tijdens deze zelfstudie gaat u voor uw VM-identiteitsabonnement query's uitvoeren op Azure AD-groepen. Groepsgegevens worden vaak gebruikt bij het nemen van autorisatiebeslissingen. Achter de schermen wordt de beheerde identiteit van uw VM in Azure AD vertegenwoordigd door een **service-principal**. 

> [!div class="checklist"]
> * Verbinding maken met Azure AD
> * Uw VM-identiteit in Azure AD toevoegen aan een groep 
> * De VM-identiteit toegang verlenen tot Azure AD Graph 
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Azure AD Graph aanroepen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [De nieuwste versie van de Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Als u een VM-identiteit toegang wilt verlenen tot Azure AD Graph, moet uw account beschikken over de rol **Globale beheerder** in Azure AD.

## <a name="connect-to-azure-ad"></a>Verbinding maken met Azure AD

U moet verbinding maken met Azure AD om de VM toe te wijzen aan een groep en om de VM toestemming te geven de groepslidmaatschappen op te halen.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Identiteit van uw VM toevoegen aan een groep in Azure AD

Op het moment dat u door het systeem toegewezen beheerde identiteiten hebt ingeschakeld op de Linux-VM, is er in Azure AD een service-principal gemaakt.  U moet de VM toevoegen aan een groep. Raadpleeg het volgende artikel voor instructies over het toevoegen van uw VM aan een groep in Azure AD:

- [Groepsleden toevoegen](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Uw VM toegang verlenen tot de Azure AD Graph-API

Met behulp van beheerde identiteiten voor Azure-resources kan uw code toegangstokens ophalen voor verificatie bij resources die ondersteuning bieden voor Microsoft Azure AD-verificatie. De Microsoft Azure AD Graph-API biedt ondersteuning voor Azure AD-verificatie. In deze stap verleent u de service-principal van uw VM-identiteit toegang tot de Azure AD Graph zodat er een query kan worden uitgevoerd voor groepslidmaatschappen. Service-principals krijgen via **toepassingstoestemmingen** toegang tot Microsoft of Azure AD Graph. Het type toepassingstoestemming dat u moet verlenen, is afhankelijk van de entiteit waar u toegang toe wilt verkrijgen in de MS of Azure AD Graph.

In deze zelfstudie verleent u uw VM-identiteit toestemming om query's voor groepslidmaatschappen uit te voeren via de toepassingstoestemming `Directory.Read.All`. Om deze toestemming te verlenen, hebt u een gebruikersaccount nodig dat beschikt over de rol Globale beheerder in Azure AD. Normaal gesproken geeft u een toepassing toestemming door in de Azure-portal naar de registratie van de toepassing te gaan en de toestemming daar toe te voegen. Bij beheerde identiteiten voor Azure-resources worden er echter geen toepassingsobjecten geregistreerd in Azure AD. Er worden alleen service-principals geregistreerd. Als u de toepassingstoestemming wilt registreren, gebruikt u het opdrachtregelprogramma van Azure AD, PowerShell. 

Azure AD Graph:
- App-id van de service-principal (gebruikt om een app toestemming te geven): 00000002-0000-0000-c000-000000000000
- Resource-id (gebruikt om toegangstokens voor beheerde identiteiten aan te vragen voor Azure-resources): https://graph.windows.net
- Verwijzing toestemmingsbereik: [Azure AD Graph-toestemmingsverwijzing](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Toepassingstoestemmingen verlenen met CURL

1. Een token ophalen om CURL aanvragen te versturen:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. U moet de `objectId` van uw VM ophalen en noteren. Deze id hebt u nodig in volgende stappen om machtigingen te verlenen aan de VM voor het lezen van het groepslidmaatschap. Vervang `<ACCESS TOKEN>` door het toegangstoken dat u in de vorige stap opgehaald.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Gebruik de Azure AD Graph-appID, 00000002-0000-0000-c000-000000000000, om de `objectId` voor `odata.type: Microsoft.DirectoryServices.ServicePrincipal` op te halen en te noteren en de `id` voor de app-rolmachtiging `Directory.Read.All`.  Vervang `<ACCESS TOKEN>` door het toegangstoken dat u eerder hebt opgehaald.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Reactie:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. Geef de service-principal van de VM nu toegang tot directory-objecten van Azure AD met behulp van de Azure AD Graph-API.  De `id`-waarde is de waarde voor de app-rolmachtiging `Directory.Read.All` en de `resourceId` is de `objectId` voor de service-principal `odata.type:Microsoft.DirectoryServices.ServicePrincipal` (de waarden die u in de vorige stap hebt genoteerd).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine om Azure Active Directory Graph aan te roepen 

U hebt een SSH-client nodig om deze stappen uit te voeren. Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](https://msdn.microsoft.com/commandline/wsl/about) gebruiken. Zie [De sleutels van uw SSH-client gebruiken onder Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) of [Een sleutelpaar met een openbare SSH-sleutel en een privé-sleutel maken en gebruiken voor virtuele Linux-machines in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) als u hulp nodig hebt bij het configureren van de sleutels van uw SSH-client.

1. Navigeer in de portal naar de virtuele Linux-machine en klik in het **overzicht** op **Verbinden**.  
2. **Maak verbinding** met de virtuele machine met de SSH-client van uw keuze. 
3. Verstuur in het terminalvenster met behulp van CURL een aanvraag naar het eindpunt van de lokaal beheerde identiteiten voor Azure-resources om een toegangstoken voor Azure AD Graph op te halen.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   De reactie bevat het toegangstoken dat u nodig hebt voor toegang tot Azure AD Graph.

   Reactie:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. Met de object-id van de service-principal van uw VM (de waarde die u in eerdere stappen hebt opgehaald) kunt u een query uitvoeren met de Azure AD Graph-API om de groepslidmaatschappen op te halen. Vervang `<OBJECT-ID>` door de object-id van de service-principal van uw VM en `<ACCESS-TOKEN>` door het eerder verkregen toegangstoken:

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Reactie:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een door het Linux-VM-systeem toegewezen beheerde identiteit kunt gebruiken voor toegang tot Azure AD Graph.  Meer informatie over Azure AD Graph:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
