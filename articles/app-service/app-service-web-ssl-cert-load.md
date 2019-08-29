---
title: SSL-client certificaat gebruiken in toepassings code-Azure App Service | Microsoft Docs
description: Meer informatie over hoe u client certificaten gebruikt om verbinding te maken met externe bronnen waarvoor ze nodig zijn.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6820daf34e63fd48e83c645e7509a3256bc8435b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066996"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Gebruik een SSL-certificaat in de code van uw toepassing in Azure App Service

In deze hand leiding vindt u informatie over het gebruik van open bare of persoonlijke certificaten in de code van uw toepassing. Een voorbeeld van deze use-case is dat uw app toegang heeft tot een externe service die certificaatverificatie vereist.

Deze methode voor het gebruik van certificaten in uw code maakt gebruik van de SSL-functionaliteit in App Service. hiervoor moet uw app in de **Basic** -laag of hoger zijn. U kunt ook [het certificaat bestand in uw app-opslag plaats toevoegen](#load-certificate-from-file), maar dit is geen aanbevolen procedure voor persoonlijke certificaten.

Als u App Service uw SSL-certificaten laat beheren, kunt u de certificaten afzonderlijk houden van de code van uw toepassing en zo uw gevoelige gegevens beschermen.

## <a name="upload-a-private-certificate"></a>Een persoonlijk certificaat uploaden

Voordat u een persoonlijk certificaat uploadt, moet u ervoor zorgen dat [het voldoet aan alle vereisten](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), behalve dat het niet hoeft te worden geconfigureerd voor Server verificatie.

Wanneer u klaar bent om te uploaden, voert u de volgende opdracht uit in de <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Kopieer de vinger afdruk van het certificaat en Zie [het certificaat toegankelijk maken](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Een openbaar certificaat uploaden

Open bare certificaten worden ondersteund in de *. CER* -indeling. Als u een openbaar certificaat wilt uploaden, het <a href="https://portal.azure.com" target="_blank">Azure Portal</a>en navigeert u naar uw app.

Klik op **SSL-instellingen** > **open bare certificaten (. CER)**  > **openbaar certificaat uploaden** vanuit de linkernavigatiebalk van uw app.

Typ in **naam**een naam voor het certificaat. Selecteer in **CER-certificaat bestand**uw CER-bestand.

Klik op **Uploaden**.

![Openbaar certificaat uploaden](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Nadat het certificaat is geüpload, kopieert u de vinger afdruk van het certificaat en raadpleegt [u het certificaat toegankelijk maken](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Een App Service-certificaat importeren

Zie [een SSL-certificaat kopen en configureren voor Azure app service](web-sites-purchase-ssl-web-site.md).

Nadat het certificaat is geïmporteerd, kopieert u de vinger afdruk van het certificaat en raadpleegt [u het certificaat toegankelijk maken](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Het certificaat toegankelijk maken

Als u een geüpload of geïmporteerd certificaat in uw app-code wilt gebruiken, maakt u de `WEBSITE_LOAD_CERTIFICATES` vinger afdruk toegankelijk met de app-instelling door de volgende opdracht uit te voeren in de <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Als u al uw certificaten toegankelijk wilt maken, stelt u `*`de waarde in op.

> [!NOTE]
> Met deze instelling worden de opgegeven certificaten in de [huidige User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) -Store voor de meeste prijs categorieën geplaatst, maar in de geïsoleerde laag (dat wil zeggen dat de app wordt uitgevoerd in een [app service Environment](environment/intro.md)), worden de certificaten in de [lokale Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) geplaatst Store.
>

![App-instelling configureren](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Wanneer u klaar bent, klikt u op **Opslaan**.

De geconfigureerde certificaten zijn nu klaar om door uw code te worden gebruikt.

## <a name="load-the-certificate-in-code"></a>Het certificaat in code laden

Wanneer uw certificaat toegankelijk is, kunt u het benaderen in C#-code door de vingerafdruk van het certificaat. De volgende code laadt een certificaat met de vingerafdruk `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>Certificaat uit bestand laden

Als u een certificaat bestand uit de toepassingsmap wilt laden, is het beter om het te uploaden met behulp van [FTPS](deploy-ftp.md) in plaats van [Git](deploy-local-git.md). Houd er rekening mee dat gevoelige gegevens, zoals een persoonlijk certificaat, niet in broncode beheer worden bewaard.

Hoewel u het bestand rechtstreeks in uw .NET-code laadt, controleert de bibliotheek nog steeds of het huidige gebruikers profiel is geladen. Als u het huidige gebruikers profiel wilt laden, `WEBSITE_LOAD_USER_PROFILE` stelt u de app-instelling in met de volgende opdracht in het <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Zodra deze instelling is ingesteld, wordt in C# het volgende voor beeld een `mycert.pfx` certificaat geladen `certs` dat wordt aangeroepen vanuit de map van de opslag plaats van uw app.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
