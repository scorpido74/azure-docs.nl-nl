---
title: Red Hat Enterprise Linux-afbeeldingen in Azure | Microsoft Documenten
description: Meer informatie over Red Hat Enterprise Linux-afbeeldingen in Microsoft Azure.
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
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239876"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Overzicht van Red Hat Enterprise Linux afbeeldingen

In dit artikel worden beschikbare RHEL-afbeeldingen (Red Hat Enterprise Linux) in Azure Marketplace en beleid rond de naamgeving en retentie ervan beschreven.

Zie [Red Hat Enterprise Linux levenscyclus](https://access.redhat.com/support/policy/updates/errata)voor informatie over het ondersteuningsbeleid van Red Hat voor alle versies van RHEL. Zie [Azure-prijscalculator](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)voor prijsgegevens .

>[!IMPORTANT]
> RHEL-afbeeldingen die momenteel beschikbaar zijn in Azure Marketplace ondersteunen byos-licentiemodellen (Bring-your-own-subscription) of pay-as-you-go.RHEL images currently available in Azure Marketplace support either bring-your-own-subscription (BYOS) or pay-as-you-go licensing models. De [Azure Hybrid Use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) en dynamische switching tussen BYOS en pay-as-you-go licenties worden niet ondersteund. Als u de licentiemodus wilt inschakelen, moet u de VM opnieuw implementeren vanuit de bijbehorende afbeelding.

>[!NOTE]
> Voor elk probleem met betrekking tot RHEL-afbeeldingen in Azure Marketplace, dient u een ondersteuningsticket in bij Microsoft.

## <a name="view-images-available-in-azure"></a>Afbeeldingen weergeven die beschikbaar zijn in Azure

Wanneer u zoekt naar 'Red Hat' in Azure Marketplace of wanneer u een bron maakt in de gebruikersinterface van de Azure-portal, ziet u slechts een subset van alle beschikbare RHEL-afbeeldingen. U altijd de volledige set beschikbare VM-afbeeldingen verkrijgen met behulp van de Azure CLI, PowerShell en API.

Voer de volgende opdracht uit om de volledige set beschikbare Red Hat-afbeeldingen in Azure te bekijken:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Naming

VM-afbeeldingen in Azure worden georganiseerd door uitgever, aanbieding, SKU en versie. De combinatie van Publisher:Offer:SKU:Version is de afbeelding URN en identificeert op unieke wijze de afbeelding die moet worden gebruikt.

Verwijst bijvoorbeeld `RedHat:RHEL:8-LVM:8.1.20200318` naar een RHEL 8.1 LVM-partitioned image gebouwd op 18 maart 2020.

Hier wordt een voorbeeld getoond van het maken van een RHEL 8.1 VM.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>De "nieuwste" bijnaam

De Azure REST API maakt het gebruik van de bijnaam "laatste" voor de versie in plaats van de specifieke versie. Met behulp van "laatste" bepalingen de meest recente beschikbare afbeelding voor de gegeven uitgever, aanbieding en SKU.

Verwijst bijvoorbeeld `RedHat:RHEL:8-LVM:latest` naar de nieuwste RHEL 8-serie LVM-partitieafbeelding beschikbaar.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> In het algemeen volgt de vergelijking van versies om de laatste te bepalen de regels van de [CompareTo-methode](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Deze vergelijking van de afbeeldingsversie wordt gedaan door de waarden te vergelijken als een object [Version,](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) niet als een tekenreeks.

## <a name="rhel-6-image-types"></a>RHEL 6-afbeeldingstypen

Voor RHEL 6.x-afbeeldingen worden de afbeeldingstypen weergegeven in de volgende tabel.

|Uitgever | Aanbieding | SKU-waarde | Versie | Details
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Secundaire versie (bijvoorbeeld 6.9) | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 6.9.2018010506) | Alle standaard RHEL 6.x beelden volgen deze conventie.
|RedHat | rhel-byos | rhel-raw69 | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 6.9.20181023) | Deze afbeelding is een RHEL 6.9 BYOS afbeelding.
|RedHat | RHEL | RHEL-SAP-APPS | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 6.8.2017053118) | Deze afbeelding is een RHEL 6.8 voor SAP Applications afbeelding. Het heeft recht op toegang tot SAP Applications repositories en base RHEL repositories.
|RedHat | RHEL | RHEL-SAP-HANA | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 6.7.2017053121) | Deze afbeelding is een RHEL 6.7 voor SAP HANA afbeelding. Het heeft recht op toegang tot SAP HANA repositories en basis RHEL repositories.

## <a name="rhel-7-image-types"></a>RHEL 7-afbeeldingstypen

Voor RHEL 7.x-afbeeldingen zijn er een paar verschillende beeldtypen. De volgende tabel toont de verschillende sets van beelden die wij aanbieden. Als u een volledige lijst wilt `az vm image list --publisher redhat --all`bekijken, gebruikt u de opdracht Azure CLI .

>[!NOTE]
> Tenzij anders aangegeven, zijn alle afbeeldingen LVM-partitie en maken ze verbinding met reguliere RHEL-repositories. Dat wil zeggen dat de repositories geen Extended Update Support (EUS) zijn en geen Update Services voor SAP (E4S) zijn. In de toekomst gaan we over op het publiceren van alleen LVM-partitieafbeeldingen, maar staan we open voor feedback over deze beslissing. Zie [Red Hat Enterprise Linux levenscyclus](https://access.redhat.com/support/policy/updates/errata)voor meer informatie over Extended Update Support and Update Services voor SAP.

|Uitgever | Aanbieding | SKU-waarde | Versie | Details
|----------|-------|------------|---------|--------
|RedHat | RHEL | Secundaire versie (bijvoorbeeld 7.6) | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019102813) | Afbeeldingen die vóór april 2019 zijn gepubliceerd, zijn gekoppeld aan standaard RHEL-repositories. Afbeeldingen die na april 2019 zijn gepubliceerd, zijn gekoppeld aan de EUS-repositories van Red Hat om het vergrendelen van een specifieke secundaire versie mogelijk te maken. Klanten die regelmatig repositories willen, moeten de afbeeldingen gebruiken die 7-LVM of 7-RAW in de SKU-waarde bevatten (details volgen). RHEL 7.7 en later afbeeldingen zijn LVM verdeeld. Alle andere afbeeldingen in deze categorie zijn onverdeeld.
|RedHat | RHEL | 7-RAW | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019102813) | Deze afbeeldingen zijn onverdeeld (er zijn bijvoorbeeld geen logische volumes toegevoegd).
|RedHat | RHEL | 7-RAW-CI | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019072418) | Deze afbeeldingen zijn onverdeeld (er zijn bijvoorbeeld geen logische volumes toegevoegd) en gebruiken cloud-init voor provisioning.
|RedHat | RHEL | 7-LVM | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019062414) | Deze afbeeldingen zijn LVM-verdeeld.
|RedHat | rhel-byos | rhel-{lvm, raw} | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.7.20190819) | Deze afbeeldingen zijn de RHEL 7 BYOS afbeeldingen. Ze zijn niet gekoppeld aan repositories en brengen geen kosten in rekening voor de RHEL-premie. Als je geïnteresseerd bent in de RHEL BYOS-afbeeldingen, [vraag dan toegang aan.](https://aka.ms/rhel-byos) SKU-waarden eindigen met de secundaire versie en geven aan of de afbeelding raw of LVM partitioned is. Een SKU-waarde van rhel-lvm77 geeft bijvoorbeeld een door LVM verdeelde RHEL 7.7-afbeelding aan.
|RedHat | RHEL | RHEL-SAP | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019071300) | Deze afbeeldingen zijn RHEL voor SAP-afbeeldingen. Ze hebben recht op toegang tot de SAP HANA- en Applications-repositories en RHEL E4S-repositories. Facturering omvat de RHEL-premie en de SAP-premie bovenop de basisrekenkosten.
|RedHat | RHEL | RHEL-SAP-HA | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019062320) | Deze afbeeldingen zijn RHEL voor SAP met afbeeldingen met hoge beschikbaarheid en Update Services. Ze hebben recht op toegang tot de SAP HANA- en Applications-repositories en de High Availability repositories en RHEL E4S repositories. Facturering omvat de RHEL-premie, SAP-premie en hoge beschikbaarheidspremie bovenop de basisrekenkosten.
|RedHat | RHEL | RHEL-HA | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.6.2019062019) | Deze afbeeldingen zijn RHEL-afbeeldingen die ook recht hebben op toegang tot de add-on met hoge beschikbaarheid. Ze rekenen iets extra bovenop de RHEL en basisrekenkosten vanwege de high availability add-on premium.
|RedHat | RHEL | RHEL-SAP-APPS | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.3.2017053118) | Deze afbeeldingen zijn verouderd omdat de SAP Applications en SAP HANA repositories werden gecombineerd in de SAP repositories. Deze afbeeldingen zijn RHEL voor SAP Applications afbeeldingen. Ze hebben recht op toegang tot SAP Applications repositories en base RHEL repositories.
|RedHat | RHEL | RHEL-SAP-HANA | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 7.3.2018051421) | Deze afbeeldingen zijn verouderd omdat de SAP Applications en SAP HANA repositories werden gecombineerd in de SAP repositories. Deze beelden zijn RHEL voor SAP HANA beelden. Ze hebben recht op toegang tot SAP HANA repositories en base RHEL repositories.

## <a name="rhel-8-image-types"></a>RHEL 8-afbeeldingstypen

Details voor RHEL 8 beeldtypen zijn hieronder.

|Uitgever | Aanbieding | SKU-waarde | Versie | Details
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 8.0.20191023) | Deze afbeeldingen zijn RHEL 8 LVM-partitieafbeeldingen die zijn verbonden met standaard Red Hat-repositories.
|RedHat | RHEL | 8-gen2 | Gelijktijdige waarden van de rhel-versie en de gepubliceerde datum (bijvoorbeeld 8.0.20191024) | Deze afbeeldingen zijn Hyper-V Generation 2 RHEL 8 LVM-partitie afbeeldingen die zijn aangesloten op standaard Red Hat-repositories. Zie [Ondersteuning voor Generatie 2 VM's op Azure voor](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)meer informatie over Generatie 2 VM's in Azure.

## <a name="rhel-longer-support-add-ons"></a>RHEL langere ondersteuning add-ons

### <a name="extended-update-support"></a>Ondersteuning voor uitgebreide updates

Vanaf april 2019 zijn RHEL-afbeeldingen standaard beschikbaar die zijn gekoppeld aan de EUS-repositories. Meer informatie over RHEL EUS is beschikbaar in [de documentatie van Red Hat.](https://access.redhat.com/articles/rhel-eus)

Overschakelen naar EUS-repositories is mogelijk en wordt ondersteund. Zie [RHEL EUS en rhel VM's](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)voor versievergrendeling voor instructies over het overschakelen naar EUS en meer informatie over EUS-ondersteuning voor de einddatum van EUS.

>[!NOTE]
> EUS wordt niet ondersteund op RHEL Extra's. Dit betekent dat als u een pakket installeert dat meestal beschikbaar is via het RHEL Extras-kanaal, u dit niet doen terwijl u op EUS bent. Zie de levenscyclus van Red Hat Enterprise [Linux Extras](https://access.redhat.com/support/policy/updates/extras/)voor meer informatie over de levenscyclus van het Red Hat Extras-product.

#### <a name="differentiate-between-regular-and-eus-images"></a>Onderscheid maken tussen gewone en EUS-afbeeldingen

Klanten die afbeeldingen willen gebruiken die zijn gekoppeld aan EUS-repositories, moeten de RHEL-afbeelding gebruiken die een RHEL-versienummer in de SKU bevat.

U ziet bijvoorbeeld de volgende twee RHEL 7.4-afbeeldingen beschikbaar.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

In dit `RedHat:RHEL:7.6:7.6.2019102813` geval is standaard gekoppeld aan EUS-repositories. De SKU-waarde is 7,4. En `RedHat:RHEL:7-LVM:7.6.2019062414` is standaard gekoppeld aan niet-EUS-repositories. De SKU-waarde is 7-LVM.

Als u reguliere (niet-EUS)-opslagplaatsen wilt gebruiken, gebruikt u een afbeelding die geen klein versienummer in de SKU bevat.

#### <a name="rhel-images-with-eus"></a>RHEL-beelden met EUS

Informatie in de volgende tabel is van toepassing op RHEL-afbeeldingen die zijn verbonden met EUS-repositories.

>[!NOTE]
> Op het moment van schrijven hebben alleen RHEL 7.4- en latere secundaire versies EUS-ondersteuning. EUS wordt niet langer ondersteund voor RHEL <=7,3.
>
> Zie de levenscyclus van Red Hat [Enterprise Linux voor](https://access.redhat.com/support/policy/updates/errata)meer informatie over de beschikbaarheid van RHEL EUS.

Secundaire versie |EUS-beeldvoorbeeld              |EUS-status                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Afbeeldingen gepubliceerd april 2019 en later zijn standaard EUS.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Afbeeldingen gepubliceerd juni 2019 en later zijn standaard EUS. |
RHEL 7.6      |RedHat:RHEL:7.6:7-6.2019052206 | Afbeeldingen gepubliceerd mei 2019 en later zijn standaard EUS. |
RHEL 8.0      |N.v.t.                            | Er is geen EUS beschikbaar bij Red Hat.                               |

### <a name="update-services-for-sap"></a>Services bijwerken voor SAP

De nieuwste RHEL voor SAP-afbeeldingen wordt gekoppeld aan de Update Services for SAP Solutions subscriptions (E4S). Zie de [Red Hat-documentatie](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)voor meer informatie over E4S.

#### <a name="rhel-images-with-e4s"></a>RHEL-afbeeldingen met E4S

Afbeeldingen van de volgende aanbiedingen die na december 2019 zijn gemaakt, zijn verbonden met E4S-repositories:

* RHEL-SAP (RHEL voor SAP)
* RHEL-SAP-HA (RHEL voor SAP met hoge beschikbaarheid en updatediensten)

## <a name="other-available-offers-and-skus"></a>Andere beschikbare aanbiedingen en SKU's

De volledige lijst met beschikbare aanbiedingen en SKU's bevat mogelijk extra afbeeldingen die verder gaan dan wat in de vorige tabel wordt vermeld. Een voorbeeld is `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Deze aanbiedingen kunnen worden gebruikt om ondersteuning te bieden voor specifieke marktoplossingen. Of ze kunnen worden gepubliceerd voor previews en testdoeleinden. Ze kunnen worden gewijzigd of verwijderd op elk gewenst moment zonder waarschuwing. Gebruik ze niet tenzij hun aanwezigheid openbaar is gedocumenteerd door Microsoft of Red Hat.

## <a name="publishing-policy"></a>Publicatiebeleid

Microsoft en Red Hat updaten afbeeldingen als nieuwe secundaire versies worden vrijgegeven, zoals vereist om specifieke veelvoorkomende kwetsbaarheden en blootstellingen (CvEs) of voor incidentele configuratiewijzigingen of updates aan te pakken. We streven ernaar om zo snel mogelijk bijgewerkte afbeeldingen te leveren binnen drie werkdagen na een release of beschikbaarheid van een CVE-oplossing.

We werken alleen de huidige kleine release in een bepaalde afbeeldingsfamilie bij. Met de release van een nieuwere secundaire versie, stoppen we met het bijwerken van de oudere secundaire versie. Met de release van RHEL 7.6 worden RHEL 7.5-afbeeldingen bijvoorbeeld niet meer bijgewerkt.

>[!NOTE]
> Actieve Azure VM's die zijn ingericht met RHEL-pay-as-you-go-afbeeldingen zijn verbonden met de Azure RHUI en kunnen updates en oplossingen ontvangen zodra ze door Red Hat zijn uitgebracht en worden gerepliceerd naar de Azure RHUI. De timing is meestal minder dan 24 uur na de officiële release door Red Hat. Deze VM's vereisen geen nieuwe gepubliceerde afbeelding voor het verkrijgen van de updates. Klanten hebben volledige controle over wanneer ze de update moeten starten.

## <a name="image-retention-policy"></a>Beleid voor het behoud van afbeeldingen

Huidig beleid is om alle eerder gepubliceerde afbeeldingen te behouden. We behouden ons het recht voor om afbeeldingen te verwijderen waarvan bekend is dat ze problemen van welke aard dan ook veroorzaken. Afbeeldingen met onjuiste configuraties als gevolg van latere platform- of componentupdates kunnen bijvoorbeeld worden verwijderd. Afbeeldingen die mogelijk worden verwijderd, volgen het huidige Azure Marketplace-beleid om meldingen te geven tot 30 dagen voordat de afbeelding wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Zie [RHEL-afbeeldingen (Red Hat Enterprise Linux) (RHEL) beschikbaar in Azure](./redhat-imagelist.md)om de volledige lijst met RHEL-afbeeldingen in Azure weer te geven.
* Zie Red Hat Update Infrastructure voor [on-demand RHEL VM's in Azure voor](https://aka.ms/rhui-update)meer informatie over de Azure Red Hat Update Infrastructure.
* Zie [Red Hat Enterprise Linux bring-your-own-subscription Gold Images in Azure](./byos.md)voor meer informatie over het RHEL BYOS-aanbod.
* Zie [Red Hat Enterprise Linux levenscyclus](https://access.redhat.com/support/policy/updates/errata)voor informatie over het ondersteuningsbeleid van Red Hat voor alle versies van RHEL.
