---
title: 'Azure VPN-gateway: genereer & exportcertificaten voor P2S: MakeCert'
description: Maak een zelfondertekend rootcertificaat, exporteer de openbare sleutel en genereer clientcertificaten met MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: ad2ab31e6771efc54238d5747863fa2a9bb2f356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75833975"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Certificaten genereren en exporteren voor Point-to-Site-verbindingen met MakeCert

Point-to-Site-verbindingen gebruiken certificaten om te verifiëren. In dit artikel ziet u hoe u een zelfondertekend rootcertificaat maakt en clientcertificaten genereert met MakeCert. Als u op zoek bent naar andere certificaatinstructies, zie [Certificaten - PowerShell](vpn-gateway-certificates-point-to-site.md) of [Certificaten - Linux](vpn-gateway-certificates-point-to-site-linux.md).

Hoewel we u aanraden de [Windows 10 PowerShell-stappen](vpn-gateway-certificates-point-to-site.md) te gebruiken om uw certificaten te maken, bieden we deze MakeCert-instructies als een optionele methode. De certificaten die u met een van beide methoden genereert, kunnen op [elk ondersteund besturingssysteem van de client](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)worden geïnstalleerd. MakeCert heeft echter de volgende beperking:

* MakeCert is afgeschaft. Dit betekent dat deze tool op elk moment kan worden verwijderd. Certificaten die u al hebt gegenereerd met MakeCert, worden niet beïnvloed wanneer MakeCert niet meer beschikbaar is. MakeCert wordt alleen gebruikt om de certificaten te genereren, niet als een validerend mechanisme.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Een zelfondertekend basiscertificaat maken

In de volgende stappen ziet u hoe u een zelfondertekend certificaat maakt met MakeCert. Deze stappen zijn niet specifiek voor het implementatiemodel. Ze zijn geldig voor zowel Resource Manager als klassiek.

1. MakeCert [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx)downloaden en installeren.
2. Na de installatie vindt u meestal het hulpprogramma makecert.exe onder dit pad: 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>'. Hoewel, het is mogelijk dat het werd geïnstalleerd op een andere locatie. Open een opdrachtprompt als beheerder en navigeer naar de locatie van het hulpprogramma MakeCert. U het volgende voorbeeld gebruiken, aanpassen voor de juiste locatie:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Een certificaat maken en installeren in het persoonlijke certificaatarchief op uw computer. In het volgende voorbeeld wordt een bijbehorend *.cer-bestand* geüpload dat u naar Azure uploadt bij het configureren van P2S. Vervang 'P2SRootCert' en 'P2SRootCert.cer' door de naam die u voor het certificaat wilt gebruiken. Het certificaat bevindt zich in uw 'Certificaten - Huidige gebruiker\Persoonlijk\Certificaten'.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Exporteer de openbare sleutel (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Het bestand exported.cer moet worden geüpload naar Azure. Zie Een [point-to-site-verbinding configureren](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)voor instructies. Zie [dit gedeelte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) van het artikel om een extra vertrouwd basiscertificaat toe te voegen.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Het zelfondertekende certificaat en de privésleutel exporteren om het op te slaan (optioneel)

U het zelfondertekende rootcertificaat exporteren en veilig opslaan. Indien nodig u het later op een andere computer installeren en meer clientcertificaten genereren of een ander .cer-bestand exporteren. Als u het zelfondertekende basiscertificaat wilt exporteren als een .pfx, selecteert u het basiscertificaat en gebruikt u dezelfde stappen als beschreven in [Een clientcertificaat exporteren](#clientexport).

## <a name="create-and-install-client-certificates"></a>Clientcertificaten maken en installeren

U installeert het zelfondertekende certificaat niet rechtstreeks op de clientcomputer. U moet een clientcertificaat genereren op basis van het zelfondertekende certificaat. Vervolgens exporteert en installeert u het clientcertificaat naar de clientcomputer. De volgende stappen zijn niet specifiek voor het implementatiemodel. Ze zijn geldig voor zowel Resource Manager als klassiek.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Een clientcertificaat genereren

Op elke clientcomputer die via punt-naar-site verbinding maakt met een VNet, moet een clientcertificaat zijn geïnstalleerd. U genereert een clientcertificaat op basis van het zelfondertekende rootcertificaat en exporteert en installeert vervolgens het clientcertificaat. Als het clientcertificaat niet is geïnstalleerd, mislukt de verificatie. 

De volgende stappen doorlopen u bij het genereren van een clientcertificaat van een zelfondertekend rootcertificaat. U meerdere clientcertificaten genereren vanuit hetzelfde basiscertificaat. Wanneer u clientcertificaten genereert met de onderstaande stappen, wordt het clientcertificaat automatisch geïnstalleerd op de computer die u hebt gebruikt om het certificaat te genereren. Als u een clientcertificaat op een andere clientcomputer wilt installeren, u het certificaat exporteren.
 
1. Open op dezelfde computer die u hebt gebruikt om het zelfondertekende certificaat te maken, een opdrachtprompt als beheerder.
2. Wijzig en voer het voorbeeld uit om een clientcertificaat te genereren.
   * Wijzig *"P2SRootCert"* in de naam van de zelfondertekende root waarvan u het clientcertificaat genereert. Zorg ervoor dat u de naam van het hoofdcertificaat gebruikt, wat ook de 'CN='-waarde is die u hebt opgegeven toen u de zelfondertekende root maakte.
   * Wijzig *P2SChildCert* in de naam die u wilt genereren.

   Als u het volgende voorbeeld uitvoert zonder het te wijzigen, is het resultaat een clientcertificaat met de naam P2SChildcert in uw persoonlijke certificaatarchief dat is gegenereerd op basiscertificaat P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Een clientcertificaat exporteren

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Een geëxporteerd clientcertificaat installeren

Zie [Een clientcertificaat installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)als u een clientcertificaat wilt installeren.

## <a name="next-steps"></a>Volgende stappen

Ga verder met uw Point-to-Site-configuratie. 

* Zie [P2S configureren met native Azure-certificaatverificatie](vpn-gateway-howto-point-to-site-resource-manager-portal.md)voor implementatiemodelstappen voor **Resource Manager.**
* Zie [Een Point-to-Site VPN-verbinding configureren op een VNet (klassiek)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)voor **klassieke** implementatiemodelstappen.

Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshooting Azure point-to-site connections](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).