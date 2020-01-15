---
title: Red Hat Update Infrastructure | Microsoft Docs
description: Meer informatie over Red Hat Update Infrastructure voor on-demand Red Hat Enterprise Linux-instanties in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 1a63b388725823695c41339ae173c8d8e34839ef
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941393"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure voor on-demand Red Hat Enterprise Linux-machines in Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) kunt u cloudproviders, zoals Azure, voor het spiegelen van de inhoud van Red Hat gehoste opslagplaats, aangepaste opslagplaatsen maken met Azure-specifieke inhoud en het beschikbaar maken voor virtuele machines door eindgebruikers.

Red Hat Enterprise Linux (RHEL) betalen naar gebruik (betalen per gebruik) afbeeldingen afkomstig zijn vooraf geconfigureerd voor toegang tot Azure RHUI. Er is geen aanvullende configuratie nodig. Uitvoeren als u de meest recente updates, `sudo yum update` nadat uw RHEL-exemplaar gereed is. Deze service is opgenomen als onderdeel van de kosten voor de RHEL PAYG-software.

Meer informatie over RHEL-installatie kopieën in azure, met inbegrip van publicatie-en bewaar beleid, is [hier](./redhat-images.md)beschikbaar.

Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

## <a name="important-information-about-azure-rhui"></a>Belangrijke informatie over Azure RHUI

* Azure RHUI is de update-infra structuur die ondersteuning biedt voor alle RHEL PAYG-Vm's die zijn gemaakt in Azure. Dit verhindert u niet dat u uw PAYG RHEL-Vm's kunt registreren met abonnements Manager of satelliet of andere bron van updates, maar wel met een PAYG-VM, resulteert dit in indirect dubbel facturering. Zie het volgende punt voor meer informatie.
* Toegang tot de RHUI wordt gehost op Azure is opgenomen in de prijs van de installatiekopie RHEL PAYG. Als u een betalen per gebruik-RHEL VM uit de RHUI wordt gehost op Azure registratie is dat de virtuele machine niet converteren naar een type bring-your-own-license (BYOL) van virtuele machine. Als u dezelfde virtuele machine met een andere bron van de updates hebt geregistreerd, kunnen kosten voor _indirecte_ dubbele kosten in rekening gebracht. U betaalt het eerst voor de kosten van Azure RHEL-software. De tweede keer voor Red Hat-abonnementen die eerder zijn gekocht, moet u betalen. Als u een andere update-infra structuur dan een door Azure gehoste RHUI wilt gebruiken, kunt u overwegen om te registreren voor het gebruik van de [RHEL BYOS-installatie kopieën](./byos.md).

* RHEL SAP PAYG-installatie kopieën in azure (RHEL for SAP, RHEL for SAP HANA en RHEL for SAP Business Applications) zijn verbonden met toegewezen RHUI-kanalen die blijven gelden voor de specifieke RHEL-versie zoals vereist voor SAP-certificering.

* Toegang tot Azure gehoste RHUI is beperkt tot de virtuele machines binnen de [Azure datacenter IP-adresbereiken](https://www.microsoft.com/download/details.aspx?id=41653). Als u via een proxy alle VM-verkeer via een on-premises netwerkinfrastructuur, moet u mogelijk voor het instellen van de gebruiker gedefinieerde routes voor de RHEL-betalen per gebruik virtuele machines voor toegang tot de Azure-RHUI. Als dat het geval is, moeten door de gebruiker gedefinieerde routes worden toegevoegd voor _alle_ RHUI IP-adressen.

## <a name="image-update-behavior"></a>Gedrag van installatie kopie-updates

Met ingang van 2019 april biedt Azure RHEL installatie kopieën die zijn verbonden met EUS-opslag plaatsen (Extended update support) standaard en RHEL installatie kopieën die standaard worden aangesloten op de normale (niet-EUS) opslag plaatsen. Meer informatie over RHEL EUS vindt u in de documentatie van de [versie levenscyclus](https://access.redhat.com/support/policy/updates/errata) van Red Hat en de [Eus-documentatie](https://access.redhat.com/articles/rhel-eus). Het standaard gedrag van `sudo yum update` varieert, afhankelijk van de RHEL-installatie kopie die u hebt ingericht, omdat verschillende installatie kopieën zijn verbonden met verschillende opslag plaatsen.

Voor een volledige lijst met installatie kopieën voert u `az vm image list --publisher redhat --all` uit met behulp van de Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Afbeeldingen die zijn verbonden met niet-EUS-opslag plaatsen

Als u een virtuele machine inricht vanuit een RHEL-installatie kopie die is verbonden met niet-EUS-opslag plaatsen, wordt u bijgewerkt naar de meest recente RHEL-versie wanneer u `sudo yum update`uitvoert. Als u bijvoorbeeld een virtuele machine inricht vanuit een RHEL 7,4 PAYG-installatie kopie en `sudo yum update`uitvoert, hebt u een RHEL 7,7 VM (de meest recente secundaire versie in de RHEL7-serie).

Installatie kopieën die zijn verbonden met niet-EUS-opslag plaatsen, bevatten geen secundair versie nummer in de SKU. De SKU is het derde element in de URN (volledige naam van de afbeelding). Zo worden alle volgende installatie kopieën gekoppeld aan niet-EUS-opslag plaatsen:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Houd er rekening mee dat de Sku's 7-LVM of 7-RAW zijn. De secundaire versie wordt aangegeven in de versie (vierde element in de URN) van deze installatie kopieën.

### <a name="images-connected-to-eus-repositories"></a>Installatie kopieën die zijn verbonden met EUS-opslag plaatsen

Als u een virtuele machine inricht vanuit een RHEL-installatie kopie die is verbonden met EUS-opslag plaatsen, wordt u niet bijgewerkt naar de meest recente secundaire versie van RHEL wanneer u `sudo yum update`uitvoert. De installatie kopieën die zijn verbonden met EUS-opslag plaatsen, zijn ook versie-vergrendeld op hun specifieke secundaire versie.

Afbeeldingen die zijn verbonden met EUS-opslag plaatsen, bevatten een secundair versie nummer in de SKU. Zo worden alle volgende installatie kopieën gekoppeld aan EUS-opslag plaatsen:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS en versie-vergren delen RHEL Vm's

EUS-opslag plaatsen (Extended update support) zijn beschikbaar voor klanten die hun RHEL-Vm's mogelijk willen vergren delen met een bepaalde RHEL-kleine release na het inrichten van de virtuele machine. U kunt uw RHEL-VM met een specifieke secundaire versie vergren delen door de opslag plaatsen bij te werken zodat deze verwijzen naar de ondersteunings opslagplaatsen voor uitgebreide updates. U kunt ook de vergrendelings bewerking van de EUS-versie ongedaan maken.

>[!NOTE]
> EUS wordt niet ondersteund voor RHEL-Extra's. Dit betekent dat als u een pakket installeert dat doorgaans beschikbaar is via het RHEL Extrass-kanaal, u dit niet kunt doen wanneer u op EUS. De product levenscyclus van Red Hat extras wordt [hier](https://access.redhat.com/support/policy/updates/extras/)beschreven.

Op het moment van deze schrijf bewerking is de EUS-ondersteuning voor RHEL < = 7,4 beëindigd. Zie de sectie ' Red Hat Enterprise Linux meer ondersteuning voor invoeg toepassingen ' in de [Red Hat-documentatie](https://access.redhat.com/support/policy/updates/errata/) voor meer informatie.
* RHEL 7,4 EUS-ondersteuning eindigt op 31 augustus 2019
* RHEL 7,5 EUS support eindigt op 30 april 2020
* RHEL 7,6 EUS-ondersteuning eindigt op 31 oktober 2020
* RHEL 7,7 EUS-ondersteuning eindigt op 30 augustus 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Een RHEL-VM overschakelen naar EUS (versie-vergren delen naar een specifieke secundaire versie)
Gebruik de volgende instructies om een RHEL-VM te vergren delen naar een bepaalde kleine release (uitvoeren als root):

>[!NOTE]
> Dit geldt alleen voor RHEL-versies waarvoor EUS beschikbaar is. Op het moment van deze schrijf bewerking omvat dit RHEL 7.2-7,7. Meer informatie vindt u op de pagina [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata) .

1. Niet-EUS-opslag plaatsen uitschakelen:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS opslag plaatsen toevoegen:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. De variabele releasever vergren delen (uitvoeren als root):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Met de bovenstaande instructie wordt de RHEL-secundaire release vergrendeld op de huidige secundaire release. Voer een specifieke kleine release in als u een upgrade wilt uitvoeren en wilt vergren delen naar een latere secundaire versie die niet het meest recent is. `echo 7.5 > /etc/yum/vars/releasever` wordt uw RHEL-versie bijvoorbeeld vergren delen met RHEL 7,5

1. Uw RHEL-VM bijwerken
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Overschakelen van een RHEL-VM naar een niet-EUS (een versie vergrendeling verwijderen)
Voer het volgende uit als root:
1. Verwijder het releasever-bestand:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS opslag plaatsen uitschakelen:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL VM configureren
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Uw RHEL-VM bijwerken
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>De IP-adressen voor de levering van inhoud RHUI servers

RHUI is beschikbaar in alle regio's waar de RHEL-installatiekopieën voor op aanvraag beschikbaar zijn. Het bevat momenteel alle openbare regio's die worden vermeld op de [Azure-statusdashboard](https://azure.microsoft.com/status/) pagina-, Azure US Government- en Microsoft Azure Duitsland-regio's.

Als u verder beperken van toegang van RHEL betalen per gebruik virtuele machines in een configuratie van het netwerk, zorg ervoor dat de volgende IP-adressen zijn toegestaan voor `yum update` om te werken, afhankelijk van de omgeving waarin u werkt in:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI-infra structuur


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Verlopen RHUI-clientcertificaat op een virtuele machine bijwerken

Als u een oudere RHEL-VM-installatie kopie gebruikt, bijvoorbeeld RHEL 7,4 (afbeelding URN: `RedHat:RHEL:7.4:7.4.2018010506`), worden er connectiviteits problemen met RHUI veroorzaakt door een nu-verlopen SSL-client certificaat. De fout die u ziet, kan er als volgt uitzien: _"SSL-peer heeft uw certificaat afgewezen als verlopen"_ of _"fout: kan de meta gegevens van de opslag plaats (repomd. XML) niet ophalen voor de opslag plaats... Controleer het pad en probeer het opnieuw_. Als u dit probleem wilt verhelpen, moet u het RHUI-client pakket op de VM bijwerken met de volgende opdracht:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Het uitvoeren van `sudo yum update` kan ook het client certificaat pakket bijwerken (afhankelijk van uw RHEL-versie), ondanks dat er fouten met de fout ' verlopen SSL-certificaat ' worden weer geven voor andere opslag plaatsen. Als deze update is geslaagd, moet de normale connectiviteit met andere RHUI-opslag plaatsen worden hersteld. u kunt `sudo yum update` nu uitvoeren.

Als u een 404-fout uitvoert tijdens het uitvoeren van een `yum update`, kunt u het volgende proberen om uw yum-cache te vernieuwen:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Problemen met verbinding naar Azure RHUI
Als u problemen hebt met het verbinding maken met Azure RHUI vanuit uw virtuele machine van Azure RHEL betalen per gebruik, volg deze stappen:

1. Inspecteer de configuratie van de virtuele machine voor het Azure-RHUI-eindpunt:

    1. Controleer of de `/etc/yum.repos.d/rh-cloud.repo` bestand bevat een verwijzing naar `rhui-[1-3].microsoft.com` in de `baseurl` van de `[rhui-microsoft-azure-rhel*]` sectie van het bestand. Als dit het geval is, gebruikt u de nieuwe Azure-RHUI.

    1. Als deze naar een locatie met het volgende patroon verwijst `mirrorlist.*cds[1-4].cloudapp.net`, een configuratie-update is vereist. U de oude VM-momentopname en moet u deze om te verwijzen naar de nieuwe Azure-RHUI bij te werken.

1. Toegang tot Azure gehoste RHUI is beperkt tot virtuele machines binnen de [Azure datacenter IP-adresbereiken](https://www.microsoft.com/download/details.aspx?id=41653).

1. Als u de nieuwe configuratie, hebt gecontroleerd dat de virtuele machine verbinding vanuit de Azure-IP-adresbereik maakt en nog steeds geen verbinding met Azure RHUI, bestand een ondersteuningscase met Microsoft of Red Hat maken.

### <a name="infrastructure-update"></a>Infrastructuur update

In September 2016 gaan we een bijgewerkte Azure RHUI geïmplementeerd. In April 2017 wordt de oude Azure RHUI afgesloten. Als u eerder hebt gebruikt de PAYG RHEL-installatiekopieën (of hun momentopnamen) vanaf September 2016 of hoger, bent u automatisch verbinding met de nieuwe Azure-RHUI. Als u oudere momentopnamen echter op uw virtuele machines hebt, moet u handmatig bijwerken hun configuratie voor toegang tot de Azure-RHUI zoals beschreven in een volgende sectie.

De nieuwe Azure RHUI-servers worden geïmplementeerd met [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). In Traffic Manager, één eindpunt (rhui-1.microsoft.com) kan worden gebruikt door een virtuele machine, ongeacht de regio.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Handmatige updateprocedure voor het gebruik van de Azure-RHUI-servers
Deze procedure is alleen ter informatie bedoeld. Er is al de juiste configuratie verbinding maken met Azure RHUI PAYG RHEL-installatiekopieën. Voor het handmatig bijwerken van de configuratie voor het gebruik van de Azure-RHUI-servers, voert u de volgende stappen uit:

- Voor RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Voor RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>Volgende stappen
* Een Red Hat Enterprise Linux-VM maken vanaf een betalen per gebruik van Azure Marketplace-installatiekopie en RHUI wordt gehost op Azure gebruiken, gaat u naar de [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Ga naar de [documentatie pagina](./redhat-images.md)voor meer informatie over de Red Hat-afbeeldingen in Azure.
* Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
