---
title: Zelf studie`:`een door Windows VM beheerde identiteit gebruiken om toegang te krijgen tot Azure AD Graph
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het gebruiken van een door het Windows-VM-systeem toegewezen beheerde identiteit om toegang te krijgen tot de Azure AD Graph API.
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
ms.openlocfilehash: 43ef467adb8970d410404c151d0028ee4cda92b9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183008"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot de Azure AD Graph API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Deze zelf studie laat zien hoe u een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine (VM) gebruikt om toegang te krijgen tot de Azure AD-Graph API om de groepslid maatschappen op te halen. Beheerde entiteiten voor Azure-resources worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Microsoft Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen.  Tijdens deze zelfstudie gaat u voor uw VM-identiteitsabonnement query's uitvoeren in Azure AD-groepen. Groepsgegevens worden vaak gebruikt bij het nemen van autorisatiebeslissingen. Achter de schermen wordt de beheerde identiteit van uw VM in Azure AD vertegenwoordigd door een **service-principal**. Voordat u de groepsquery uitvoert, voegt u de service-principal van de VM-identiteit toe aan een groep in Azure AD. U kunt dit doen met Azure PowerShell, Azure AD PowerShell of Azure CLI.

> [!div class="checklist"]
> * Verbinding maken met Azure AD
> * Uw VM-identiteit in Azure AD toevoegen aan een groep 
> * De VM-identiteit toegang verlenen tot Azure AD Graph 
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Azure AD Graph aanroepen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Als u een VM-identiteit toegang wilt verlenen tot Azure AD Graph, moet uw account beschikken over de rol **Globale beheerder** in Azure AD.
- Installeer de meest recente versie van [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) als u dat nog niet hebt gedaan. 

## <a name="connect-to-azure-ad"></a>Verbinding maken met Azure AD

U moet verbinding maken met Azure AD om de VM toe te wijzen aan een groep en om de VM toestemming te geven de groepslidmaatschappen op te halen. U kunt de cmdlet Connect-AzureAD rechtstreeks gebruiken, of met Tenant-id-parameter als u meerdere tenants hebt.

```powershell
Connect-AzureAD
```
OF
```powershell
Connect-AzureAD -TenantId "Object Id of the tenant"
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Uw VM-identiteit in Azure AD toevoegen aan een groep

Als u door het systeem toegewezen beheerde identiteiten hebt ingeschakeld op de Windows-VM, is er in Azure AD een service-principal gemaakt.  Nu moet u de VM toevoegen aan een groep.  In het volgende voorbeeld wordt een nieuwe groep gemaakt in Azure AD en wordt de service-principal van uw VM aan die groep toegevoegd:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Uw VM toegang verlenen tot de Azure AD Graph-API

Met behulp van beheerde identiteiten voor Azure-resources kan uw code toegangstokens ophalen voor verificatie bij resources die ondersteuning bieden voor Microsoft Azure AD-verificatie. De Microsoft Azure AD Graph-API biedt ondersteuning voor Azure AD-verificatie. In deze stap verleent u de service-principal van uw VM-identiteit toegang tot de Azure AD Graph zodat er een query kan worden uitgevoerd voor groepslidmaatschappen. Service-principals krijgen via **toepassingstoestemmingen** toegang tot Microsoft of Azure AD Graph. Het type toepassingstoestemming dat u moet verlenen, is afhankelijk van de entiteit waar u toegang toe wilt verkrijgen in de MS of Azure AD Graph.

In deze zelfstudie verleent u uw VM-identiteit toestemming om query's voor groepslidmaatschappen uit te voeren via de toepassingstoestemming ```Directory.Read.All```. Om deze toestemming te verlenen, hebt u een gebruikersaccount nodig dat beschikt over de rol Globale beheerder in Azure AD. Normaal gesproken geeft u een toepassing toestemming door in de Azure-portal naar de registratie van de toepassing te gaan en de toestemming daar toe te voegen. Bij beheerde identiteiten voor Azure-resources worden er echter geen toepassingsobjecten geregistreerd in Azure AD. Er worden alleen service-principals geregistreerd. Als u de toepassingstoestemming wilt registreren, gebruikt u het opdrachtregelprogramma van Azure AD, PowerShell. 

Azure AD Graph:
- App-id van de service-principal (gebruikt om een app toestemming te geven): 00000002-0000-0000-c000-000000000000
- Resource-id (gebruikt om toegangstokens voor beheerde identiteiten aan te vragen voor Azure-resources): https://graph.windows.net
- Verwijzing toestemmingsbereik: [Azure AD Graph-toestemmingsverwijzing](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Toepassingstoestemmingen verlenen met Azure AD PowerShell

U hebt Azure AD PowerShell nodig om deze optie te gebruiken. Als u deze nog niet hebt geïnstalleerd, moet u [de nieuwste versie downloaden](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) voordat u doorgaat.

1. Open een PowerShell-venster en maak verbinding met Azure AD:

   ```powershell
   Connect-AzureAD
   ```
   Als u verbinding wilt maken met een specifieke omgeving van Azure Active Directory, gebruikt u de parameter _TenantId_ op de volgende manier:

   ```powershell
   Connect-AzureAD -TenantId "Object Id of the tenant"
   ```

   
2. Voer de volgende PowerShell-opdrachten uit om de toepassingstoestemming ``Directory.Read.All`` toe te wijzen aan de service-principal die staat voor de identiteit van uw virtuele machine.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   De uitvoer van de laatste opdracht ziet er als volgt uit (de id van de toewijzing wordt geretourneerd):
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Als het aanroepen van `New-AzureAdServiceAppRoleAssignment` mislukt met de fout `bad request, one or more properties are invalid`, is de toepassingstoestemming mogelijk al toegewezen aan de service-principal van de VM-identiteit. U kunt de volgende PowerShell-opdrachten gebruiken om te controleren of de toepassingstoestemming al bestaat voor de identiteit van uw VM en Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   U kunt de volgende PowerShell-opdrachten gebruiken om alle toepassingstoestemmingen te vermelden die zijn verleend aan Azure AD Graph:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   U kunt de volgende PowerShell-opdrachten gebruiken om alle toepassingstoestemmingen te verwijderen die zijn verleend aan uw VM-identiteit voor Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine om Azure Active Directory Graph aan te roepen 

Als u de door het systeem toegewezen beheerde identiteit van de virtuele machine wilt gebruiken voor verificatie bij Azure AD Graph, moet u de aanvragen verzenden vanaf de virtuele machine.

1. Navigeer in de Azure-portal naar **Virtuele machines**, ga naar uw virtuele Windows-machine en klik op de blade **Overzicht** op **Verbinden**.  
2. Voer de **gebruikersnaam** en het **wachtwoord** in die u hebt gebruikt bij het maken van de Windows-VM.
3. Nu u een 'Verbinding met extern bureaublad' met de virtuele machine hebt gemaakt, opent u PowerShell in de externe sessie.  
4. Dien met behulp van PowerShell een Invoke-WebRequest-aanvraag in bij de lokale beheerde identiteiten om een toegangstoken voor Azure AD Graph op te halen voor het Azure-resourceseindpunt.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Converteer de reactie van een JSON-object naar een PowerShell-object.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraheer het toegangstoken uit de reactie.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. Met de object-id van de service-principal van uw VM-identiteit (deze waarde is op te halen via de variabele uit de vorige stappen: ``$ManagedIdentitiesServicePrincipal.ObjectId``) kunt u een query uitvoeren voor de Azure AD Graph API om de groepslidmaatschappen op te halen. Vervang `<OBJECT ID>` door de object-ID uit de vorige stap en <`ACCESS-TOKEN>` met het eerder verkregen toegangs token:

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   Het antwoord bevat de `Object ID` van de groep waar u in vorige stappen de service-principal van uw VM aan hebt toegevoegd.

   Reactie:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een door het Windows-VM-systeem toegewezen beheerde identiteit kunt gebruiken voor toegang tot Azure AD Graph.  Meer informatie over Azure AD Graph:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
