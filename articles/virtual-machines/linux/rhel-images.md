---
title: Installatie kopieën Red Hat Enterprise Linux in azure | Microsoft Docs
description: Meer informatie over Red Hat Enterprise Linux installatie kopieën in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 8/14/2019
ms.author: borisb
ms.openlocfilehash: accc59ee31ddb7371774da3235cc9ccff24ad641
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309230"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Installatie kopieën Red Hat Enterprise Linux in azure
In dit artikel worden beschik bare installatie kopieën van Red Hat Enterprise Linux (RHEL) in de Azure Marketplace beschreven, samen met beleids regels rondom hun naam en bewaar periode.

Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

>[!Important]
> RHEL-installatie kopieën die momenteel beschikbaar zijn in azure Marketplace ondersteunen de licentie modellen van uw eigen abonnement (BYOS) of betalen per gebruik (PAYG). Het [Azure Hybrid use Benefit](../windows/hybrid-use-benefit-licensing.md) en de dynamische switch tussen BYOS en payg worden niet ondersteund. Voor het overschakelen van de licentie modus moet de virtuele machine opnieuw worden geïmplementeerd vanaf de bijbehorende installatie kopie.

>[!Note]
> Voor een probleem met betrekking tot RHEL-installatie kopieën in de Azure Marketplace-galerie kunt u een ondersteunings ticket bij micro soft indienen.

## <a name="images-available-in-the-ui"></a>Beschik bare installatie kopieën in de gebruikers interface
Wanneer u op de Marketplace zoekt naar "Red Hat" of als u een resource in Azure Portal gebruikers interface maakt, ziet u een subset van beschik bare RHEL-installatie kopieën en gerelateerde Red Hat-producten. U kunt altijd de volledige set beschik bare VM-installatie kopieën verkrijgen met behulp van de Azure CLI/Power shell/API.

Voer de volgende opdracht uit om de volledige set beschik bare Red Hat-afbeeldingen in azure weer te geven

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Naamgevings Conventie
VM-installatie kopieën in azure zijn ingedeeld op uitgever, aanbieding, SKU en versie. De combi natie van Publisher: aanbieding: SKU: version is de afbeelding URN en identificeert de afbeelding die moet worden gebruikt.

`RedHat:RHEL:7-RAW:7.6.2018103108` Verwijst bijvoorbeeld naar een RHEL 7,6 RAW-gepartitioneerde afbeelding die is gebouwd op 31 oktober 2018.

Hieronder ziet u een voor beeld van het maken van een RHEL 7,6 VM.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>De ' meest recente ' moniker
Azure REST API staat het gebruik van moniker "nieuwste" toe voor versie in plaats van de specifieke versie. Als u ' nieuwste ' gebruikt, wordt de meest recente beschik bare installatie kopie ingericht voor de gegeven Uitgever, aanbieding en SKU.

`RedHat:RHEL:7-RAW:latest` Verwijst bijvoorbeeld naar de meest recente onbewerkte afbeelding van de RHEL 7-familie.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> In het algemeen is het vergelijken van versies om het meest recent te bepalen, de regels van de [methode CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Huidige naamgevings Conventie
Alle momenteel gepubliceerde RHEL-installatie kopieën maken gebruik van het betalen per gebruik-model en zijn verbonden met de [Red Hat Update infrastructure (RHUI) in azure](https://aka.ms/rhui-update). Er is een nieuwe naamgevings Conventie aangenomen voor installatie kopieën van de RHEL-7-serie waarin het schema voor schijf partitionering (RAW, LVM) is opgegeven in de SKU in plaats van de versie. De versie van de RHEL-installatie kopie bevat 7-RAW of 7-LVM. De naam van de RHEL 6-familie is op dit moment niet gewijzigd.

Er zijn twee soorten RHEL 7 Image Sku's in deze naamgevings Conventie: Sku's met een lijst met de secundaire versie en Sku's die dat niet zijn. Als u een 7-RAW-of 7-LVM-SKU wilt gebruiken, kunt u de secundaire versie van RHEL opgeven die u in de versie wilt implementeren. Als u de nieuwste versie kiest, wordt de meest recente kleine release van RHEL ingericht.

>[!NOTE]
> In de RHEL voor SAP-set installatie kopieën blijft de versie van RHEL vast. Als zodanig bevat hun naam Conventie een bepaalde versie in de SKU.

>[!NOTE]
> RHEL 6 set met installatie kopieën is niet verplaatst naar de nieuwe naam Conventie.

## <a name="extended-update-support-eus"></a>Ondersteuning voor uitgebreide updates (EUS)
Sinds 2019 april zijn er RHEL-installatie kopieën beschikbaar die standaard zijn gekoppeld aan de EUS-opslag plaatsen (Extended update support). Meer informatie over RHEL EUS vindt u in [de documentatie van Red Hat](https://access.redhat.com/articles/rhel-eus).

Instructies voor het scha kelen tussen uw virtuele machine en EUS en meer informatie over de ondersteuning van de eind datums van EUS zijn [hier](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)beschikbaar.

>[!NOTE]
> EUS wordt niet ondersteund voor RHEL-Extra's. Dit betekent dat als u een pakket installeert dat doorgaans beschikbaar is via het RHEL Extrass-kanaal, u dit niet kunt doen wanneer u op EUS. De product levenscyclus van Red Hat extras wordt [hier](https://access.redhat.com/support/policy/updates/extras/)beschreven.

### <a name="for-customers-that-want-to-use-eus-images"></a>Voor klanten die EUS-installatie kopieën willen gebruiken:
Klanten die installatie kopieën willen gebruiken die zijn gekoppeld aan EUS-opslag plaatsen, moeten de RHEL-installatie kopie gebruiken die een RHEL-secundair versie nummer in de SKU bevat. Deze installatie kopieën worden gepartitioneerd (d.w.z. niet LVM).

Zo ziet u mogelijk de volgende 2 RHEL 7,4-installatie kopieën beschikbaar:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
In dit geval `RedHat:RHEL:7.4:7.4.2019041718` wordt standaard gekoppeld aan Eus-opslag plaatsen en `RedHat:RHEL:7-RAW:7.4.2018010506` wordt standaard aan niet-Eus-opslag plaatsen gekoppeld.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Voor klanten die geen EUS-installatie kopieën willen gebruiken:
Als u een installatie kopie die is verbonden met EUS standaard niet wilt gebruiken, implementeert u een installatie kopie die geen secundair versie nummer in de SKU bevat.

#### <a name="rhel-images-with-eus"></a>RHEL-installatie kopieën met EUS
De volgende tabel is van toepassing op RHEL-installatie kopieën die een secundaire versie in de SKU bevatten.

>[!NOTE]
> Op het moment van schrijven hebben alleen RHEL 7,4 en latere secundaire versies EUS-ondersteuning. EUS wordt niet meer ondersteund voor RHEL < = 7,3.
>
> Meer informatie over de beschik baarheid van RHEL EUS vindt u [hier](https://access.redhat.com/support/policy/updates/errata).

Secundaire versie |Voor beeld van een EUS-afbeelding              |EUS-status                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | Afbeeldingen die zijn gepubliceerd 2019 april en hoger, worden standaard EUS|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Afbeeldingen die zijn gepubliceerd 2019 juni en hoger, worden standaard EUS |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Afbeeldingen die zijn gepubliceerd, zijn mogelijk 2019 en hoger EUS standaard  |
RHEL 8,0      |N/A                            | Geen EUS beschikbaar voor Red Hat                               |


## <a name="list-of-rhel-images-available"></a>Lijst met beschik bare RHEL-installatie kopieën
De volgende aanbiedingen zijn Sku's die momenteel beschikbaar zijn voor algemeen gebruik:

Aanbieding| SKU | Partitionering | Inrichten | Opmerkingen
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Linux-agent | RHEL 7-familie van installatie kopieën. <br> Niet standaard gekoppeld aan EUS-opslag plaatsen.
|             | 7-LVM    | LVM    | Linux-agent | RHEL 7-familie van installatie kopieën. <br> Niet standaard gekoppeld aan EUS-opslag plaatsen.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7-familie van installatie kopieën. <br> Niet standaard gekoppeld aan EUS-opslag plaatsen.
|             | 6.7      | RAW    | Linux-agent | RHEL 6,7-installatie kopieën, oude naamgevings Conventie
|             | 6.8      | RAW    | Linux-agent | Hetzelfde als hierboven voor RHEL 6,8
|             | 6.9      | RAW    | Linux-agent | Hetzelfde als hierboven voor RHEL 6,9
|             | 6.10     | RAW    | Linux-agent | Hetzelfde als hierboven voor RHEL 6,10
|             | 7.2      | RAW    | Linux-agent | Hetzelfde als hierboven voor RHEL 7,2
|             | 7.3      | RAW    | Linux-agent | Hetzelfde als hierboven voor RHEL 7,3
|             | 7.4      | RAW    | Linux-agent | Hetzelfde als hierboven voor RHEL 7,4. <br> Is vanaf april 2019 standaard gekoppeld aan EUS-opslag plaatsen
|             | 7.5      | RAW    | Linux-agent | Hetzelfde als hierboven voor RHEL 7,5. <br> Gekoppeld aan EUS-opslag plaatsen standaard vanaf juni 2019
|             | 7,6      | RAW    | Linux-agent | Hetzelfde als hierboven voor RHEL 7,6. <br> Gekoppeld aan EUS-opslag plaatsen standaard vanaf mei 2019
RHEL-SAP      | 7.4      | LVM    | Linux-agent | RHEL 7,4 voor SAP HANA en zakelijke apps
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 voor SAP HANA en zakelijke apps
RHEL-SAP-HANA | 6.7      | RAW    | Linux-agent | RHEL 6,7 voor SAP HANA
|             | 7.2      | LVM    | Linux-agent | RHEL 7,2 voor SAP HANA
|             | 7.3      | LVM    | Linux-agent | RHEL 7,3 voor SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Linux-agent | RHEL 6,8 voor SAP Business Applications
|             | 7.3      | LVM    | Linux-agent | RHEL 7,3 voor SAP Business Applications
RHEL-HA       | 7.4      | LVM    | Linux-agent | RHEL 7,4 met HA-invoeg toepassing
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 met HA-invoeg toepassing
|             | 7,6      | LVM    | Linux-agent | RHEL 7,6 met HA-invoeg toepassing
RHEL-SAP-HA   | 7.4      | LVM    | Linux-agent | RHEL 7,4 voor SAP met HA-invoeg toepassing
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 voor SAP met HA-invoeg toepassing
|             | 7,6      | LVM    | Linux-agent | RHEL 7,6 voor SAP met HA-invoeg toepassing

### <a name="old-naming-convention"></a>Oude naam Conventie
De RHEL 7-familie van installatie kopieën en de RHEL 6-familie van installatie kopieën gebruikte specifieke versies in hun Sku's tot de hierboven beschreven wijziging van de naamgevings Conventie.

U vindt numerieke Sku's in de volledige lijst met installatie kopieën. Micro soft en Red Hat maken nieuwe numerieke Sku's wanneer een nieuwe kleine versie wordt geleverd.

### <a name="other-available-offers-and-skus"></a>Andere beschik bare aanbiedingen en Sku's
De volledige lijst met beschik bare aanbiedingen en Sku's kan extra installatie kopieën bevatten, behalve wat wordt vermeld in de bovenstaande tabel `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`, bijvoorbeeld. Deze aanbiedingen kunnen worden gebruikt om ondersteuning te bieden voor specifieke Marketplace-oplossingen, of ze kunnen worden gepubliceerd voor voor beelden en test doeleinden. Ze kunnen op elk gewenst moment zonder waarschuwing worden gewijzigd of verwijderd. Gebruik deze niet, tenzij hun aanwezigheid openbaar is gemaakt door micro soft of Red Hat.

## <a name="publishing-policy"></a>Publicatie beleid
Micro soft en Red Hat update-installatie kopieën als nieuwe secundaire versies worden uitgebracht, indien nodig om specifieke CVEs te verhelpen of voor incidentele configuratie wijzigingen/-updates. We streven ernaar bijgewerkte installatie kopieën zo snel mogelijk te leveren, binnen drie werk dagen na een release of Beschik baarheid van een CVE-oplossing.

We werken alleen de huidige secundaire release bij in een opgegeven installatie kopie serie. Als er een nieuwere secundaire versie wordt uitgebracht, wordt de oudere secundaire versie niet meer bijgewerkt. Met de release van RHEL 7,6 worden RHEL 7,5-installatie kopieën bijvoorbeeld niet meer bijgewerkt.

>[!NOTE]
> Actieve Azure-Vm's die zijn ingericht op basis van RHEL betalen per gebruik-installatie kopieën zijn verbonden met de Azure-RHUI en kunnen updates en oplossingen ontvangen zodra deze zijn uitgebracht door Red Hat en worden gerepliceerd naar de Azure-RHUI (doorgaans in minder dan 24 uur na de officiële versie van Red Hat) . Voor deze Vm's is geen nieuwe gepubliceerde installatie kopie vereist voor het ophalen van updates en klanten hebben volledige controle over het moment waarop de update kan worden gestart.

## <a name="image-retention-policy"></a>Beleid voor het bewaren van installatie kopieën
Ons huidige beleid is om alle eerder gepubliceerde installatie kopieën te blijven gebruiken. Wij behouden ons het recht voor om installatie kopieën te verwijderen waarvan bekend is dat ze problemen kunnen veroorzaken. Zo kunnen installatie kopieën met onjuiste configuraties door volgende platform-of onderdeel updates worden verwijderd. Installatie kopieën die kunnen worden verwijderd, volgen het huidige Marketplace-beleid om meldingen tot 30 dagen te geven voordat de installatie kopie wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen
* Lees [hier](https://aka.ms/rhui-update)meer over de Azure Red Hat-update-infra structuur.
* Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
