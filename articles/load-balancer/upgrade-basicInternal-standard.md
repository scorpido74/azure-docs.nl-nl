---
title: Upgrade van Basic intern naar Standard intern-Azure Load Balancer
description: Dit artikel laat u zien hoe u de interne Load Balancer van Azure kunt upgraden van de Basic-SKU naar de standaard-SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770391"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Upgrade uitvoeren voor interne Load Balancer van Azure: er is geen uitgaande verbinding vereist
[Azure Standard Load Balancer](load-balancer-overview.md) biedt een uitgebreide set functionaliteit en hoge Beschik baarheid via zone redundantie. Zie [vergelijkings tabel](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)voor meer informatie over Load Balancer SKU.

In dit artikel wordt een Power shell-script geïntroduceerd waarmee een Standard Load Balancer met dezelfde configuratie als de basis Load Balancer samen met het migreren van verkeer van basis Load Balancer naar Standard Load Balancer.

## <a name="upgrade-overview"></a>Overzicht van de upgrade

Er is een Azure PowerShell script beschikbaar dat het volgende doet:

* Hiermee maakt u een standaard interne SKU Load Balancer op de locatie die u opgeeft. Houd er rekening mee dat er geen [uitgaande verbinding](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) wordt gegeven door de standaard interne Load Balancer.
* De configuraties van de basis-SKU Load Balancer naadloos gekopieerd naar de zojuist gemaakte Standard Load Balancer.
* Verplaats de privé-IP-adressen van de Basic-Load Balancer naadloos naar de zojuist gemaakte Standard Load Balancer.
* Verplaats de Vm's naadloos van de back-endadresgroep van de basis Load Balancer naar de back-end-groep van de Standard Load Balancer

### <a name="caveatslimitations"></a>Caveats\Limitations

* Script ondersteunt alleen interne Load Balancer upgrade, waarbij geen uitgaande verbinding vereist is. Raadpleeg deze [pagina](upgrade-InternalBasic-To-PublicStandard.md) voor instructies als u een [uitgaande verbinding](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) voor sommige vm's hebt vereist. 
* Als de standaard load balancer in een andere regio wordt gemaakt, kunt u de virtuele machines die in de oude regio bestaan, niet koppelen aan de zojuist gemaakte Standard Load Balancer. Om deze beperking te omzeilen, moet u ervoor zorgen dat u een nieuwe virtuele machine maakt in de nieuwe regio.
* Als uw Load Balancer geen frontend-IP-configuratie of back-end-pool heeft, zult u waarschijnlijk een fout bij het uitvoeren van het script aangaan. Zorg ervoor dat deze niet leeg zijn.

## <a name="download-the-script"></a>Het script downloaden

Down load het migratie script van de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Het script gebruiken

Er zijn twee opties voor u afhankelijk van de instellingen en voor keuren van uw lokale Power shell-omgeving:

* Als u de Azure AZ-modules niet hebt geïnstalleerd of als u niet van mening bent dat u de Azure AZ-modules verwijdert, kunt u `Install-Script` het beste de optie gebruiken om het script uit te voeren.
* Als u de Azure AZ-modules wilt blijven gebruiken, is het verstandig om het script te downloaden en het rechtstreeks uit te voeren.

Als u wilt weten of u de Azure AZ-modules hebt `Get-InstalledModule -Name az`geïnstalleerd, voert u uit. Als er geen geïnstalleerde AZ-modules worden weer geven, kunt u de `Install-Script` -methode gebruiken.

### <a name="install-using-the-install-script-method"></a>Installeren met behulp van de methode install-script

Als u deze optie wilt gebruiken, moet u de Azure AZ-modules niet op uw computer installeren. Als ze zijn geïnstalleerd, wordt een fout weer gegeven in de volgende opdracht. U kunt de Azure AZ-modules verwijderen of de andere optie gebruiken om het script hand matig te downloaden en uit te voeren.
  
Voer het script uit met de volgende opdracht:

`Install-Script -Name AzureILBUpgrade`

Met deze opdracht worden ook de vereiste AZ-modules geïnstalleerd.  

### <a name="install-using-the-script-directly"></a>Rechtstreeks installeren met behulp van het script

Als er sommige Azure AZ-modules zijn geïnstalleerd en deze niet kunnen verwijderen (of niet wilt verwijderen), kunt u het script hand matig downloaden met het tabblad **hand matig downloaden** in de koppeling voor het downloaden van het script. Het script wordt gedownload als een onbewerkt nupkg-bestand. Zie [hand matig downloaden van pakketten](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)als u het script wilt installeren vanuit dit nupkg-bestand.

Het script uitvoeren:

1. Gebruiken `Connect-AzAccount` om verbinding te maken met Azure.

1. Gebruiken `Import-Module Az` voor het importeren van de AZ-modules.

1. Controleer de vereiste para meters:

   * **rgName: [teken reeks]: vereist** – dit is de resource groep voor uw bestaande basis Load Balancer en nieuwe Standard Load Balancer. Als u deze teken reeks waarde wilt vinden, gaat u naar Azure Portal, selecteert u de basis Load Balancer bron en klikt u op het **overzicht** van de Load Balancer. De resource groep bevindt zich op deze pagina.
   * **oldLBName: [teken reeks]: vereist** – dit is de naam van de bestaande Basic-Balancer die u wilt bijwerken. 
   * **newlocation: [string]: vereist** : dit is de locatie waar de Standard Load Balancer wordt gemaakt. Het wordt aanbevolen om dezelfde locatie van de gekozen basis Load Balancer over te nemen naar de Standard Load Balancer voor een betere koppeling met andere bestaande resources.
   * **newLBName: [string]: vereist** : dit is de naam voor de Standard Load Balancer die u wilt maken.
1. Voer het script uit met de juiste para meters. Het kan vijf tot zeven minuten duren voordat de bewerking is voltooid.

    **Voorbeeld**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Zijn er beperkingen met het Azure PowerShell script voor het migreren van de configuratie van v1 naar v2?

Ja. Zie [aanvullende informatie/beperkingen](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Schakelt het Azure PowerShell script ook over op het verkeer van mijn basis Load Balancer naar de zojuist gemaakte Standard Load Balancer?

Ja, het verkeer wordt gemigreerd. Als u het verkeer persoonlijk wilt migreren, gebruikt u [Dit script](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) , waarmee u geen vm's voor u kunt verplaatsen.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Er zijn problemen opgetreden bij het gebruik van dit script. Hoe kan ik hulp krijgen?
  
U kunt een e-mail verzenden naar slbupgradesupport@microsoft.com, een ondersteunings aanvraag openen met ondersteuning voor Azure of beide.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Standard Load Balancer](load-balancer-overview.md)
