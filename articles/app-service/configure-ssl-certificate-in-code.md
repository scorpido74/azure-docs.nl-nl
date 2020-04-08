---
title: Een TLS/SSL-certificaat in code gebruiken
description: Meer informatie over het gebruik van clientcertificaten in uw code. Verifieer met externe bronnen met een clientcertificaat of voer cryptografische taken met hen uit.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811694"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Een TLS/SSL-certificaat gebruiken in uw code in Azure App Service

In uw toepassingscode hebt u toegang tot de openbare of privécertificaten die [u toevoegt aan App Service.](configure-ssl-certificate.md) Uw app-code kan fungeren als een client en toegang krijgen tot een externe service waarvoor certificaatverificatie vereist is, of het kan nodig zijn om cryptografische taken uit te voeren. Deze handleiding laat zien hoe u openbare of particuliere certificaten in uw toepassingscode gebruiken.

Deze benadering van het gebruik van certificaten in uw code maakt gebruik van de TLS-functionaliteit in App Service, waarvoor uw app in **de basislaag** of hoger moet zijn. Als uw app zich in de laag **Gratis** of **Gedeeld bevindt,** u het [certificaatbestand opnemen in uw app-opslagplaats.](#load-certificate-from-file)

Wanneer u App Service uw TLS/SSL-certificaten laat beheren, u de certificaten en uw toepassingscode afzonderlijk onderhouden en uw gevoelige gegevens beveiligen.

## <a name="prerequisites"></a>Vereisten

Volg deze handleiding:

- [Een App Service-app maken](/azure/app-service/)
- [Een certificaat toevoegen aan uw app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Zoek de duimafdruk

Selecteer in de <a href="https://portal.azure.com" target="_blank">Azure-portal</a>in het linkermenu de>**\<app-naam app-naam ** **van App Services.** > 

Selecteer in de linkernavigatie van uw app **TLS/SSL-instellingen**en selecteer **vervolgens Private Key Certificates (.pfx)** of Public Key Certificates **(.cer).**

Zoek het certificaat dat u wilt gebruiken en kopieer de duimafdruk.

![De duimafdruk van het certificaat kopiëren](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Het certificaat toegankelijk maken

Als u toegang wilt krijgen tot een certificaat `WEBSITE_LOAD_CERTIFICATES` in uw app-code, voegt u de duimafdruk toe aan de app-instelling door de volgende opdracht in de <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>uit te voeren:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Als u al uw certificaten toegankelijk `*`wilt maken, stelt u de waarde in op .

## <a name="load-certificate-in-windows-apps"></a>Certificaat laden in Windows-apps

Met `WEBSITE_LOAD_CERTIFICATES` de instelling van de app zijn de opgegeven certificaten toegankelijk voor uw windows gehoste app in het Windows-certificaatarchief en is de locatie afhankelijk van de [prijscategorie:](overview-hosting-plans.md)

- **Geïsoleerde** laag - in [Lokale Machine\Mijn](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Alle andere lagen - in [Huidige gebruiker\Mijn](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

In C#-code krijgt u toegang tot het certificaat via de duimafdruk van het certificaat. De volgende code laadt een `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`certificaat met de duimafdruk .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

In Java-code krijgt u toegang tot het certificaat vanuit het "Windows-MY"-archief met het veld Algemene naam onderwerp (zie [certificaat van openbare sleutel](https://en.wikipedia.org/wiki/Public_key_certificate)). In de volgende code ziet u hoe u een privésleutelcertificaat laadt:

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

Zie [Certificaat laden uit bestand](#load-certificate-from-file)voor talen die geen ondersteuning bieden voor het Windows-certificaatarchief.

## <a name="load-certificate-in-linux-apps"></a>Certificaat laden in Linux-apps

De `WEBSITE_LOAD_CERTIFICATES` app-instellingen maken de opgegeven certificaten toegankelijk voor uw door Linux gehoste apps (inclusief aangepaste container-apps) als bestanden. De bestanden zijn te vinden onder de volgende mappen:

- Privécertificaten `/var/ssl/private` - `.p12` ( bestanden)
- Openbare certificaten `/var/ssl/certs` `.der` - ( bestanden)

De bestandsnamen van het certificaat zijn de duimafdrukken van het certificaat. De volgende C#-code laat zien hoe u een openbaar certificaat laadt in een Linux-app.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Zie de documentatie voor de betreffende taal of webplatform om te zien hoe u een TLS/SSL-certificaat laden vanuit een bestand in Node.js, PHP, Python, Java of Ruby.

## <a name="load-certificate-from-file"></a>Certificaat laden uit bestand

Als u een certificaatbestand moet laden dat u handmatig uploadt, u het certificaat beter uploaden met [FTPS](deploy-ftp.md) in plaats van [Git,](deploy-local-git.md)bijvoorbeeld. U moet gevoelige gegevens zoals een privécertificaat buiten bronbeheer houden.

> [!NOTE]
> ASP.NET en ASP.NET Core op Windows moeten toegang krijgen tot het certificaatarchief, zelfs als u een certificaat uit een bestand laadt. Als u een certificaatbestand wilt laden in een Windows .NET-app, laadt u het huidige gebruikersprofiel met de volgende opdracht in de <a target="_blank" href="https://shell.azure.com" >Cloud Shell:</a>
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Deze benadering van het gebruik van certificaten in uw code maakt gebruik van de TLS-functionaliteit in App Service, waarvoor uw app in **de basislaag** of hoger moet zijn.

In het volgende voorbeeld Van C# wordt een openbaar certificaat geladen vanaf een relatief pad in uw app:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Zie de documentatie voor de betreffende taal of webplatform om te zien hoe u een TLS/SSL-certificaat laden vanuit een bestand in Node.js, PHP, Python, Java of Ruby.

## <a name="more-resources"></a>Meer bronnen

* [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)
* [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](configure-ssl-bindings.md#enforce-tls-versions)
* [Veelgestelde vragen: App-servicecertificaten](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
