---
title: Toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources-Azure Storage
description: Azure Blob-en Queue Storage ondersteunen het verlenen van toegang tot resources met Azure Active Directory en beheerde identiteiten voor Azure-resources. U kunt beheerde identiteiten voor Azure-resources gebruiken om toegang te verlenen tot blobs en wacht rijen van toepassingen die worden uitgevoerd op virtuele machines van Azure, functie-apps, schaal sets voor virtuele machines en andere.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 833aa7dcce5c429b3005a378e93e2177df1eb0d4
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595186"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources

Azure Blob-en Queue Storage ondersteunen Azure Active Directory-verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md). Beheerde identiteiten voor Azure-resources kunnen toegang verlenen tot Blob-en wachtrij gegevens met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in azure virtual machines (Vm's), functie-apps, schaal sets voor virtuele machines en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.  

In dit artikel wordt beschreven hoe u toegang kunt verlenen tot BLOB-of wachtrij gegevens van een Azure-VM met beheerde identiteiten voor Azure-resources. Ook wordt beschreven hoe u uw code kunt testen in de ontwikkel omgeving.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen

Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om toegang te verlenen tot blobs en wacht rijen van uw VM, moet u eerst beheerde identiteiten voor Azure-resources inschakelen op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Zie [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Verifiëren met de Azure-identiteits bibliotheek (preview-versie)

Met de Azure Identity client-bibliotheek voor .NET (preview) wordt een beveiligingsprincipal geverifieerd. Wanneer uw code wordt uitgevoerd in azure, is de beveiligingsprincipal een beheerde identiteit voor Azure-resources.

Wanneer uw code wordt uitgevoerd in de ontwikkel omgeving, wordt de verificatie mogelijk automatisch verwerkt of is er mogelijk een browser aanmelding vereist, afhankelijk van de hulpprogram ma's die u gebruikt. Micro soft Visual Studio ondersteunt eenmalige aanmelding (SSO), zodat het actieve Azure AD-gebruikers account automatisch wordt gebruikt voor verificatie. Zie [eenmalige aanmelding bij toepassingen](../../active-directory/manage-apps/what-is-single-sign-on.md)voor meer informatie over SSO.

Andere ontwikkel hulpprogramma's vragen u mogelijk om u aan te melden via een webbrowser. U kunt ook een Service-Principal gebruiken om te verifiëren vanuit de ontwikkel omgeving. Zie [identiteit voor Azure-app maken in de portal](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

Na de verificatie krijgt de Azure Identity client-bibliotheek een token referentie. Deze token referentie wordt vervolgens ingekapseld in het service-client object dat u maakt om bewerkingen uit te voeren op basis van Azure Storage. De bibliotheek verwerkt dit voor uw probleemloos door de juiste token referentie op te halen.

Zie de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)voor meer informatie over de Azure Identity client-bibliotheek.

## <a name="assign-rbac-roles-for-access-to-data"></a>RBAC-rollen toewijzen voor toegang tot gegevens

Wanneer een Azure AD-beveiligingsprincipal probeert toegang te krijgen tot BLOB-of wachtrij gegevens, moet die beveiligingsprincipal machtigingen hebben voor de resource. Of de beveiligingsprincipal een beheerde identiteit in azure of een Azure AD-gebruikers account voor het uitvoeren van code in de ontwikkel omgeving is, moet aan de beveiligingsprincipal een RBAC-rol worden toegewezen die toegang verleent tot BLOB-of wachtrij gegevens in Azure Storage. Voor informatie over het toewijzen van machtigingen via RBAC, zie de sectie **RBAC-rollen toewijzen voor toegangs rechten** in [toegang verlenen tot Azure-blobs en-wacht rijen met behulp van Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>De preview-pakketten installeren

In de voor beelden in dit artikel wordt gebruikgemaakt van de nieuwste preview-versie van de Azure Storage-client bibliotheek voor Blob Storage. Als u het preview-pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

In de voor beelden in dit artikel wordt ook gebruikgemaakt van de nieuwste preview-versie van de [Azure Identity client-bibliotheek voor .net](https://www.nuget.org/packages/Azure.Identity/) om te verifiëren met Azure AD-referenties. Als u het preview-pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>.NET-code voorbeeld: een blok-Blob maken

Voeg de volgende `using`-instructies aan uw code toe om de Preview-versies van de identiteits-en Azure Storage-client bibliotheken van Azure te gebruiken.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

Maak een instantie van de [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -klasse om een token referentie op te halen die door uw code kan worden gebruikt om aanvragen voor Azure Storage te autoriseren. In het volgende code voorbeeld ziet u hoe de referenties van de geverifieerde token worden opgehaald en gebruikt om een service-client object te maken. vervolgens gebruikt u de service-client om een nieuwe BLOB te uploaden:

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
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Als u aanvragen voor BLOB-of wachtrij gegevens met Azure AD wilt autoriseren, moet u HTTPS voor die aanvragen gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Zie [toegangs rechten voor opslag gegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over RBAC-rollen voor Azure Storage.
- Zie voor meer informatie over het machtigen van toegang tot containers en wacht rijen in uw opslag toepassingen [Azure AD gebruiken met opslag toepassingen](storage-auth-aad-app.md).
- Zie voor meer informatie over het uitvoeren van Azure CLI-en Power shell-opdrachten met Azure AD-referenties [Azure CLI of Power shell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot BLOB-of wachtrij gegevens](storage-auth-aad-script.md).
