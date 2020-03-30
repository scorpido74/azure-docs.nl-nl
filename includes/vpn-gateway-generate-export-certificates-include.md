---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e85dc8c079205484db9b7b7c43a0086f69feb3be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059932"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Een zelfondertekend basiscertificaat maken

Gebruik de cmdlet Nieuw-SelfSignedCertificate om een zelfondertekend rootcertificaat te maken. Zie [Nieuw-SelfSignedCertificate voor](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)aanvullende parameterinformatie .

1. Open vanaf een computer met Windows 10 of Windows Server 2016 een Windows PowerShell-console met verhoogde bevoegdheden. Deze voorbeelden werken niet in de Azure Cloud Shell "Try It". U moet deze voorbeelden lokaal uitvoeren.
2. Gebruik het volgende voorbeeld om het zelfondertekende rootcertificaat te maken. In het volgende voorbeeld wordt een zelfondertekend rootcertificaat met de naam 'P2SRootCert' dat automatisch wordt geïnstalleerd in 'Certificates-Current User\Personal\Certificates'. U het certificaat bekijken door *certmgr.msc*te openen of *Gebruikerscertificaten beheren.*

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```
 3. Laat de PowerShell-console open als u direct na het maken van dit hoofdcertificaat een clientcertificaat wilt maken.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Een clientcertificaat genereren

Op elke clientcomputer die via punt-naar-site verbinding maakt met een VNet, moet een clientcertificaat zijn geïnstalleerd. U genereert een clientcertificaat op basis van het zelfondertekende rootcertificaat en exporteert en installeert vervolgens het clientcertificaat. Als het clientcertificaat niet is geïnstalleerd, mislukt de verificatie. 

De volgende stappen doorlopen u bij het genereren van een clientcertificaat van een zelfondertekend rootcertificaat. U meerdere clientcertificaten genereren vanuit hetzelfde basiscertificaat. Wanneer u clientcertificaten genereert met de onderstaande stappen, wordt het clientcertificaat automatisch geïnstalleerd op de computer die u hebt gebruikt om het certificaat te genereren. Als u een clientcertificaat op een andere clientcomputer wilt installeren, u het certificaat exporteren.

De voorbeelden gebruiken de cmdlet Nieuw-SelfSignedCertificate om een clientcertificaat te genereren dat binnen een jaar verloopt. Zie [Nieuw-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)voor aanvullende parametergegevens, zoals het instellen van een andere vervaldatum voor het clientcertificaat.

### <a name="example-1---powershell-console-session-still-open"></a>Voorbeeld 1 - PowerShell-consolesessie nog steeds geopend

Gebruik dit voorbeeld als u uw PowerShell-console niet hebt gesloten nadat u het zelfondertekende rootcertificaat hebt gemaakt. Dit voorbeeld gaat verder uit de vorige sectie en gebruikt de opgegeven variabele '$cert'. Als u de PowerShell-console hebt gesloten na het maken van het zelfondertekende basiscertificaat of extra clientcertificaten hebt gemaakt in een nieuwe PowerShell-consolesessie, gebruikt u de stappen in [voorbeeld 2](#ex2).

Wijzig en voer het voorbeeld uit om een clientcertificaat te genereren. Als u het volgende voorbeeld uitvoert zonder het te wijzigen, is het resultaat een clientcertificaat met de naam 'P2SChildCert'.  Als u het onderliggendcertificaat iets anders wilt noemen, wijzigt u de waarde van de GN. Wijzig de textextension niet wanneer u dit voorbeeld uitvoert. Het clientcertificaat dat u genereert, wordt automatisch geïnstalleerd in 'Certificaten - Huidige gebruiker\Persoonlijke\certificaten' op uw computer.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Voorbeeld 2 - Nieuwe PowerShell-consolesessie

Als u aanvullende clientcertificaten maakt of niet dezelfde PowerShell-sessie gebruikt die u hebt gebruikt om uw zelfondertekende rootcertificaat te maken, gebruikt u de volgende stappen:

1. Identificeer het zelfondertekende rootcertificaat dat op de computer is geïnstalleerd. Met deze cmdlet wordt een lijst met certificaten geretourneerd die op uw computer zijn geïnstalleerd.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Zoek de onderwerpnaam uit de geretourneerde lijst en kopieer vervolgens de duimafdruk die ernaast zich bevindt naar een tekstbestand. In het volgende voorbeeld zijn er twee certificaten. De CN-naam is de naam van het zelfondertekende rootcertificaat waaruit u een onderliggend certificaat wilt genereren. In dit geval 'P2SRootCert'.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Declareer een variabele voor het basiscertificaat met behulp van de duimafdruk van de vorige stap. Vervang DUIMAFDRUK door de duimafdruk van het basiscertificaat waaruit u een onderliggend certificaat wilt genereren.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Met behulp van de duimafdruk voor P2SRootCert in de vorige stap ziet de variabele er bijvoorbeeld als volgt uit:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Wijzig en voer het voorbeeld uit om een clientcertificaat te genereren. Als u het volgende voorbeeld uitvoert zonder het te wijzigen, is het resultaat een clientcertificaat met de naam 'P2SChildCert'. Als u het onderliggendcertificaat iets anders wilt noemen, wijzigt u de waarde van de GN. Wijzig de textextension niet wanneer u dit voorbeeld uitvoert. Het clientcertificaat dat u genereert, wordt automatisch geïnstalleerd in 'Certificaten - Huidige gebruiker\Persoonlijke\certificaten' op uw computer.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>De hoofdcertificaatsleutel exporteren (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Het zelfondertekende rootcertificaat en de privésleutel exporteren om het op te slaan (optioneel)

U het zelfondertekende rootcertificaat exporteren en veilig opslaan als back-up. Indien nodig u het later op een andere computer installeren en meer clientcertificaten genereren. Als u het zelfondertekende basiscertificaat wilt exporteren als een .pfx, selecteert u het basiscertificaat en gebruikt u dezelfde stappen als beschreven in [Een clientcertificaat exporteren](#clientexport).

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Het clientcertificaat exporteren

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
