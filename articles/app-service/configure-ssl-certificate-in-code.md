---
title: SSL-certificaat gebruiken in code-Azure App Service | Microsoft Docs
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
ms.date: 11/04/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 93dfe784d45cd9cd93d22c5e8c3275c563f7f88b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572085"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Gebruik een SSL-certificaat in uw code in Azure App Service

In de toepassings code kunt u toegang krijgen tot de [open bare of persoonlijke certificaten die u toevoegt aan app service](configure-ssl-certificate.md). Uw app-code kan fungeren als een client en toegang krijgen tot een externe service waarvoor verificatie via een certificaat vereist is, of moet mogelijk cryptografische taken uitvoeren. In deze hand leiding vindt u informatie over het gebruik van open bare of persoonlijke certificaten in de code van uw toepassing.

Deze methode voor het gebruik van certificaten in uw code maakt gebruik van de SSL-functionaliteit in App Service. hiervoor moet uw app in de **Basic** -laag of hoger zijn. Als uw app zich in de **vrije** of **gedeelde** laag bevindt, kunt u [het certificaat bestand toevoegen aan de opslag plaats van uw app](#load-certificate-from-file).

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

## <a name="make-the-certificate-accessible"></a>Het certificaat toegankelijk maken

Als u toegang wilt krijgen tot een certificaat in uw app-code, voegt u de vinger afdruk toe aan de `WEBSITE_LOAD_CERTIFICATES` app-instelling door de volgende opdracht uit te voeren in de <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Als u al uw certificaten toegankelijk wilt maken, stelt u de waarde in op `*`.

## <a name="load-certificate-in-windows-apps"></a>Certificaat in Windows-apps laden

Met de instelling voor de `WEBSITE_LOAD_CERTIFICATES`-app worden de opgegeven certificaten toegankelijk voor de Windows-app die wordt gehost in het Windows-certificaat archief. de locatie is afhankelijk van de [prijs categorie](overview-hosting-plans.md):

- **Geïsoleerde** laag-in [lokale Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Alle andere lagen: in [huidige User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

In C# code opent u het certificaat via de vinger afdruk van het certificaat. Met de volgende code wordt een certificaat geladen met de vinger afdruk `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

In Java-code opent u het certificaat in de Store ' Windows-mijn ' met behulp van het veld algemene naam voor het onderwerp (Zie [certificaat voor open bare sleutels](https://en.wikipedia.org/wiki/Public_key_certificate)). De volgende code laat zien hoe u een certificaat van een persoonlijke sleutel kunt laden:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Zie [certificaat uit bestand laden](#load-certificate-from-file)voor talen die niet de ondersteuning bieden voor of het Windows-certificaat archief onvoldoende ondersteunen.

## <a name="load-certificate-in-linux-apps"></a>Certificaat in Linux-apps laden

Met de instellingen voor de `WEBSITE_LOAD_CERTIFICATES`-app kunnen de opgegeven certificaten toegankelijk zijn voor uw door Linux gehoste apps (inclusief aangepaste container-apps) als bestanden. De bestanden bevinden zich in de volgende directory's:

- Privé certificaten-`/var/ssl/private` (`.p12`-bestanden)
- Open bare certificaten-`/var/ssl/certs` (`.der`-bestanden)

De namen van de certificaat bestanden zijn de certificaat vingerafdrukken. De volgende C# code laat zien hoe u een openbaar certificaat in een Linux-app kunt laden.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Zie de documentatie voor de desbetreffende taal of het webplatform voor meer informatie over het laden van een SSL-certificaat van een bestand in node. js, PHP, Python, Java of Ruby.

## <a name="load-certificate-from-file"></a>Certificaat uit bestand laden

Als u een certificaat bestand moet laden dat u hand matig uploadt, is het beter om het certificaat te uploaden met behulp van [FTPS](deploy-ftp.md) in plaats van [Git](deploy-local-git.md). Houd er rekening mee dat gevoelige gegevens, zoals een persoonlijk certificaat, niet in broncode beheer worden bewaard.

> [!NOTE]
> ASP.NET en ASP.NET Core in Windows moeten toegang hebben tot het certificaat archief, zelfs als u een certificaat uit een bestand laadt. Als u een certificaat bestand in een Windows .NET-app wilt laden, laadt u het huidige gebruikers profiel met de volgende opdracht in het <a target="_blank" href="https://shell.azure.com" >Cloud shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```

In het C# volgende voor beeld wordt een openbaar certificaat geladen vanuit een relatief pad in uw app:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Zie de documentatie voor de desbetreffende taal of het webplatform voor meer informatie over het laden van een SSL-certificaat van een bestand in node. js, PHP, Python, Java of Ruby.

## <a name="more-resources"></a>Meer bronnen

* [Een aangepaste DNS-naam beveiligen met een SSL-binding](configure-ssl-bindings.md)
* [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](configure-ssl-bindings.md#enforce-tls-versions)
* [Veelgestelde vragen: App Service certificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
