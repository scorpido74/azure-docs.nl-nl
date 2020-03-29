---
title: Toegang tot gegevens met een beheerde identiteit autoriseren
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van beheerde identiteiten voor Azure-resources om toegang tot blob- en wachtrijgegevens te autoriseren van toepassingen die worden uitgevoerd in virtuele Azure-machines, functie-apps, virtuele machineschaalsets en andere.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255338"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Toegang tot blob- en wachtrijgegevens met beheerde identiteiten voor Azure-bronnen autoriseren

Azure Blob- en Queue-opslag ondersteunen Azure Active Directory-verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources.](../../active-directory/managed-identities-azure-resources/overview.md) Beheerde identiteiten voor Azure-resources kunnen toegang tot blob- en wachtrijgegevens autoriseren met Azure AD-referenties van toepassingen die worden uitgevoerd in Virtuele Azure-machines (VM's), functie-apps, schaalsets voor virtuele machines en andere services. Door beheerde identiteiten voor Azure-resources samen met Azure AD-verificatie te gebruiken, u voorkomen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.  

In dit artikel ziet u hoe u toegang tot blob- of wachtrijgegevens van een Azure VM autoriseert met beheerde identiteiten voor Azure Resources. Het beschrijft ook hoe u uw code testen in de ontwikkelomgeving.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten inschakelen op een virtuele machine

Voordat u beheerde identiteiten voor Azure Resources gebruiken om toegang tot blobs en wachtrijen van uw VM te autoriseren, moet u eerst beheerde identiteiten voor Azure Resources op de VM inschakelen. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure Resources:

- [Azure-portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-clientbibliotheken](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Zie [Beheerde identiteiten voor Azure-bronnen voor](../../active-directory/managed-identities-azure-resources/overview.md)meer informatie over beheerde identiteiten.

## <a name="authenticate-with-the-azure-identity-library"></a>Verifiëren met de Azure Identity-bibliotheek

De Azure Identity-clientbibliotheek biedt Azure Azure AD-tokenverificatieondersteuning voor de [Azure SDK.](https://github.com/Azure/azure-sdk) De nieuwste versies van de Azure Storage-clientbibliotheken voor .NET, Java, Python en JavaScript integreren met de Azure Identity-bibliotheek om een eenvoudige en veilige manier te bieden om een OAuth 2.0-token te verkrijgen voor autorisatie van Azure Storage-aanvragen.

Een voordeel van de Azure Identity-clientbibliotheek is dat u dezelfde code gebruiken om te verifiëren of uw toepassing wordt uitgevoerd in de ontwikkelomgeving of in Azure. De Azure Identity-clientbibliotheek voor .NET verifieert een beveiligingsprincipal. Wanneer uw code wordt uitgevoerd in Azure, is de beveiligingsprincipal een beheerde identiteit voor Azure-resources. In de ontwikkelomgeving bestaat de beheerde identiteit niet, dus de clientbibliotheek verifieert de gebruiker of een serviceprincipal voor testdoeleinden.

Na verificatie krijgt de Azure Identity-clientbibliotheek een tokenreferentie. Deze tokenreferentie wordt vervolgens ingekapseld in het serviceclientobject dat u maakt om bewerkingen uit te voeren tegen Azure Storage. De bibliotheek behandelt dit probleemloos voor u door de juiste tokenreferentie te verkrijgen.

Zie [Azure Identity-clientbibliotheek voor .NET voor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)meer informatie over de Azure Identity-clientbibliotheek voor .NET . Zie [Azure.Identity Namespace](/dotnet/api/azure.identity)voor referentiedocumentatie voor de Azure Identity-clientbibliotheek .

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>RBAC-rollen (role-based access control) toewijzen voor toegang tot gegevens

Wanneer een Azure AD-beveiligingsprincipal probeert toegang te krijgen tot blob- of wachtrijgegevens, moet die beveiligingsprincipal machtigingen voor de bron hebben. Of de beveiligingsprincipal nu een beheerde identiteit is in Azure of een Azure AD-gebruikersaccount met code in de ontwikkelomgeving, de beveiligingsprincipal moet een RBAC-rol krijgen toegewezen die toegang geeft tot blob- of wachtrijgegevens in Azure Storage. Zie de sectie getiteld **RBAC-rollen toewijzen voor toegangsrechten** in [Toegang tot Azure-blobs en wachtrijen met Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)voor informatie over het toewijzen van machtigingen via RBAC.

### <a name="authenticate-the-user-in-the-development-environment"></a>De gebruiker in de ontwikkelomgeving verifiëren

Wanneer uw code wordt uitgevoerd in de ontwikkelomgeving, kan verificatie automatisch worden verwerkt of moet u inloggen in de browser, afhankelijk van welke hulpprogramma's u gebruikt. Microsoft Visual Studio ondersteunt bijvoorbeeld eenmalige aanmelding (SSO), zodat het actieve Azure AD-gebruikersaccount automatisch wordt gebruikt voor verificatie. Zie [Enkele aanmelding voor toepassingen voor](../../active-directory/manage-apps/what-is-single-sign-on.md)meer informatie over SSO.

Andere ontwikkeltools kunnen u vragen om in te loggen via een webbrowser.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Een serviceprincipal in de ontwikkelomgeving verifiëren

Als uw ontwikkelomgeving geen enkele aanmelding of aanmelding via een webbrowser ondersteunt, u een serviceprincipal gebruiken om te verifiëren vanuit de ontwikkelomgeving.

#### <a name="create-the-service-principal"></a>De service-principal maken

Als u een serviceprincipal wilt maken met Azure CLI en een RBAC-rol wilt toewijzen, roept u de opdracht [AZ Ad SP create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) aan. Geef een azure storage-gegevenstoegangsrol die u wilt toewijzen aan de nieuwe serviceprincipal. Geef daarnaast de ruimte voor de roltoewijzing. Zie [Ingebouwde rollen voor Azure-resources voor](../../role-based-access-control/built-in-roles.md)meer informatie over de ingebouwde rollen voor Azure Storage.

Als u niet over voldoende machtigingen beschikt om een rol toe te wijzen aan de serviceprincipal, moet u mogelijk de eigenaar of beheerder van het account vragen om de roltoewijzing uit te voeren.

In het volgende voorbeeld wordt de Azure CLI gebruikt om een nieuwe serviceprincipal te maken en de rol **Storage Blob Data Reader** toe te wijzen met accountbereik

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

De `az ad sp create-for-rbac` opdracht retourneert een lijst met serviceprincipal-eigenschappen in json-indeling. Kopieer deze waarden zodat u ze gebruiken om de benodigde omgevingsvariabelen te maken in de volgende stap.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> RBAC-roltoewijzingen kunnen enkele minuten duren voordat ze worden gepropageerd.

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

De Azure Identity-clientbibliotheek leest waarden uit drie omgevingsvariabelen tijdens runtime om de serviceprincipal te verifiëren. In de volgende tabel wordt de waarde beschreven die moet worden ingesteld voor elke omgevingsvariabele.

|Omgevingsvariabele|Waarde
|-|-
|`AZURE_CLIENT_ID`|De app-id voor de serviceprincipal
|`AZURE_TENANT_ID`|De Azure AD-tenant-id van de serviceprincipal
|`AZURE_CLIENT_SECRET`|Het wachtwoord dat is gegenereerd voor de serviceprincipal

> [!IMPORTANT]
> Nadat u de omgevingsvariabelen hebt ingesteld, sluit en opent u het consolevenster opnieuw. Als u Visual Studio of een andere ontwikkelomgeving gebruikt, moet u mogelijk de ontwikkelomgeving opnieuw starten om de nieuwe omgevingsvariabelen te kunnen registreren.

Zie [Identiteit maken voor Azure-app in portal](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Voorbeeld van .NET-code: een blokblob maken

Voeg de `using` volgende richtlijnen toe aan uw code om de azure identity- en Azure Storage-clientbibliotheken te gebruiken.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Als u een tokenreferentie wilt ontvangen die uw code kan gebruiken om aanvragen voor Azure Storage te autoriseren, maakt u een instantie van de klasse [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) In het volgende codevoorbeeld ziet u hoe u de geverifieerde tokenreferentie krijgen en deze gebruiken om een serviceclientobject te maken en vervolgens de serviceclient te gebruiken om een nieuwe blob te uploaden:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Als u aanvragen wilt autoriseren tegen blob- of wachtrijgegevens met Azure AD, moet u HTTPS voor die aanvragen gebruiken.

## <a name="next-steps"></a>Volgende stappen

- [Beheer toegangsrechten tot opslaggegevens met RBAC.](storage-auth-aad-rbac.md)
- [Azure AD gebruiken met opslagtoepassingen](storage-auth-aad-app.md).
- [Voer Azure CLI- of PowerShell-opdrachten uit met Azure AD-referenties om toegang te krijgen tot blob- of wachtrijgegevens.](authorize-active-directory-powershell.md)
