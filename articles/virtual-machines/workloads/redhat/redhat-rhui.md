---
title: Red Hat Update Infrastructuur | Microsoft Documenten
description: Meer informatie over Red Hat Update-infrastructuur voor on-demand Red Hat Enterprise Linux-exemplaren in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: aa9fd230f59b5e46576e78beb0436c85449d3c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256909"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update-infrastructuur voor on-demand Red Hat Enterprise Linux VM's in Azure
 [Met De Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) kunnen cloudproviders, zoals Azure, door Red Hat gehoste repository-inhoud spiegelen, aangepaste opslagplaatsen maken met Azure-specifieke inhoud en deze beschikbaar maken voor vm's van eindgebruikers.

Red Hat Enterprise Linux (RHEL) Pay-As-You-Go (PAYG) afbeeldingen zijn vooraf geconfigureerd om toegang te krijgen tot Azure RHUI. Er is geen extra configuratie nodig. Als u de nieuwste `sudo yum update` updates wilt ontvangen, voert u het uitvoeren uit nadat uw RHEL-exemplaar klaar is. Deze service is opgenomen als onderdeel van de RHEL PAYG-softwarekosten.

Aanvullende informatie over RHEL-afbeeldingen in Azure, inclusief publicatie- en bewaarbeleid, is [hier](./redhat-images.md)beschikbaar.

Informatie over het ondersteuningsbeleid van Red Hat voor alle versies van RHEL is te vinden op de Red [Hat Enterprise Linux Life Cycle-pagina.](https://access.redhat.com/support/policy/updates/errata)

> [!IMPORTANT]
> RHUI is alleen bedoeld voor pay-as-you-go (PAYG) afbeeldingen. Voor aangepaste en gouden afbeeldingen, ook wel bring-your-own-subscription (BYOS) genoemd, moet het systeem worden gekoppeld aan RHSM of Satellite om updates te ontvangen. Zie [Red Hat artikel](https://access.redhat.com/solutions/253273) voor meer details.


## <a name="important-information-about-azure-rhui"></a>Belangrijke informatie over Azure RHUI

* Azure RHUI is de update-infrastructuur die alle RHEL PAYG VM's ondersteunt die in Azure zijn gemaakt. Dit sluit u niet uit om uw PAYG RHEL VM's te registreren met Subscription Manager of Satellite of een andere bron van updates, maar dit met een PAYG VM zal resulteren in indirecte dubbele facturering. Zie het volgende punt voor meer informatie.
* Toegang tot de door Azure gehoste RHUI is inbegrepen in de RHEL PAYG-afbeeldingsprijs. Als u een PAYG RHEL VM uitschrijft van de door Azure gehoste RHUI, zet de virtuele machine niet om in een BYOL-type VM (bring-your-own-license). Als u dezelfde vm registreert met een andere bron van updates, u _indirecte_ dubbele kosten in rekening brengen. Er worden de eerste keer kosten in rekening gebracht voor de Azure RHEL-softwarekosten. Er worden de tweede keer kosten in rekening gebracht voor Red Hat-abonnementen die eerder zijn aangeschaft. Als u consequent een andere update-infrastructuur dan RHUI met Azure moet gebruiken, u overwegen u te registreren om de [RHEL BYOS-afbeeldingen](./byos.md)te gebruiken.

* RHEL SAP PAYG-afbeeldingen in Azure (RHEL voor SAP, RHEL voor SAP HANA en RHEL voor SAP Business Applications) zijn verbonden met speciale RHUI-kanalen die op de specifieke RHEL-secundaire versie blijven staan, zoals vereist voor SAP-certificering.

* De toegang tot door Azure gehoste RHUI is beperkt tot de VM's binnen de [IP-bereiken van Azure-datacenters.](https://www.microsoft.com/download/details.aspx?id=41653) Als u al het VM-verkeer proxy's via een on-premises netwerkinfrastructuur, moet u mogelijk door de gebruiker gedefinieerde routes instellen voor de RHEL PAYG VM's om toegang te krijgen tot de Azure RHUI. Als dat het geval is, moeten door de gebruiker gedefinieerde routes worden toegevoegd voor _alle_ RHUI IP-adressen.


## <a name="image-update-behavior"></a>Afbeeldingsupdategedrag

Vanaf april 2019 biedt Azure Standaard RHEL-afbeeldingen die zijn verbonden met EUS-repositories (Extended Update Support) en RHEL-afbeeldingen die standaard zijn verbonden met de reguliere (niet-EUS)-repositories. Meer informatie over RHEL EUS is beschikbaar in de [levenscyclusdocumentatie](https://access.redhat.com/support/policy/updates/errata) van Red Hat en [EUS-documentatie.](https://access.redhat.com/articles/rhel-eus) Het standaardgedrag `sudo yum update` van zal variëren afhankelijk van welke RHEL-afbeelding u hebt ingericht, omdat verschillende afbeeldingen zijn verbonden met verschillende opslagplaatsen.

Voer voor een volledige `az vm image list --publisher redhat --all` afbeeldingslijst uit met Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Afbeeldingen die zijn verbonden met niet-EUS-repositories

Als u een VM indient op basis van een RHEL-afbeelding die is verbonden met niet-EUS-repositories, wordt u geüpgraded naar de nieuwste RHEL-secundaire versie wanneer u deze uitvoert. `sudo yum update` Als u bijvoorbeeld een VM indient op basis van een `sudo yum update`RHEL 7.4 PAYG-afbeelding en deze uitvoert, komt u terecht bij een RHEL 7.7 VM (de nieuwste secundaire versie in de RHEL7-familie).

Afbeeldingen die zijn verbonden met niet-EUS-repositories, bevatten geen klein versienummer in de SKU. De SKU is het derde element in de URN (volledige naam van de afbeelding). Alle volgende afbeeldingen zijn bijvoorbeeld gekoppeld aan niet-EUS-repositories:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Houd er rekening mee dat de SKU's 7-LVM of 7-RAW zijn. De kleine versie is aangegeven in de versie (vierde element in de URN) van deze afbeeldingen.

### <a name="images-connected-to-eus-repositories"></a>Afbeeldingen die zijn verbonden met EUS-repositories

Als u een VM indient op basis van een RHEL-afbeelding die is verbonden met EUS-repositories, wordt u niet geüpgraded naar de nieuwste RHEL-secundaire versie wanneer u deze uitvoert. `sudo yum update` Dit komt omdat de afbeeldingen die zijn aangesloten op EUS-repositories ook zijn vergrendeld op hun specifieke secundaire versie.

Afbeeldingen die zijn verbonden met EUS-repositories bevatten een klein versienummer in de SKU. Zo zijn alle volgende afbeeldingen gekoppeld aan EUS-repositories:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS en versievergrendelende RHEL VM's

Extended Update Support (EUS) repositories zijn beschikbaar voor klanten die hun RHEL VM's willen vergrendelen naar een bepaalde RHEL-release na het inrichten van de VM. U uw RHEL VM vergrendelen naar een specifieke secundaire versie door de repositories bij te werken om naar de Extended Update Support-repositories te wijzen. U ook de eus-versievergrendeling ongedaan maken.

>[!NOTE]
> EUS wordt niet ondersteund op RHEL Extra's. Dit betekent dat als u een pakket installeert dat meestal beschikbaar is via het RHEL Extras-kanaal, u dit niet doen terwijl u op EUS bent. De Red Hat Extras Product Life Cycle is [hier](https://access.redhat.com/support/policy/updates/extras/)gedetailleerd.

Op het moment van schrijven is de EUS-steun voor RHEL <= 7,4 beëindigd. Zie de sectie "Red Hat Enterprise Linux Longer Support Add-Ons" in de [Red Hat-documentatie](https://access.redhat.com/support/policy/updates/errata/) voor meer informatie.
* RHEL 7.4 EUS-ondersteuning eindigt op 31 augustus 2019
* RHEL 7.5 EUS-steun eindigt op 30 april 2020
* RHEL 7.6 EUS-steun eindigt op 31 oktober 2020
* RHEL 7.7 EUS-steun eindigt op 30 augustus 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Een RHEL VM overschakelen naar EUS (versievergrendeling naar een specifieke secundaire versie)
Gebruik de volgende instructies om een RHEL VM te vergrendelen naar een bepaalde kleine release (voer uit als root):

>[!NOTE]
> Dit geldt alleen voor RHEL-versies waarvoor EUS beschikbaar is. Op het moment van schrijven, dit omvat RHEL 7.2-7.7. Meer details zijn beschikbaar op de [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata) pagina.

1. Niet-EUS-repo's uitschakelen:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Voeg EUS repos toe:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Vergrendel `releasever` de variabele (als hoofd):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > De bovenstaande instructie zal vergrendelen de RHEL kleine release naar de huidige minor release. Voer een specifieke kleine release in als u wilt upgraden en vergrendelen naar een latere kleine release die niet de laatste is. Vergrendelbijvoorbeeld `echo 7.5 > /etc/yum/vars/releasever` uw RHEL-versie naar RHEL 7.5

1. Uw RHEL VM bijwerken
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Een RHEL VM terugschakelen naar niet-EUS (verwijder een versievergrendeling)
Voer het volgende uit als hoofd:
1. Het `releasever` bestand verwijderen:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Eus-repo's uitschakelen:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL VM configureren
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Uw RHEL VM bijwerken
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>De IP's voor de RHUI content delivery servers

RHUI is beschikbaar in alle regio's waar RHEL on-demand afbeeldingen beschikbaar zijn. Het bevat momenteel alle openbare regio's die worden vermeld op de [azure-statusdashboardpagina,](https://azure.microsoft.com/status/) azure us-overheid en Microsoft Azure Germany-regio's.

Als u een netwerkconfiguratie gebruikt om de toegang van RHEL PAYG VM's verder `yum update` te beperken, controleert u of de volgende IP's kunnen werken, afhankelijk van de omgeving waarin u zich bevindt:


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

## <a name="azure-rhui-infrastructure"></a>Azure RHUI-infrastructuur


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Verlopen RHUI-clientcertificaat bijwerken op een vm

Als u bijvoorbeeld een oudere RHEL VM-afbeelding gebruikt, bijvoorbeeld RHEL 7.4 (afbeelding URN: `RedHat:RHEL:7.4:7.4.2018010506`), zult u verbindingsproblemen ondervinden voor RHUI als gevolg van een nu verlopen TLS/SSL-clientcertificaat. De fout die u ziet, kan eruit zien als _"SSL-peer heeft uw certificaat afgewezen als verlopen"_ of _"Fout: kan metagegevens van opslagplaatsen (repomd.xml) niet ophalen voor opslagplaats: ... Controleer het pad en probeer het opnieuw"_. Update het RHUI-clientpakket op de VM met de volgende opdracht om dit probleem op te lossen:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Als alternatief `sudo yum update` kan het uitvoeren van het clientcertificaatpakket (afhankelijk van uw RHEL-versie) ook worden bijgewerkt, ondanks fouten met het 'verlopen SSL-certificaat' dat u voor andere opslagplaatsen ziet. Als deze update succesvol is, moet de normale verbinding met andere RHUI-repositories worden hersteld, zodat u met succes uitvoeren. `sudo yum update`

Als u tijdens het uitvoeren van `yum update`een fout van 404 een fout van 404 tegenkomt, probeert u het volgende om de cache van uw yum te vernieuwen:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Verbindingsproblemen met Azure RHUI oplossen
Als u problemen ondervindt bij het maken van verbinding met Azure RHUI vanuit uw Azure RHEL PAYG VM, voert u de volgende stappen uit:

1. Controleer de VM-configuratie voor het Azure RHUI-eindpunt:

    1. Controleer of `/etc/yum.repos.d/rh-cloud.repo` het bestand `rhui-[1-3].microsoft.com` een `baseurl` verwijzing `[rhui-microsoft-azure-rhel*]` bevat naar in de sectie van het bestand. Als dit het wel het zo is, gebruikt u de nieuwe Azure RHUI.

    1. Als deze verwijst naar een locatie `mirrorlist.*cds[1-4].cloudapp.net`met het volgende patroon, is een configuratie-update vereist. U gebruikt de oude VM-momentopname en u moet deze bijwerken om naar de nieuwe Azure RHUI te wijzen.

1. Toegang tot door Azure gehoste RHUI is beperkt tot VM's binnen de [IP-bereiken van Azure-datacenters.](https://www.microsoft.com/download/details.aspx?id=41653)

1. Als u de nieuwe configuratie gebruikt, moet u controleren of de VM verbinding maakt vanuit het Azure IP-bereik en nog steeds geen verbinding kan maken met Azure RHUI, een ondersteuningsaanvraag indienen bij Microsoft of Red Hat.

### <a name="infrastructure-update"></a>Infrastructuurupdate

In september 2016 hebben we een bijgewerkte Azure RHUI geïmplementeerd. In april 2017 hebben we de oude Azure RHUI gesloten. Als u de RHEL PAYG-afbeeldingen (of hun momentopnamen) vanaf september 2016 of later hebt gebruikt, maakt u automatisch verbinding met de nieuwe Azure RHUI. Als u echter oudere momentopnamen op uw VM's hebt, moet u hun configuratie handmatig bijwerken om toegang te krijgen tot de Azure RHUI zoals beschreven in een volgende sectie.

De nieuwe Azure RHUI-servers worden geïmplementeerd met [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). In Traffic Manager kan één eindpunt (rhui-1.microsoft.com) door elke virtuele machine worden gebruikt, ongeacht de regio.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Handmatige updateprocedure voor het gebruik van de Azure RHUI-servers
Deze procedure is alleen bedoeld als referentie. RHEL PAYG-afbeeldingen hebben al de juiste configuratie om verbinding te maken met Azure RHUI. Voer de volgende stappen uit om de configuratie handmatig bij te werken om de Azure RHUI-servers te gebruiken:

- Voor RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Voor RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Voor RHEL 8:
    1. Een config-bestand maken:
        ```bash
        vi rhel8.config
        ```
    1. Voeg de volgende inhoud toe aan het config-bestand:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Sla het bestand op en voer de volgende opdracht uit:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Uw VM bijwerken
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Volgende stappen
* Als u een Red Hat Enterprise Linux VM wilt maken op basis van een Azure Marketplace PAYG-afbeelding en een Door Azure gehoste RHUI wilt gebruiken, gaat u naar de [Azure Marketplace.](https://azure.microsoft.com/marketplace/partners/redhat/)
* Ga voor meer informatie over de Red Hat-afbeeldingen in Azure naar de [documentatiepagina](./redhat-images.md).
* Informatie over het ondersteuningsbeleid van Red Hat voor alle versies van RHEL is te vinden op de Red [Hat Enterprise Linux Life Cycle-pagina.](https://access.redhat.com/support/policy/updates/errata)
