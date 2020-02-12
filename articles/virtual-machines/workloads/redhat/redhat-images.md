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
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 5ed5d9337dd4e7acdbba25c4cb66d2690793f250
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134393"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Overzicht van Red Hat Enterprise Linux installatie kopieën
In dit artikel worden beschik bare installatie kopieën van Red Hat Enterprise Linux (RHEL) in de Azure Marketplace beschreven, samen met beleids regels rondom hun naam en bewaar periode.

Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) . Voor prijs informatie gaat u naar de [Azure-prijs calculator](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> RHEL-installatie kopieën die momenteel beschikbaar zijn in azure Marketplace ondersteunen de licentie modellen van uw eigen abonnement (BYOS) of betalen per gebruik (PAYG). Het [Azure Hybrid use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) en de dynamische switch tussen BYOS en payg worden niet ondersteund. Voor het overschakelen van de licentie modus moet de virtuele machine opnieuw worden geïmplementeerd vanaf de bijbehorende installatie kopie.

>[!NOTE]
> Voor een probleem met betrekking tot RHEL-installatie kopieën in azure Marketplace kunt u een ondersteunings ticket bij micro soft indienen.

## <a name="viewing-images-available-in-azure"></a>Afbeeldingen weer geven die beschikbaar zijn in azure
Wanneer u op de Marketplace zoekt naar "Red Hat" of als u een resource in Azure Portal gebruikers interface maakt, ziet u slechts een subset van alle beschik bare RHEL-installatie kopieën. U kunt altijd de volledige set beschik bare VM-installatie kopieën verkrijgen met behulp van de Azure CLI/Power shell/API.

Voer de volgende opdracht uit om de volledige set beschik bare Red Hat-afbeeldingen in azure weer te geven

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Naamgevings Conventie
VM-installatie kopieën in azure zijn ingedeeld op uitgever, aanbieding, SKU en versie. De combi natie van Publisher: aanbieding: SKU: version is de afbeelding URN en identificeert de afbeelding die moet worden gebruikt.

`RedHat:RHEL:7-LVM:7.6.2018103108` verwijst bijvoorbeeld naar een RHEL 7,6 LVM-gepartitioneerde afbeelding die is gebouwd op 31 oktober 2018.

Hieronder ziet u een voor beeld van het maken van een RHEL 7,6 VM.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>De ' meest recente ' moniker
De Azure REST API staat het gebruik van moniker "nieuwste" toe voor versie in plaats van de specifieke versie. Als u ' nieuwste ' gebruikt, wordt de meest recente beschik bare installatie kopie ingericht voor de gegeven Uitgever, aanbieding en SKU.

`RedHat:RHEL:7-LVM:latest` verwijst bijvoorbeeld naar de meest recente LVM-gepartitioneerde installatie kopie van RHEL 7-familie.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> In het algemeen is het vergelijken van versies om het meest recent te bepalen, de regels van de [methode CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Deze vergelijking van de afbeeldings versie wordt uitgevoerd door de waarden te vergelijken als een [versie](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) object, niet als een teken reeks.

## <a name="rhel-6-image-types"></a>RHEL 6-afbeeldings typen
Voor RHEL 6. x-installatie kopieën zijn de volgende afbeeldings typen:

|Uitgever | Aanbieding | SKU-waarde | Version | Details
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Secundaire versie (bijvoorbeeld 6,9) | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 6.9.2018010506) | Alle Standard RHEL 6. x-installatie kopieën volgen deze Conventie
|RedHat | RHEL-BYOS | RHEL-raw69 | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 6.9.20181023) | Deze installatie kopie is een RHEL 6,9 BYOS-installatie kopie.
|RedHat | RHEL | RHEL-SAP-APPS | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 6.8.2017053118) | Dit is een installatie kopie van RHEL 6,8 voor SAP-toepassingen. Het is gerechtigd om toegang te krijgen tot SAP-toepassings opslagplaatsen, maar ook met RHEL-opslag plaatsen.
|RedHat | RHEL | RHEL-SAP-HANA | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 6.7.2017053121) | Dit is een RHEL 6,7 voor de SAP HANA-installatie kopie. Het is gerechtigd om toegang te krijgen tot SAP HANA opslagplaatsen, maar ook met base RHEL-opslag plaatsen.

## <a name="rhel-7-image-types"></a>RHEL 7-afbeeldings typen
Voor RHEL 7. x-installatie kopieën zijn er een aantal verschillende typen installatie kopieën. In de volgende tabel ziet u de verschillende sets met installatie kopieën die wij bieden. U kunt een volledige lijst weer geven met de opdracht AZ CLI `az vm image list --publisher redhat --all`.

>[!NOTE]
> Tenzij anders aangegeven, worden alle installatie kopieën gepartitioneerd en wordt er verbinding gemaakt met gewone RHEL-opslag plaatsen (d.w.z. niet EUS, niet E4S). We gaan verder met het publiceren van alleen LVM-gepartitioneerde installatie kopieën, maar zijn geopend voor feedback over dit besluit. Meer informatie over ondersteuning voor uitgebreide updates en Update Services voor SAP zijn beschikbaar op de [pagina Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata).

|Uitgever | Aanbieding | SKU-waarde | Version | Details
|----------|-------|------------|---------|--------
|RedHat | RHEL | Secundaire versie (bijvoorbeeld 7,6) | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019102813) | Afbeeldingen die vóór april 2019 worden gepubliceerd, worden gekoppeld aan standaard RHEL-opslag plaatsen. Installatie kopieën die na april 2019 zijn gepubliceerd, worden toegevoegd aan de EUS-opslag plaatsen (Extended update support) van Red Hat zodat een specifieke secundaire versie kan worden vergrendeld. Klanten die normale opslag plaatsen willen, moeten de installatie kopieën met 7-LVM of 7-RAW gebruiken in de SKU-waarde (details hieronder). RHEL 7,7 en latere installatie kopieën worden LVM gepartitioneerd. Alle andere installatie kopieën in deze categorie worden gepartitioneerd.
|RedHat | RHEL | 7-RAW | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019102813) | Deze installatie kopieën worden gepartitioneerd (dat wil zeggen dat er geen logische volumes zijn toegevoegd).
|RedHat | RHEL | 7-RAW-CI | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019072418) | Deze installatie kopieën worden onbewerkt gepartitioneerd (dat wil zeggen dat er geen logische volumes zijn toegevoegd) en dat Cloud-init wordt gebruikt voor het inrichten.
|RedHat | RHEL | 7-LVM | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019062414) | Deze installatie kopieën worden LVM gepartitioneerd.
|RedHat | RHEL-BYOS | RHEL-{LVM, RAW} | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.7.20190819) | Deze installatie kopieën zijn de RHEL 7 BYOS-installatie kopieën. Ze zijn niet gekoppeld aan opslag plaatsen en worden niet in rekening gebracht voor de kosten voor RHEL Premium. Als u geïnteresseerd bent in de RHEL BYOS-installatie kopieën, [vraagt u om toegang](https://aka.ms/rhel-byos). SKU-waarden eindigen met de secundaire versie en duiden aan of de afbeelding RAW of LVM is gepartitioneerd. Een SKU-waarde van RHEL-lvm77 duidt bijvoorbeeld op een LVM gepartitioneerde RHEL 7,7-installatie kopie.
|RedHat | RHEL | RHEL-SAP | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019071300) | Deze installatie kopieën zijn RHEL voor SAP-installatie kopieën. Ze hebben het recht om toegang te krijgen tot de opslag plaatsen SAP HANA en toepassingen, evenals RHEL E4S-opslag plaatsen. Facturering bevat de RHEL Premium en de SAP Premium boven op de basis reken kosten.
|RedHat | RHEL | RHEL-SAP-HA | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019062320) | Deze installatie kopieën zijn RHEL voor SAP met hoge Beschik baarheid en installatie kopieën van Update Services. Ze hebben het recht om toegang te krijgen tot de opslag plaatsen SAP HANA en toepassingen en de opslag plaatsen met hoge Beschik baarheid, evenals RHEL E4S-opslag plaatsen. Billing bevat de Premium van RHEL Premium, SAP Premium en HA over de basis reken kosten.
|RedHat | RHEL | RHEL-HA | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019062019) | Dit zijn RHEL-installatie kopieën die ook recht hebben op toegang tot de invoeg toepassing met hoge Beschik baarheid. Ze zullen iets extra kosten boven op de RHEL en de basis reken kosten als gevolg van de HA-Premium-premie.
|RedHat | RHEL | RHEL-SAP-APPS | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.3.2017053118) | Deze installatie kopieën zijn verouderd omdat de SAP-toepassingen en SAP HANA opslagplaatsen zijn gecombineerd in de SAP-opslag plaatsen. Dit zijn RHEL voor SAP-toepassingen installatie kopieën. Ze hebben het recht om toegang te krijgen tot SAP-toepassings opslagplaatsen, maar ook met RHEL-opslag plaatsen.
|RedHat | RHEL | RHEL-SAP-HANA | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 7.3.2018051421) | Deze installatie kopieën zijn verouderd omdat de SAP-toepassingen en SAP HANA opslagplaatsen zijn gecombineerd in de SAP-opslag plaatsen. Dit zijn RHEL for SAP HANA installatie kopieën. Ze hebben recht op toegang tot SAP HANA opslag plaatsen en met RHEL-opslag plaatsen.

## <a name="rhel-8-image-types"></a>RHEL 8-afbeeldings typen
Details voor RHEL 8-afbeeldings typen vindt u hieronder.

|Uitgever | Aanbieding | SKU-waarde | Version | Details
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 8.0.20191023) | Deze installatie kopieën zijn RHEL 8,0 LVM-gepartitioneerde installatie kopieën die zijn verbonden met Standard Red Hat-opslag plaatsen.
|RedHat | RHEL | 8-Gen2 | Aaneengeschakelde waarden van de RHEL secundaire versie en de gepubliceerde datum (bijvoorbeeld 8.0.20191024) | Deze installatie kopieën zijn Hyper-V-generatie 2 RHEL 8,0 LVM-gepartitioneerde installatie kopieën die zijn verbonden met Standard Red Hat-opslag plaatsen. Meer informatie over virtuele machines van generatie 2 in Azure is [hier](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)beschikbaar.

## <a name="rhel-longer-support-add-ons"></a>RHEL meer ondersteuning voor invoeg toepassingen

### <a name="extended-update-support-eus"></a>Ondersteuning voor uitgebreide updates (EUS)
Sinds 2019 april zijn er RHEL-installatie kopieën beschikbaar die standaard zijn gekoppeld aan de EUS-opslag plaatsen (Extended update support). Meer informatie over RHEL EUS vindt u in [de documentatie van Red Hat](https://access.redhat.com/articles/rhel-eus).

Overschakelen naar EUS-opslag plaatsen is mogelijk en wordt ondersteund. Instructies voor het scha kelen tussen uw virtuele machine en EUS en meer informatie over de ondersteuning van de eind datums van EUS zijn [hier](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)beschikbaar.

>[!NOTE]
> EUS wordt niet ondersteund voor RHEL-Extra's. Dit betekent dat als u een pakket installeert dat doorgaans beschikbaar is via het RHEL Extrass-kanaal, u dit niet kunt doen wanneer u op EUS. De product levenscyclus van Red Hat extras wordt [hier](https://access.redhat.com/support/policy/updates/extras/)beschreven.

#### <a name="differentiating-between-regular-and-eus-images"></a>Onderscheid te onderscheiden van normale en EUSe installatie kopieën.
Klanten die installatie kopieën willen gebruiken die zijn gekoppeld aan EUS-opslag plaatsen, moeten de RHEL-installatie kopie gebruiken die een RHEL-secundair versie nummer in de SKU bevat.

U kunt bijvoorbeeld de volgende twee RHEL 7,4-installatie kopieën zien:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
In dit geval wordt `RedHat:RHEL:7.6:7.6.2019102813` standaard aan EUS-opslag plaatsen gekoppeld (SKU-waarde 7,4) en wordt `RedHat:RHEL:7-LVM:7.6.2019062414` standaard aan niet-EUS-opslag plaatsen gekoppeld (SKU-waarde van 7-LVM).

Als u normale (niet-EUS) opslag plaatsen wilt gebruiken, implementeert u een installatie kopie die geen secundair versie nummer in de SKU bevat.

#### <a name="rhel-images-with-eus"></a>RHEL-installatie kopieën met EUS
De volgende tabel is van toepassing op RHEL-installatie kopieën die zijn verbonden met EUS-opslag plaatsen.

>[!NOTE]
> Op het moment van schrijven hebben alleen RHEL 7,4 en latere secundaire versies EUS-ondersteuning. EUS wordt niet meer ondersteund voor RHEL < = 7,3.
>
> Meer informatie over de beschik baarheid van RHEL EUS vindt u [hier](https://access.redhat.com/support/policy/updates/errata).

Secundaire versie |Voor beeld van een EUS-afbeelding              |EUS-status                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | Afbeeldingen die zijn gepubliceerd 2019 april en hoger, worden standaard EUS|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Afbeeldingen die zijn gepubliceerd 2019 juni en hoger, worden standaard EUS |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Afbeeldingen die zijn gepubliceerd, zijn mogelijk 2019 en hoger EUS standaard  |
RHEL 8,0      |N.v.t.                            | Geen EUS beschikbaar voor Red Hat                               |

### <a name="update-services-for-sap-e4s"></a>Update Services voor SAP (E4S)
De meest recente RHEL voor SAP-installatie kopieën worden aangesloten op de Update Services for SAP Solutions abonnementen (E4S). Meer informatie over E4S vindt u in de [documentatie](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)van Red Hat.

#### <a name="rhel-images-with-e4s"></a>RHEL-installatie kopieën met E4S
Installatie kopieën van de volgende aanbiedingen die na december 2019 zijn gemaakt, worden verbonden met E4S-opslag plaatsen.

* RHEL-SAP (RHEL voor SAP)
* RHEL-SAP-HA (RHEL voor SAP met HA en Update Services)

## <a name="other-available-offers-and-skus"></a>Andere beschik bare aanbiedingen en Sku's
De volledige lijst met beschik bare aanbiedingen en Sku's kan extra installatie kopieën bevatten, behalve wat wordt vermeld in de bovenstaande tabel, bijvoorbeeld `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Deze aanbiedingen kunnen worden gebruikt om ondersteuning te bieden voor specifieke Marketplace-oplossingen, of ze kunnen worden gepubliceerd voor voor beelden en test doeleinden. Ze kunnen op elk gewenst moment zonder waarschuwing worden gewijzigd of verwijderd. Gebruik deze niet, tenzij hun aanwezigheid openbaar is gemaakt door micro soft of Red Hat.

## <a name="publishing-policy"></a>Publicatie beleid
Micro soft en Red Hat update-installatie kopieën als nieuwe secundaire versies worden uitgebracht, indien nodig om specifieke CVEs te verhelpen of voor incidentele configuratie wijzigingen/-updates. We streven ernaar bijgewerkte installatie kopieën zo snel mogelijk te leveren, binnen drie werk dagen na een release of Beschik baarheid van een CVE-oplossing.

We werken alleen de huidige secundaire release bij in een opgegeven installatie kopie serie. Als er een nieuwere secundaire versie wordt uitgebracht, wordt de oudere secundaire versie niet meer bijgewerkt. Met de release van RHEL 7,6 worden RHEL 7,5-installatie kopieën bijvoorbeeld niet meer bijgewerkt.

>[!NOTE]
> Actieve Azure-Vm's die zijn ingericht op basis van RHEL betalen per gebruik-installatie kopieën zijn verbonden met de Azure-RHUI en kunnen updates en oplossingen ontvangen zodra deze zijn uitgebracht door Red Hat en worden gerepliceerd naar de Azure-RHUI (doorgaans in minder dan 24 uur na de officiële versie van Red Hat) . Voor deze Vm's is geen nieuwe gepubliceerde installatie kopie vereist voor het ophalen van updates en klanten hebben volledige controle over het moment waarop de update kan worden gestart.

## <a name="image-retention-policy"></a>Beleid voor het bewaren van installatie kopieën
Ons huidige beleid is om alle eerder gepubliceerde installatie kopieën te blijven gebruiken. Wij behouden ons het recht voor om installatie kopieën te verwijderen waarvan bekend is dat ze problemen kunnen veroorzaken. Zo kunnen installatie kopieën met onjuiste configuraties door volgende platform-of onderdeel updates worden verwijderd. Installatie kopieën die kunnen worden verwijderd, volgen het huidige Marketplace-beleid om meldingen tot 30 dagen te geven voordat de installatie kopie wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen
* Bekijk de volledige lijst met [RHEL-installatie kopieën in azure](./redhat-imagelist.md).
* Lees [hier](https://aka.ms/rhui-update)meer over de Azure Red Hat-update-infra structuur.
* Meer informatie over de [RHEL BYOS-aanbieding](./byos.md).
* Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
