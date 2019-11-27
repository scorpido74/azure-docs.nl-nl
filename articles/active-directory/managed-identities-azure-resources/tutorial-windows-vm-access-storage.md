---
title: Zelf studie`:` een beheerde identiteit gebruiken om toegang te krijgen tot Azure Storage-Windows-Azure AD
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het gebruiken van een door het Windows-VM-systeem toegewezen beheerde identiteit om toegang te krijgen tot Azure Storage.
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
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2449307936839d50fe0d48a0536ca4dd9c8d85c3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74181908"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Storage via een toegangssleutel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


> [!IMPORTANT] 
> Azure Storage ondersteunt nu Azure AD-verificatie. Als best practice gebruikt u [Azure AD-verificatie](tutorial-vm-windows-access-storage.md) in plaats van toegangs sleutels. 


Deze zelfstudie laat zien hoe u toegangssleutels voor opslagaccounts kunt ophalen met een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine (VM). U kunt toegangssleutels voor opslag gebruiken zoals u gewend bent bij opslagbewerkingen, bijvoorbeeld bij het gebruik van de Storage-SDK. Voor deze zelfstudie uploaden en downloaden we blobs met behulp van PowerShell voor Azure Storage. U leert het volgende:


> [!div class="checklist"]
> * Een opslagaccount maken
> * Uw virtuele machine toegang verlenen tot toegangssleutels voor opslagaccounts in Resource Manager 
> * Een toegangstoken ophalen met de identiteit van de virtuele machine, en daarmee de toegangssleutels voor opslag ophalen uit Resource Manager 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Een opslagaccount maken 

Als u nog geen opslagaccount hebt, maakt u er nu een. U kunt deze stap ook overslaan en de door het systeem toegewezen beheerde identiteit voor uw VM toegang verlenen tot de sleutels van een bestaand opslagaccount. 

1. Klik op de knop **+/Nieuwe service maken** in de linkerbovenhoek van Azure Portal.
2. Klik op **Opslag** en vervolgens op **Opslagaccount**. Het paneel Opslagaccount maken wordt weergegeven.
3. Voer een naam voor het opslagaccount in. U gaat deze gegevens later gebruiken.  
4. **Implementatiemodel** en **Soort account** moeten respectievelijk worden ingesteld op Resource Manager en Algemeen gebruik. 
5. Zorg ervoor dat de waarden van **Abonnement** en **Resourcegroep** overeenkomen met de waarden die u hebt opgegeven bij het maken van de virtuele machine in de vorige stap.
6. Klik op **Create**.

    ![Nieuw opslagaccount maken](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Een blobcontainer in het opslagaccount maken

Later zullen we een bestand uploaden en downloaden naar het nieuwe opslagaccount. Omdat voor bestanden blobopslag nodig is, moeten we een blobcontainer maken waarin het bestand kan worden opgeslagen.

1. Navigeer terug naar het zojuist gemaakte opslagaccount.
2. Klik op de koppeling **Containers** aan de linkerkant, onder Blob service.
3. Klik op **+ Container** boven aan de pagina om het paneel Nieuwe container deelvenster uit te schuiven.
4. Geef een naam voor de container op, selecteer een toegangsniveau en klik op **OK**. De naam die u hebt opgegeven, wordt verderop in de zelfstudie gebruikt. 

    ![Opslagcontainer maken](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>De door het systeem toegewezen beheerde entiteit van de virtuele machine toegang verlenen tot het gebruik van toegangssleutels voor het opslagaccount 

Azure Storage biedt geen systeemeigen ondersteuning voor Azure AD-verificatie.  U kunt echter de door het systeem toegewezen beheerde entiteit van de virtuele machine gebruiken om toegangssleutels voor opslagaccounts op te halen uit Resource Manager, en vervolgens een sleutel gebruiken om toegang tot de opslag te krijgen.  In deze stap verleent u de door het systeem toegewezen beheerde identiteit voor uw VM toegang tot de sleutels voor uw opslagaccount.   

1. Navigeer terug naar het zojuist gemaakte opslagaccount.  
2. Klik op de koppeling **Toegangsbeheer (IAM)** in het linkerpaneel.  
3. Klik op **+ Roltoewijzing toevoegen** boven aan de pagina om een nieuwe roltoewijzing voor de VM toe te voegen
4. Stel **Rol** in op 'De servicerol Sleuteloperator voor opslagaccounts', aan de rechterkant van de pagina. 
5. Stel in de volgende vervolgkeuzelijst **Toegang toewijzen aan** de resource in op Virtuele machine.  
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst **Abonnement**, en stel **Resourcegroep** in op Alle resourcegroepen.  
7. Kies ten slotte onder **Selecteren** uw virtuele Windows-machine in de vervolgkeuzelijst en klik op **Opslaan**. 

    ![Alt-tekst voor afbeelding](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Een toegangstoken ophalen met behulp van de door het systeem toegewezen beheerde identiteit van de VM voor het aanroepen van Azure Resource Manager 

Voor de rest van de zelfstudie werken we op de virtuele machine die we eerder hebben gemaakt. 

In dit gedeelte moet u de PowerShell-cmdlets voor Azure Resource Manager gebruiken.  Als u deze nog niet hebt geïnstalleerd, moet u [de nieuwste versie downloaden](https://docs.microsoft.com/powershell/azure/overview) voordat u doorgaat.

1. In Azure Portal navigeert u naar **Virtuele machines**, gaat u naar uw virtuele Windows-machine, klikt u vervolgens boven aan de pagina **Overzicht** op **Verbinden**. 
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de virtuele Windows-machine. 
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u PowerShell in de externe sessie.
4. Dien met behulp van Powershell een Invoke-WebRequest-aanvraag in bij de lokaal beheerde identiteit om een toegangstoken voor Azure Resource Manager op te halen voor het Azure-resources-eindpunt.

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
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Toegangssleutels voor opslagaccounts ophalen uit Azure Resource Manager om opslagaanroepen te maken  

Gebruik nu PowerShell voor het aanroepen van Resource Manager met behulp van het toegangstoken dat we hebben opgehaald in de vorige sectie, om de toegangssleutel voor opslag op te halen. Als we de toegangssleutel voor opslag eenmaal hebben, kunnen we up- en downloadbewerkingen aanroepen.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> De URL is hoofdlettergevoelig, dus gebruik precies dezelfde naam van de resourcegroep als hiervoor, met inbegrip van de hoofdletter 'G' in 'resourceGroups'. 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Vervolgens maken we een bestand met de naam test.txt. Gebruik de toegangssleutel voor opslag om te verifiëren bij de `New-AzStorageContent`-cmdlet, upload het bestand naar de blobcontainer, en download het bestand vervolgens.

```bash
echo "This is a test text file." > test.txt
```

Zorg ervoor dat u eerst de Azure Storage-cmdlets installeert met `Install-Module Az.Storage`. Upload de blob die u zojuist hebt gemaakt, met behulp van de `Set-AzStorageBlobContent` PowerShell-cmdlet:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Reactie:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

U kunt de blob die u zojuist hebt geüpload, ook downloaden met de `Get-AzStorageBlobContent` PowerShell-cmdlet:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Reactie:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een door het systeem toegewezen beheerde entiteit te maken om toegang tot Azure Storage te krijgen met behulp van een toegangssleutel.  Zie voor meer informatie over Azure Storage-toegangssleutels:

> [!div class="nextstepaction"]
>[De toegangssleutels van uw opslagaccount beheren](/azure/storage/common/storage-create-storage-account)

