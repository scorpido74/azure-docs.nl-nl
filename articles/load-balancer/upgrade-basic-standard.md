---
title: Upgraden van Basic Public naar Standard Public - Azure Load Balancer
description: In dit artikel ziet u hoe u Azure Public Load Balancer upgraden van Basic SKU naar Standaard SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: a2d6f41756d87e43ac7db9e6a8670c453920c834
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770372"
---
# <a name="upgrade-azure-public-load-balancer"></a>Azure Public Load Balancer upgraden
[Azure Standard Load Balancer](load-balancer-overview.md) biedt een uitgebreide set functionaliteit en hoge beschikbaarheid door zoneredundantie. Zie [vergelijkingstabel](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)voor meer informatie over Load Balancer SKU.

Er zijn drie fasen in een upgrade:

1. De configuratie migreren
2. VM's toevoegen aan backendpools van Standard Load Balancer

Dit artikel behandelt configuratiemigratie. Het toevoegen van VM's aan backendpools kan variëren afhankelijk van uw specifieke omgeving. Er worden echter enkele algemene aanbevelingen op hoog niveau [gedaan](#add-vms-to-backend-pools-of-standard-load-balancer).

## <a name="upgrade-overview"></a>Overzicht van de upgrade

Er is een Azure PowerShell-script beschikbaar dat het volgende doet:

* Hiermee maakt u een standaard SKU-loadbalancer in de resourcegroep en de locatie die u opgeeft.
* Kopieert naadloos de configuraties van de Basic SKU Load Balancer naar de nieuw gemaakte Standaard Load Balancer.
* Hiermee maakt u een standaard uitgaande regel die uitgaande connectiviteit mogelijk maakt.

### <a name="caveatslimitations"></a>Kanttekeningen\Beperkingen

* Script ondersteunt alleen de upgrade van Public Load Balancer. Raadpleeg [deze pagina](https://docs.microsoft.com/azure/load-balancer/upgrade-basicinternal-standard) voor de upgrade van Interne basislastbalansen voor instructies.
* De Standaard Load Balancer heeft een nieuw openbaar adres. Het is onmogelijk om de IP-adressen die zijn gekoppeld aan bestaande Basic Load Balancer naadloos te verplaatsen naar Standard Load Balancer, omdat ze verschillende SKU's hebben.
* Als de standaardloadbalancer in een andere regio is gemaakt, u de VM's die in het oude gebied bestaan niet koppelen aan de nieuw gemaakte Standaardload Balancer. Als u deze beperking wilt omzeilen, moet u een nieuwe virtuele machine maken in de nieuwe regio.
* Als uw Load Balancer geen IP-configuratie of backendpool aan de frontend heeft, loopt u waarschijnlijk een fout op met het script. Zorg ervoor dat ze niet leeg zijn.

## <a name="download-the-script"></a>Het script downloaden

Download het migratiescript uit de [PowerShell-galerie](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/2.0).
## <a name="use-the-script"></a>Het script gebruiken

Er zijn twee opties voor u, afhankelijk van de installatie en voorkeuren van uw lokale PowerShell-omgeving:

* Als u de Azure Az-modules niet hebt geïnstalleerd of het niet erg vindt om de `Install-Script` Azure Az-modules te verwijderen, u de optie het beste gebruiken om het script uit te voeren.
* Als u de Azure Az-modules moet behouden, u het beste het script downloaden en het rechtstreeks uitvoeren.

Voer de Azure Az-modules uit `Get-InstalledModule -Name az`om te bepalen of u de Azure Az-modules hebt geïnstalleerd. Als u geen geïnstalleerde Az-modules ziet, kunt `Install-Script` u de methode gebruiken.

### <a name="install-using-the-install-script-method"></a>Installeren met de methode Install-Script

Als u deze optie wilt gebruiken, mag u de Azure Az-modules niet op uw computer hebben geïnstalleerd. Als ze zijn geïnstalleerd, wordt in de volgende opdracht een fout weergegeven. U de Azure Az-modules verwijderen of de andere optie gebruiken om het script handmatig te downloaden en uit te voeren.
  
Voer het script uit met de volgende opdracht:

`Install-Script -Name AzurePublicLBUpgrade`

Deze opdracht installeert ook de vereiste Az-modules.  

### <a name="install-using-the-script-directly"></a>Direct installeren met behulp van het script

Als u sommige Azure Az-modules hebt geïnstalleerd en deze niet verwijderen (of deze niet wilt verwijderen), u het script handmatig downloaden via het tabblad **Handmatig downloaden** in de downloadkoppeling voor scripts. Het script wordt gedownload als een ruw nupkg-bestand. Zie Handmatig pakket downloaden als u het script vanuit dit nupkg-bestand wilt [installeren.](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)

Het script uitvoeren:

1. Gebruiken `Connect-AzAccount` om verbinding te maken met Azure.

1. Gebruik `Import-Module Az` om de Az-modules te importeren.

1. Onderzoek de vereiste parameters:

   * **oldRgName: [Tekenreeks]: vereist** – Dit is de resourcegroep voor uw bestaande Basislastbalansr die u wilt upgraden. Als u deze tekenreekswaarde wilt vinden, navigeert u naar azure-portal, selecteert u de bron Basic Load Balancer en klikt u op het **overzicht** voor de load balancer. De resourcegroep bevindt zich op die pagina.
   * **oldLBName: [Tekenreeks]: vereist** – Dit is de naam van uw bestaande Basisbalansdie u wilt upgraden. 
   * **newrgName: [String]: Vereist** – Dit is de resourcegroep waarin de StandaardLoad Balancer wordt gemaakt. Het kan een nieuwe resourcegroep of een bestaande groep zijn. Als u een bestaande resourcegroep kiest, moet de naam van de load balancer uniek zijn binnen de resourcegroep. 
   * **newlocation: [String]: Vereist** – Dit is de locatie waar de Standaard Load Balancer wordt gemaakt. Het wordt aanbevolen om dezelfde locatie van de gekozen Basislastbalansr over te nemen aan de Standaardloadbalancer voor een betere koppeling met andere bestaande resources.
   * **newLBName: [Tekenreeks]: vereist** – Dit is de naam voor de standaardloadbalans die moet worden gemaakt.
1. Voer het script uit met de juiste parameters. Het kan vijf tot zeven minuten duren om te voltooien.

    **Voorbeeld**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>VM's toevoegen aan backendpools van Standard Load Balancer

Controleer eerst of het script een nieuwe Standaard Public Load Balancer heeft gemaakt met de exacte configuratie die is gemigreerd van uw Basic Public Load Balancer. U dit verifiëren via de Azure-portal.

Zorg ervoor dat u een kleine hoeveelheid verkeer door de Standaard Load Balancer als een handmatige test.
  
Hier volgen een paar scenario's van hoe u VM's toevoegt aan backendpools van de nieuw gemaakte Standaard Public Load Balancer en kunnen onze aanbevelingen voor elk van deze scenario's worden geconfigureerd:

* **Bestaande VM's verplaatsen van backendpools van oude Basic Public Load Balancer naar backendpools van nieuw gecreëerde Standaard Public Load Balancer**.
    1. Als u de taken in deze quickstart wilt uitvoeren, moet u zich aanmelden bij de [Azure-portal](https://portal.azure.com).
 
    1. Selecteer **Alle bronnen** in het linkermenu en selecteer vervolgens de nieuw gemaakte **standaardbalansinbalancer** in de resourcelijst.
   
    1. Selecteer **Backend-groepen**onder **Instellingen**.
   
    1. Selecteer de backendpool die overeenkomt met de backendpool van de Basisbalans, selecteer de volgende waarde: 
      - **Virtuele machine:** De vm's indelijst en selecteer de vm's uit de bijpassende backendpool van de Basic Load Balancer.
    1. Selecteer **Opslaan**.
    >[!NOTE]
    >Voor VM's met openbare IP-adressen moet u eerst standaard IP-adressen maken waar hetzelfde IP-adres niet is gegarandeerd. Koppel VM's van Basis-IP's en koppel ze aan de nieuw gemaakte standaard IP-adressen. Vervolgens u instructies volgen om VM's toe te voegen aan de backendpool van Standard Load Balancer. 

* **Nieuwe VM's maken om toe te voegen aan de backendpools van de nieuw gecreëerde Standaard Public Load Balancer**.
    * Meer instructies over het maken van VM en associëren met Standard Load Balancer vindt u [hier.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Een uitgaande regel maken voor uitgaande verbinding

Volg de [instructies](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) om een uitgaande regel te maken, zodat u
* Definieer uitgaande NAT vanaf nul.
* Schaal en stem het gedrag van bestaande uitgaande NAT af.

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Zijn er beperkingen met het Azure PowerShell-script om de configuratie van v1 naar v2 te migreren?

Ja. Zie [kanttekeningen/beperkingen](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Schakelt het Azure PowerShell-script ook het verkeer over van mijn Basic Load Balancer naar de nieuw gemaakte Standard Load Balancer?

Nee. Het Azure PowerShell-script migreert alleen de configuratie. Daadwerkelijke verkeersmigratie is uw verantwoordelijkheid en in uw controle.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Ik liep in een aantal problemen met het gebruik van dit script. Hoe kan ik hulp krijgen?
  
U een slbupgradesupport@microsoft.come-mail verzenden naar, een ondersteuningsaanvraag openen met Azure Support of beide doen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Standard Load Balancer](load-balancer-overview.md)
