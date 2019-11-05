---
title: SSL-certificaat in de toepassings code gebruiken-Azure App Service | Microsoft Docs
description: Meer informatie over hoe u client certificaten gebruikt om verbinding te maken met externe bronnen waarvoor ze nodig zijn.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513690"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Gebruik een SSL-certificaat in de code van uw toepassing in Azure App Service

Uw App Service app-code kan fungeren als een client en toegang krijgen tot een externe service waarvoor verificatie via een certificaat is vereist. In deze hand leiding vindt u informatie over het gebruik van open bare of persoonlijke certificaten in de code van uw toepassing.

Deze methode voor het gebruik van certificaten in uw code maakt gebruik van de SSL-functionaliteit in App Service. hiervoor moet uw app in de **Basic** -laag of hoger zijn. U kunt ook [het certificaat bestand in uw app-opslag plaats toevoegen](#load-certificate-from-file), maar dit is geen aanbevolen procedure voor persoonlijke certificaten.

Wanneer u uw SSL-certificaten App Service beheren, kunt u de certificaten en de code van uw toepassing afzonderlijk onderhouden en uw gevoelige gegevens beveiligen.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volgen:

- [Een App Service-app maken](/azure/app-service/)
- [Een certificaat toevoegen aan uw app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>De vinger afdruk zoeken

Selecteer in het menu links in het <a href="https://portal.azure.com" target="_blank">Azure Portal</a> **app Services** > \<naam van de **app >** .

Selecteer in de linkernavigatiebalk van uw app instellingen voor **TLS/SSL**en selecteer vervolgens **certificaten voor persoonlijke sleutels (PFX)** of certificaten met een **open bare sleutel (. CER)** .

Zoek het certificaat dat u wilt gebruiken en kopieer de vinger afdruk.

![De vinger afdruk van het certificaat kopiëren](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>Het certificaat laden

Als u een certificaat in uw app-code wilt gebruiken, voegt u de vinger afdruk toe aan de `WEBSITE_LOAD_CERTIFICATES` app-instelling door de volgende opdracht uit te voeren in de <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Als u al uw certificaten toegankelijk wilt maken, stelt u de waarde in op `*`.

> [!NOTE]
> Met deze instelling worden de opgegeven certificaten in de [huidige User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) -Store voor de meeste prijs categorieën geplaatst, maar in de **geïsoleerde** laag (dat wil zeggen dat de app wordt uitgevoerd in een [app service Environment](environment/intro.md)), worden de certificaten in de [lokale Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) geplaatst Store.
>

De geconfigureerde certificaten zijn nu klaar om door uw code te worden gebruikt.

## <a name="load-the-certificate-in-code"></a>Het certificaat in code laden

Zodra uw certificaat toegankelijk is, kunt u het openen C# in code door de vinger afdruk van het certificaat. Met de volgende code wordt een certificaat geladen met de vinger afdruk `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

Hoewel u het bestand rechtstreeks in uw .NET-code laadt, controleert de bibliotheek nog steeds of het huidige gebruikers profiel is geladen. Als u het huidige gebruikers profiel wilt laden, stelt u de `WEBSITE_LOAD_USER_PROFILE` app-instelling in met de volgende opdracht in de <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Zodra deze instelling is ingesteld, wordt in C# het volgende voor beeld een certificaat met de naam `mycert.pfx` geladen vanuit de `certs` map van de opslag plaats van uw app.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>Meer bronnen

* [Een aangepaste DNS-naam beveiligen met een SSL-binding](configure-ssl-bindings.md)
* [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](configure-ssl-bindings.md#enforce-tls-versions)
* [Veelgestelde vragen: App Service certificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
