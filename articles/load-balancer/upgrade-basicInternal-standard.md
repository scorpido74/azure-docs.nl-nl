---
title: Upgraden van Basic Internal naar Standard Internal - Azure Load Balancer
description: In dit artikel ziet u hoe u Azure Internal Load Balancer upgraden van Basic SKU naar Standaard SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770391"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure Internal Load Balancer upgraden- geen uitgaande verbinding vereist
[Azure Standard Load Balancer](load-balancer-overview.md) biedt een uitgebreide set functionaliteit en hoge beschikbaarheid door zoneredundantie. Zie [vergelijkingstabel](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)voor meer informatie over Load Balancer SKU.

Dit artikel introduceert een PowerShell-script dat een Standaard Load Balancer maakt met dezelfde configuratie als de Basic Load Balancer, samen met het migreren van verkeer van Basic Load Balancer naar Standard Load Balancer.

## <a name="upgrade-overview"></a>Overzicht van de upgrade

Er is een Azure PowerShell-script beschikbaar dat het volgende doet:

* Hiermee maakt u een standaard interne SKU-loadbalancer op de locatie die u opgeeft. Houd er rekening mee dat er geen [uitgaande verbinding](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) wordt geleverd door de standaard interne load balancer.
* Kopieert naadloos de configuraties van de Basic SKU Load Balancer naar de nieuw gecreëerde Standard Load Balancer.
* Verplaats de privé-IP's naadloos van Basic Load Balancer naar de nieuw gecreëerde Standard Load Balancer.
* Verplaats de VM's naadloos van backendpool van de Basic Load Balancer naar de backendpool van de Standaard Load Balancer

### <a name="caveatslimitations"></a>Kanttekeningen\Beperkingen

* Script ondersteunt alleen de interne upgrade van load balancer wanneer er geen uitgaande verbinding nodig is. Als u voor sommige van uw VM's [een uitgaande verbinding](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) nodig hebt, raadpleegt u deze [pagina](upgrade-InternalBasic-To-PublicStandard.md) voor instructies. 
* Als de standaardloadbalancer in een andere regio is gemaakt, u de VM's die in het oude gebied bestaan niet koppelen aan de nieuw gemaakte Standaardload Balancer. Als u deze beperking wilt omzeilen, moet u een nieuwe virtuele machine maken in de nieuwe regio.
* Als uw Load Balancer geen IP-configuratie of backendpool aan de frontend heeft, loopt u waarschijnlijk een fout op met het script. Zorg ervoor dat ze niet leeg zijn.

## <a name="download-the-script"></a>Het script downloaden

Download het migratiescript uit de [PowerShell-galerie](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Het script gebruiken

Er zijn twee opties voor u, afhankelijk van de installatie en voorkeuren van uw lokale PowerShell-omgeving:

* Als u de Azure Az-modules niet hebt geïnstalleerd of het niet erg vindt om de `Install-Script` Azure Az-modules te verwijderen, u de optie het beste gebruiken om het script uit te voeren.
* Als u de Azure Az-modules moet behouden, u het beste het script downloaden en het rechtstreeks uitvoeren.

Voer de Azure Az-modules uit `Get-InstalledModule -Name az`om te bepalen of u de Azure Az-modules hebt geïnstalleerd. Als u geen geïnstalleerde Az-modules ziet, kunt `Install-Script` u de methode gebruiken.

### <a name="install-using-the-install-script-method"></a>Installeren met de methode Install-Script

Als u deze optie wilt gebruiken, mag u de Azure Az-modules niet op uw computer hebben geïnstalleerd. Als ze zijn geïnstalleerd, wordt in de volgende opdracht een fout weergegeven. U de Azure Az-modules verwijderen of de andere optie gebruiken om het script handmatig te downloaden en uit te voeren.
  
Voer het script uit met de volgende opdracht:

`Install-Script -Name AzureILBUpgrade`

Deze opdracht installeert ook de vereiste Az-modules.  

### <a name="install-using-the-script-directly"></a>Direct installeren met behulp van het script

Als u sommige Azure Az-modules hebt geïnstalleerd en deze niet verwijderen (of deze niet wilt verwijderen), u het script handmatig downloaden via het tabblad **Handmatig downloaden** in de downloadkoppeling voor scripts. Het script wordt gedownload als een ruw nupkg-bestand. Zie Handmatig pakket downloaden als u het script vanuit dit nupkg-bestand wilt [installeren.](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)

Het script uitvoeren:

1. Gebruiken `Connect-AzAccount` om verbinding te maken met Azure.

1. Gebruik `Import-Module Az` om de Az-modules te importeren.

1. Onderzoek de vereiste parameters:

   * **rgName: [String]: Vereist** – Dit is de resourcegroep voor uw bestaande Basic Load Balancer en nieuwe Standard Load Balancer. Als u deze tekenreekswaarde wilt vinden, navigeert u naar azure-portal, selecteert u de bron Basic Load Balancer en klikt u op het **overzicht** voor de load balancer. De resourcegroep bevindt zich op die pagina.
   * **oldLBName: [Tekenreeks]: vereist** – Dit is de naam van uw bestaande Basisbalansdie u wilt upgraden. 
   * **newlocation: [String]: Vereist** – Dit is de locatie waar de Standaard Load Balancer wordt gemaakt. Het wordt aanbevolen om dezelfde locatie van de gekozen Basislastbalansr over te nemen aan de Standaardloadbalancer voor een betere koppeling met andere bestaande resources.
   * **newLBName: [Tekenreeks]: vereist** – Dit is de naam voor de standaardloadbalans die moet worden gemaakt.
1. Voer het script uit met de juiste parameters. Het kan vijf tot zeven minuten duren om te voltooien.

    **Voorbeeld**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Zijn er beperkingen met het Azure PowerShell-script om de configuratie van v1 naar v2 te migreren?

Ja. Zie [kanttekeningen/beperkingen](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Schakelt het Azure PowerShell-script ook het verkeer over van mijn Basic Load Balancer naar de nieuw gemaakte Standard Load Balancer?

Ja, het migreert verkeer. Als u verkeer persoonlijk wilt migreren, gebruikt u [dit script](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) dat vm's niet voor u verplaatst.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Ik liep in een aantal problemen met het gebruik van dit script. Hoe kan ik hulp krijgen?
  
U een slbupgradesupport@microsoft.come-mail verzenden naar, een ondersteuningsaanvraag openen met Azure Support of beide doen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Standard Load Balancer](load-balancer-overview.md)
