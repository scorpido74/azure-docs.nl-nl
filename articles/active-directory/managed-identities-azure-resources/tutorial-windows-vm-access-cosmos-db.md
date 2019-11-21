---
title: Tutorial`:` Use a managed identity to access Azure Cosmos DB - Windows - Azure AD
description: Een zelfstudie die u helpt bij het gebruiken van een door het systeem toegewezen beheerde identiteit op een Windows-VM voor toegang tot Azure Cosmos DB.
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
ms.date: 04/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97a89e87dad1e940f30e255a919f3f2cf25f21d7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224251"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot Cosmos DB met een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine (VM). In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Cosmos DB-account maken
> * Een door het Windows-VM-systeem toegewezen beheerde identiteit toegang geven tot de toegangssleutels van het Cosmos DB-account
> * Een toegangstoken ophalen met behulp van de door het Windows-VM-systeem toegewezen beheerde identiteit om Azure Resource Manager aan te roepen
> * Toegangssleutels ophalen uit Azure Resource Manager voor Cosmos DB-aanroepen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Installeer de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-account maken 

Maak een Cosmos DB-account als u er nog geen hebt. U kunt deze stap overslaan en een bestaand Cosmos DB-account gebruiken. 

1. Klik op de knop **+/Nieuwe service maken** in de linkerbovenhoek van Azure Portal.
2. Klik op **Databases** en vervolgens op **Azure Cosmos DB**. Er verschijnt een nieuw venster 'Nieuw account'.
3. Voer een **Id** in voor het Cosmos DB-account, die u later zult gebruiken.  
4. **API** moet worden ingesteld op 'SQL'. De aanpak die in deze zelfstudie wordt beschreven, kan worden gebruikt met de andere beschikbare API-typen, maar de stappen in deze zelfstudie zijn voor de SQL-API.
5. Zorg ervoor dat de waarden van **Abonnement** en **Resourcegroep** overeenkomen met de waarden die u hebt opgegeven bij het maken van de virtuele machine in de vorige stap.  Selecteer een **Locatie** waar Cosmos DB beschikbaar is.
6. Klik op **Maken**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Een verzameling maken in het Cosmos DB-account

Voeg vervolgens een gegevensverzameling toe in het Cosmos DB-account, waarop u later query’s kunt uitvoeren.

1. Navigeer naar het zojuist gemaakte Cosmos DB-account.
2. Klik op het tabblad **Overzicht** op de knop **+/Verzameling toevoegen**. Het deelvenster 'Verzameling toevoegen' wordt weergegeven.
3. Geef de verzameling een database-ID en een verzameling-ID, selecteer een opslagcapaciteit, voer een partitiesleutel in, voer een doorvoerwaarde in en klik vervolgens op **OK**.  Voor deze zelfstudie volstaat het om ‘Test’ te gebruiken als database-ID en verzameling-ID en een vaste opslagcapaciteit en de laagste doorvoer (400 RU/s) te selecteren.  

## <a name="grant-windows-vm-system-assigned-managed-identity-access-to-the-cosmos-db-account-access-keys"></a>Door het Windows-VM-systeem toegewezen beheerde identiteit toegang geven tot de toegangssleutels van het Cosmos DB-account

Cosmos DB biedt geen systeemeigen ondersteuning voor Azure AD-verificatie. U kunt echter een door het systeem toegewezen beheerde identiteit gebruiken om een Cosmos DB-toegangssleutel op te halen uit Resource Manager, en die sleutel gebruiken om toegang tot Cosmos DB te krijgen. In deze stap verleent u de door het Windows-VM-systeem toegewezen beheerde identiteit toegang tot de sleutels voor het Cosmos DB-account.

Werk de waarden voor `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` en `<COSMOS DB ACCOUNT NAME>` bij voor uw omgeving om de door het Windows-VM-systeem toegewezen beheerde identiteit met behulp van PowerShell toegang te geven tot het Cosmos DB-account in Azure Resource Manager. Cosmos DB ondersteunt twee granulariteitsniveaus bij het gebruik van toegangssleutels: lees-/schrijftoegang tot het account en alleen-lezen toegang tot het account.  Wijs de rol `DocumentDB Account Contributor` toe als u sleutels voor lezen/schrijven voor het account wilt krijgen, de rol `Cosmos DB Account Reader Role` als u alleen-lezensleutels voor het account wilt ophalen.  Voor deze zelfstudie moet u de `Cosmos DB Account Reader Role` toewijzen:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```
## <a name="get-an-access-token-using-the-windows-vm-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Een toegangstoken ophalen met behulp van de door het Windows-VM-systeem toegewezen beheerde identiteit om Azure Resource Manager aan te roepen

Voor de rest van de zelfstudie werken we op de virtuele machine die we eerder hebben gemaakt. 

U moet de nieuwste versie van [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) op uw Windows-VM installeren.

1. In Azure Portal navigeert u naar **Virtuele machines**, gaat u naar uw virtuele Windows-machine, klikt u vervolgens boven aan de pagina **Overzicht** op **Verbinden**. 
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de virtuele Windows-machine. 
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u PowerShell in de externe sessie.
4. Dien met behulp van Powershell een Invoke-WebRequest-aanvraag in bij de lokale beheerde identiteiten om een toegangstoken voor Azure Resource Manager op te halen voor het Azure-resources-eindpunt.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > De waarde van de parameter 'resource' moet exact overeenkomen met wat er in Azure AD wordt verwacht. Wanneer u de resource-id van Azure Resource Manager gebruikt, moet u de URI opgeven met een slash op het einde.
    
   Extraheer vervolgens het element 'Content' (inhoud), dat is opgeslagen als een tekenreeks in JSON-indeling (JavaScript Object Notation) in het object $response. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Extraheer vervolgens het toegangstoken uit het antwoord.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Toegangssleutels ophalen uit Azure Resource Manager voor Cosmos DB-aanroepen

Gebruik nu PowerShell voor het aanroepen van Resource Manager met behulp van het toegangstoken dat in de vorige sectie is opgehaald, om de toegangssleutel voor het Cosmos DB-account op te halen. Wanneer we de toegangssleutel hebben, kunnen we query’s uitvoeren op de Cosmos DB. Vervang de parameterwaarden `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` en `<COSMOS DB ACCOUNT NAME>` door uw eigen waarden. Vervang de waarde `<ACCESS TOKEN>` door het toegangstoken dat u eerder hebt opgehaald.  Als u lees-/schrijfsleutels wilt ophalen, gebruikt u sleutelbewerkingstype `listKeys`.  Als u alleen-lezensleutels wilt ophalen, gebruikt u sleutelbewerkingstype `readonlykeys`:

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
In het antwoord vindt u de lijst met sleutels.  Als u bijvoorbeeld alleen-lezensleutels ophaalt:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Nu u de toegangssleutel voor het Cosmos DB-account hebt, kunt u deze doorgeven aan een Cosmos DB-SDK en aanroepen doen om toegang tot het account te krijgen.  Voor een snel voorbeeld kunt u de toegangssleutel doorgeven aan de Azure CLI.  U kunt de `<COSMOS DB CONNECTION URL>` vinden op het tabblad **Overzicht** op de Cosmos DB-accountblade in Azure Portal.  Vervang de `<ACCESS KEY>` door de waarde die u hierboven hebt opgehaald:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Deze CLI-opdracht retourneert details over de verzameling:

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een door het Windows-VM-systeem toegewezen identiteit kunt gebruiken voor toegang tot Cosmos DB.  Zie voor meer informatie over Cosmos DB:

> [!div class="nextstepaction"]
>[Overzicht van Azure Cosmos DB](/azure/cosmos-db/introduction)


