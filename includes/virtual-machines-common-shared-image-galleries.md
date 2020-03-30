---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117227"
---
Shared Image Gallery is een service waarmee u structuur en organisatie opbouwen rond uw beheerde afbeeldingen. Gedeelde galerieën bieden:

- Beheerde wereldwijde replicatie van afbeeldingen.
- Versiebeheer en groepering van afbeeldingen voor eenvoudiger beheer.
- Afbeeldingen met ZRS-accounts (Zone Redundant Storage) in regio's die beschikbaarheidszones ondersteunen. ZRS biedt een betere veerkracht tegen zonale mislukkingen.
- Delen tussen abonnementen en zelfs tussen AD-tenants (Active Directory) met RBAC.
- Hiermee schalen uw implementaties met afbeeldingsreplica's in elke regio.

Met behulp van een gedeelde afbeeldingsgalerie u uw afbeeldingen delen met verschillende gebruikers, serviceprincipals of AD-groepen binnen uw organisatie. Gedeelde afbeeldingen kunnen worden gerepliceerd naar meerdere regio's, voor een snellere schaling van uw implementaties.

Een beheerde afbeelding is een kopie van een volledige VM (inclusief bijgevoegde gegevensschijven) of alleen de OS-schijf, afhankelijk van hoe u de afbeelding maakt. Wanneer u een VM maakt op basis van de afbeelding, wordt een kopie van de VHD's in de afbeelding gebruikt om de schijven voor de nieuwe virtuele machine te maken. De beheerde afbeelding blijft in opslag en kan steeds opnieuw worden gebruikt om nieuwe VM's te maken.

Als u een groot aantal beheerde afbeeldingen hebt die u moet onderhouden en deze beschikbaar wilt stellen in uw hele bedrijf, u een Shared Image Gallery gebruiken als opslagplaats waarmee u uw afbeeldingen eenvoudig delen. 

De functie Gedeelde galerie heeft meerdere resourcetypen:

| Resource | Beschrijving|
|----------|------------|
| **Beheerde afbeelding** | Een basisafbeelding die alleen kan worden gebruikt of kan worden gebruikt om een **afbeeldingsversie** in een afbeeldingsgalerie te maken. Beheerde afbeeldingen worden gemaakt op [gealgemene](#generalized-and-specialized-images) VM's. Een beheerde afbeelding is een speciaal type VHD dat kan worden gebruikt om meerdere VM's te maken en kan nu worden gebruikt om versies van gedeelde afbeeldingen te maken. |
| **Momentopname** | Een kopie van een VHD die kan worden gebruikt om een **afbeeldingsversie**te maken. Momentopnamen kunnen worden genomen van een [gespecialiseerde](#generalized-and-specialized-images) VM (een die niet is gegeneraliseerd) dan alleen gebruikt of met snapshots van gegevensschijven, om een gespecialiseerde afbeeldingversie te maken.
| **Afbeeldingsgalerie** | Net als de Azure Marketplace is een **afbeeldingsgalerie** een opslagplaats voor het beheren en delen van afbeeldingen, maar u bepaalt wie toegang heeft. |
| **Afbeeldingsdefinitie** | Afbeeldingen worden gedefinieerd in een galerie en bevatten informatie over de afbeelding en vereisten voor het gebruik ervan binnen uw organisatie. U informatie opnemen zoals of de afbeelding algemeen of gespecialiseerd is, het besturingssysteem, minimale en maximale geheugenvereisten en releasenotes. Het is een definitie van een type beeld. |
| **Versie van installatiekopie** | Een **afbeeldingsversie** is wat u gebruikt om een vm te maken wanneer u een galerie gebruikt. U meerdere versies van een afbeelding hebben als dat nodig is voor uw omgeving. Net als een beheerde afbeelding wordt de afbeeldingsversie gebruikt om nieuwe schijven voor de virtuele machine te maken wanneer u een **afbeeldingsversie** gebruikt om een afbeeldingsversie te maken. Afbeeldingsversies kunnen meerdere keren worden gebruikt. |

<br>

![Afbeelding die laat zien hoe u meerdere versies van een afbeelding in uw galerie hebben](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Afbeeldingsdefinities

Afbeeldingsdefinities zijn een logische groepering voor versies van een afbeelding. De afbeeldingsdefinitie bevat informatie over waarom de afbeelding is gemaakt, voor welk besturingssysteem het is en informatie over het gebruik van de afbeelding. Een afbeeldingsdefinitie is als een plan voor alle details rond het maken van een specifieke afbeelding. U implementeert een VM niet vanuit een afbeeldingsdefinitie, maar vanuit de afbeeldingsversie die is gemaakt met de definitie.

Er zijn drie parameters voor elke afbeeldingsdefinitie die in combinatie worden gebruikt - **Publisher,** **Offer** en **SKU**. Deze worden gebruikt om een specifieke afbeeldingsdefinitie te vinden. U afbeeldingsversies hebben die een of twee delen, maar niet alle drie de waarden.  Hier zijn bijvoorbeeld drie afbeeldingsdefinities en hun waarden:

|Definitie van installatiekopie|Uitgever|Aanbieding|Sku|
|---|---|---|---|
|myImage1|Contoso|Finance|Back-end|
|myImage2|Contoso|Finance|Front-end|
|myImage3|Testen|Finance|Front-end|

Alle drie van deze hebben unieke sets van waarden. De indeling is vergelijkbaar met de manier waarop u momenteel uitgever, aanbieding en SKU voor [Azure Marketplace-afbeeldingen](../articles/virtual-machines/windows/cli-ps-findimage.md) in Azure PowerShell opgeven om de nieuwste versie van een Marketplace-afbeelding te krijgen. Elke afbeeldingsdefinitie moet een unieke set van deze waarden hebben.

De volgende parameters zijn andere parameters die kunnen worden ingesteld op uw afbeeldingsdefinitie, zodat u uw bronnen gemakkelijker bijhouden:

* Status van het besturingssysteem - U de status van het besturingssysteem instellen op [gegeneraliseerd of gespecialiseerd](#generalized-and-specialized-images).
* Besturingssysteem - kan windows of Linux.
* Beschrijving - gebruik beschrijving om meer gedetailleerde informatie te geven over waarom de afbeeldingsdefinitie bestaat. U bijvoorbeeld een afbeeldingsdefinitie hebben voor uw front-endserver waarop de toepassing vooraf is geïnstalleerd.
* Eula - kan worden gebruikt om te wijzen op een licentieovereenkomst voor eindgebruikers die specifiek is voor de beelddefinitie.
* Privacyverklaring en releasenotities - sla releasenotities en privacyverklaringen op in Azure-opslag en bieden een URI voor toegang tot deze notities als onderdeel van de afbeeldingsdefinitie.
* Einddatum - voeg een einddatum aan uw afbeeldingsdefinitie toe om automatisering te kunnen gebruiken om oude afbeeldingsdefinities te verwijderen.
* Tag - u tags toevoegen wanneer u uw afbeeldingsdefinitie maakt. Zie [Labels gebruiken om uw bronnen te ordenen](../articles/azure-resource-manager/management/tag-resources.md) voor meer informatie over tags
* Minimale en maximale vCPU- en geheugenaanbevelingen - als uw afbeelding vCPU- en geheugenaanbevelingen heeft, u die informatie koppelen aan uw afbeeldingsdefinitie.
* Niet-toegestane schijftypen : u informatie geven over de opslagbehoeften van uw virtuele machine. Als de afbeelding bijvoorbeeld niet geschikt is voor standaard HDD-schijven, voegt u deze toe aan de lijst met niet-toegestane schijven.

## <a name="generalized-and-specialized-images"></a>Algemene en gespecialiseerde afbeeldingen

Er zijn twee besturingssysteemstatussen die worden ondersteund door Shared Image Gallery. Afbeeldingen vereisen doorgaans dat de VM die wordt gebruikt om de afbeelding te maken, is gegeneraliseerd voordat de afbeelding wordt gemaakt. Generaliseren is een proces dat machine- en gebruikersspecifieke informatie uit de VM verwijdert. Voor Windows wordt ook de Sysprep gebruikt. Voor Linux u [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` of `-deprovision+user` parameters gebruiken.

Gespecialiseerde VM's hebben geen proces doorlopen om machinespecifieke informatie en accounts te verwijderen. Ook vm's gemaakt van gespecialiseerde `osProfile` afbeeldingen hebben geen bijbehorende. Dit betekent dat gespecialiseerde afbeeldingen een aantal beperkingen hebben.

- Accounts die kunnen worden gebruikt om zich aan te melden bij de VM, kunnen ook worden gebruikt op elke vm die is gemaakt met behulp van de gespecialiseerde afbeelding die is gemaakt met die VM.
- VM's hebben de **computernaam** van de VM waarvan de afbeelding is overgenomen. U moet de naam van de computer wijzigen om botsingen te voorkomen.
- Het `osProfile` is hoe sommige gevoelige informatie wordt `secrets`doorgegeven aan de VM, met behulp van . Dit kan problemen veroorzaken met KeyVault, WinRM `secrets` en `osProfile`andere functionaliteit die wordt gebruikt in de . In sommige gevallen u managed service identitalen (MSI) gebruiken om deze beperkingen te omzeilen.

> [!IMPORTANT]
> Gespecialiseerde afbeeldingen zijn momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
>
> **Bekende preview-beperkingen** VM's kunnen alleen worden gemaakt op basis van gespecialiseerde afbeeldingen met behulp van de portal of API. Het is geen CLI of PowerShell ondersteuning voor de preview.


## <a name="regional-support"></a>Regionale steun

Bronregio's worden vermeld in de onderstaande tabel. Alle openbare regio's kunnen doelregio's zijn, maar om te repliceren naar Australia Central en Australia Central 2 moet u uw abonnement op de witte lijst hebben staan. Ga naar:https://azure.microsoft.com/global-infrastructure/australia/contact/


| Bronregio's        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Australië - centraal     | China East        | India - zuid        | Europa -west        |
| Australië - centraal 2   | China Oost 2      | Azië - zuidoost     | Verenigd Koninkrijk Zuid           |
| Australië - oost        | China - noord       | Japan - oost         | Verenigd Koninkrijk West            |
| Australië - zuidoost   | China Noord 2     | Japan - west         | US DoD Central     |
| Brazilië - zuid          | Azië - oost         | Korea - centraal      | US DoD East        |
| Canada - midden        | VS - oost           | Korea - zuid        | VS (overheid) - Arizona     |
| Canada - oost           | VS - oost 2         | VS - noord-centraal   | VS (overheid) - Texas       |
| India - centraal         | Oost-VS 2 EUAP    | Europa - noord       | VS (overheid) - Virginia    |
| VS - centraal            | Frankrijk - centraal    | VS - zuid-centraal   | India - west         |
| Centrale EUAP van de VS       | Frankrijk - zuid      | VS - west-centraal    | VS - west            |
|                       |                   |                    | VS - west 2          |



## <a name="limits"></a>Limieten 

Er zijn limieten per abonnement voor het implementeren van bronnen met gedeelde galerieën:
- 100 gedeelde beeldgalerijen, per abonnement, per regio
- 1.000 beelddefinities, per abonnement, per regio
- 10.000 afbeeldingsversies, per abonnement, per regio
- Elke schijf die aan de afbeelding is gekoppeld, moet kleiner zijn dan of gelijk zijn aan 1 TB groot

Zie [Resourcegebruik controleren op limieten](https://docs.microsoft.com/azure/networking/check-usage-against-limits) voor voorbeelden van het controleren van uw huidige gebruik voor meer informatie.
 
## <a name="scaling"></a>Schalen
Met Shared Image Gallery u het aantal replica's opgeven dat azure van de afbeeldingen wilt bewaren. Dit helpt in multi-VM implementatie scenario's als de VM-implementaties kunnen worden verspreid naar verschillende replica's waardoor de kans op het maken van instanties verwerking wordt beperkt als gevolg van overbelasting van een enkele replica.

Met Shared Image Gallery u nu maximaal 1.000 VM-exemplaren implementeren in een virtuele machineschaalset (vanaf 600 met beheerde afbeeldingen). Afbeeldingsreplica's zorgen voor betere implementatieprestaties, betrouwbaarheid en consistentie. U in elke doelregio een ander aantal replica's instellen op basis van de schaalbehoeften voor de regio. Aangezien elke replica een diepe kopie van uw afbeelding is, helpt dit uw implementaties lineair te schalen met elke extra replica. Hoewel we begrijpen dat geen twee afbeeldingen of regio's hetzelfde zijn, volgt hier onze algemene richtlijn voor het gebruik van replica's in een regio:

- VmsS-implementaties (non-Virtual Machine Scale Set) - Voor elke 20 VM's die u gelijktijdig maakt, raden we u aan één replica te bewaren. Als u bijvoorbeeld tegelijkertijd 120 VM's maakt met dezelfde afbeelding in een regio, raden we u aan ten minste 6 replica's van uw afbeelding te bewaren. 
- VMSS-implementaties (Virtual Machine Scale Set) - Voor elke implementatie van schaalsetsets met maximaal 600 exemplaren raden we u aan ten minste één replica te bewaren. Als u bijvoorbeeld 5 schaalsets tegelijk maakt, elk met 600 VM-exemplaren met dezelfde afbeelding in één regio, raden we u aan ten minste 5 replica's van uw afbeelding te bewaren. 

We raden u altijd aan om het aantal replica's te overprovisioneren vanwege factoren zoals afbeeldingsgrootte, inhoud en ostype.

![Afbeelding die laat zien hoe u afbeeldingen schalen](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Maak uw afbeeldingen zeer beschikbaar

[Azure Zone Redundant Storage (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) biedt veerkracht tegen een storing in de beschikbaarheidszone in de regio. Met de algemene beschikbaarheid van Gedeelde afbeeldingengalerij u ervoor kiezen om uw afbeeldingen op te slaan in ZRS-accounts in regio's met beschikbaarheidszones. 

U ook het accounttype voor elk van de doelgebieden kiezen. Het standaardopslagaccounttype is Standard_LRS, maar u Standard_ZRS kiezen voor regio's met beschikbaarheidszones. Bekijk [hier](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)de regionale beschikbaarheid van ZRS.

![Afbeelding met ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicatie
Met Shared Image Gallery u uw afbeeldingen ook automatisch repliceren naar andere Azure-regio's. Elke versie met gedeelde afbeelding kan worden gerepliceerd naar verschillende regio's, afhankelijk van wat zinvol is voor uw organisatie. Een voorbeeld is om altijd de nieuwste afbeelding in meerdere regio's te repliceren, terwijl alle oudere versies alleen beschikbaar zijn in 1 regio. Dit kan helpen besparen op opslagkosten voor Shared Image-versies. 

De regio's waareen gedeelde afbeeldingsversie op wordt gerepliceerd, kunnen worden bijgewerkt na het maken van de tijd. De tijd die nodig is om te repliceren naar verschillende regio's is afhankelijk van de hoeveelheid gegevens die wordt gekopieerd en het aantal regio's waarin de versie wordt gerepliceerd. Dit kan in sommige gevallen enkele uren duren. Terwijl de replicatie plaatsvindt, u de status van replicatie per regio bekijken. Zodra de afbeeldingsreplicatie in een regio is voltooid, u vervolgens een vm of schaalset implementeren met die afbeeldingsversie in de regio.

![Afbeelding die laat zien hoe u afbeeldingen repliceren](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Toegang

Omdat de gedeelde afbeeldingsgalerie, beelddefinitie en afbeeldingsversie allemaal bronnen zijn, kunnen ze worden gedeeld met de ingebouwde native Azure RBAC-besturingselementen. Met RBAC u deze bronnen delen met andere gebruikers, serviceprincipals en groepen. U zelfs toegang delen tot personen buiten de tenant waarin ze zijn gemaakt. Zodra een gebruiker toegang heeft tot de versie gedeelde afbeelding, kan hij een VM of een virtuele machineschaalset implementeren.  Hier is de matrix voor delen die helpt te begrijpen waartoe de gebruiker toegang krijgt:

| Gedeeld met gebruiker     | Galerie met gedeelde installatiekopieën | Definitie van installatiekopie | Versie van installatiekopie |
|----------------------|----------------------|--------------|----------------------|
| Galerie met gedeelde installatiekopieën | Ja                  | Ja          | Ja                  |
| Definitie van installatiekopie     | Nee                   | Ja          | Ja                  |

We raden u aan om op galerijniveau te delen voor de beste ervaring. We raden het delen van afzonderlijke afbeeldingsversies af. Zie [Toegang tot Azure-bronnen beheren met RBAC](../articles/role-based-access-control/role-assignments-portal.md)voor meer informatie over RBAC.

Afbeeldingen kunnen ook op schaal worden gedeeld, zelfs voor huurders met behulp van een registratie van een multi-tenant-app. Zie [VM-afbeeldingen delen in Azure-tenants](../articles/virtual-machines/linux/share-images-across-tenants.md)voor meer informatie over het delen van afbeeldingen tussen tenants in Azure.

## <a name="billing"></a>Billing
Er zijn geen extra kosten verbonden aan het gebruik van de shared image gallery-service. Er worden kosten in rekening gebracht voor de volgende bronnen:
- Opslagkosten voor het opslaan van de versies gedeelde afbeelding. De kosten zijn afhankelijk van het aantal replica's van de afbeeldingsversie en het aantal regio's waaraan de versie is gerepliceerd. Als u bijvoorbeeld 2 afbeeldingen hebt en beide worden gerepliceerd naar 3 regio's, worden er 6 beheerde schijven in rekening gebracht op basis van hun grootte. Zie [Prijzen voor beheerde schijven voor](https://azure.microsoft.com/pricing/details/managed-disks/)meer informatie .
- Netwerkuitgangen kosten voor replicatie van de eerste afbeeldingsversie van het brongebied naar de gerepliceerde regio's. Volgende replica's worden afgehandeld binnen de regio, dus er zijn geen extra kosten. 

## <a name="updating-resources"></a>Bronnen bijwerken

Nadat u bent gemaakt, u enkele wijzigingen aanbrengen in de bronnen van de afbeeldingsgalerie. Deze zijn beperkt tot:
 
Gedeelde afbeeldingsgalerie:
- Beschrijving

Afbeeldingsdefinitie:
- Aanbevolen vCPU's
- Aanbevolen geheugen
- Beschrijving
- Einddatum van het einde van de levensduur

Afbeeldingsversie:
- Aantal regionale replica's
- Doelregio's
- Uitsluiten van de laatste
- Einddatum van het einde van de levensduur

## <a name="sdk-support"></a>SDK-ondersteuning

De volgende SDK's ondersteunen het maken van gedeelde galerieën:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [OK](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Sjablonen

U bronnen voor Shared Image Gallery maken met sjablonen. Er zijn verschillende Azure Quickstart-sjablonen beschikbaar: 

- [Een gedeelde afbeeldingsgalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een afbeeldingsdefinitie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een afbeeldingsversie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van afbeeldingsversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen 

* [Hoe kan ik alle bronnen van gedeelde afbeeldingengalerij voor verschillende abonnementen weergeven?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Kan ik mijn bestaande afbeelding verplaatsen naar de gedeelde afbeeldingsgalerie?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Kan ik een afbeeldingsversie maken vanaf een gespecialiseerde schijf?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Kan ik de bron Shared Image Gallery verplaatsen naar een ander abonnement nadat deze is gemaakt?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Kan ik mijn afbeeldingsversies repliceren in clouds zoals Azure China 21Vianet of Azure Germany of Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Kan ik mijn afbeeldingsversies repliceren voor verschillende abonnementen?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Kan ik afbeeldingsversies delen in Azure AD-tenants?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Hoe lang duurt het om afbeeldingsversies te repliceren in de doelregio's?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Wat is het verschil tussen bronregio en doelregio?](#what-is-the-difference-between-source-region-and-target-region)
* [Hoe geef ik het brongebied op tijdens het maken van de afbeeldingsversie?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Hoe geef ik het aantal replica's van afbeeldingsversies op dat in elke regio moet worden gemaakt?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Kan ik de gedeelde afbeeldingsgalerie op een andere locatie maken dan die voor de afbeeldingsdefinitie en afbeeldingsversie?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Wat zijn de kosten voor het gebruik van de Gedeelde Afbeeldingsgalerie?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Welke API-versie moet ik gebruiken om Shared Image Gallery en Image Definition en Image Version te maken?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Welke API-versie moet ik gebruiken om gedeelde VM- of virtuele machineschaalset te maken uit de afbeeldingsversie?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Hoe kan ik alle bronnen van gedeelde afbeeldingengalerij voor verschillende abonnementen weergeven?

Voer de onderstaande stappen uit om alle bronnen van Shared Image Gallery weer te geven voor abonnementen waartoe u toegang hebt op de Azure-portal:

1. Open de [Azure Portal](https://portal.azure.com).
1. Ga naar **Alle bronnen.**
1. Selecteer alle abonnementen waaronder u alle bronnen wilt weergeven.
1. Zoek naar bronnen van het type **Privégalerie**.
 
   Als u de afbeeldingsdefinities en afbeeldingsversies wilt zien, selecteert u ook **Verborgen typen weergeven**.
 
   Als u alle bronnen van Shared Image Gallery wilt weergeven voor abonnementen waar toe u machtigingen hebt, gebruikt u de volgende opdracht in de Azure CLI:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Kan ik mijn bestaande afbeelding verplaatsen naar de gedeelde afbeeldingsgalerie?
 
Ja. Er zijn 3 scenario's gebaseerd op de soorten afbeeldingen die u mogelijk hebt.

 Scenario 1: Als u een beheerde afbeelding in hetzelfde abonnement hebt als uw SIG, u er een afbeeldingsdefinitie en afbeeldingsversie van maken.

 Scenario 2: Als u een onbeheerde afbeelding in hetzelfde abonnement hebt als uw SIG, u er een beheerde afbeelding van maken en er vervolgens een afbeeldingsdefinitie en afbeeldingsversie van maken. 

 Scenario 3: Als u een VHD in uw lokale bestandssysteem hebt, moet u de VHD uploaden naar een beheerde afbeelding, dan u er een afbeeldingsdefinitie en afbeeldingsversie van maken.

- Zie [Een VHD uploaden](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)als de VHD van een Windows-VM is.
- Als de VHD voor een Linux-VM is, [raadpleegt u Een VHD uploaden](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Kan ik een afbeeldingsversie maken vanaf een gespecialiseerde schijf?

Ja, ondersteuning voor gespecialiseerde schijven als afbeeldingen is in preview. U alleen een VM maken op basis van een gespecialiseerde afbeelding met behulp van de portal[(Windows](../articles/virtual-machines/linux/shared-images-portal.md) of [Linux)](../articles/virtual-machines/linux/shared-images-portal.md)en API. Er is geen PowerShell-ondersteuning voor de preview.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Kan ik de bron Shared Image Gallery verplaatsen naar een ander abonnement nadat deze is gemaakt?

Nee, u de bron voor gedeelde afbeeldingsgaleries niet verplaatsen naar een ander abonnement. U de afbeeldingsversies in de galerie echter naar behoefte repliceren naar andere regio's.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Kan ik mijn afbeeldingsversies repliceren in clouds zoals Azure China 21Vianet of Azure Germany of Azure Government Cloud?

Nee, u afbeeldingsversies niet repliceren in verschillende clouds.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Kan ik mijn afbeeldingsversies repliceren voor verschillende abonnementen?

Nee, u de afbeeldingsversies in verschillende regio's in een abonnement repliceren en gebruiken in andere abonnementen via RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Kan ik afbeeldingsversies delen in Azure AD-tenants? 

Ja, u RBAC gebruiken om te delen met personen in verschillende huurders. Maar als u wilt delen op schaal, raadpleegt u 'Galerijafbeeldingen delen tussen Azure-tenants' met [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) of [CLI.](../articles/virtual-machines/linux/share-images-across-tenants.md)

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Hoe lang duurt het om afbeeldingsversies te repliceren in de doelregio's?

De replicatietijd van de afbeeldingsversie is volledig afhankelijk van de grootte van de afbeelding en het aantal regio's waarin deze wordt gerepliceerd. Als aanbevolen praktijk wordt u echter aangeraden de afbeelding klein te houden en de bron- en doelregio's dicht te houden voor de beste resultaten. U de status van de replicatie controleren met de vlag -Replicatiestatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Wat is het verschil tussen bronregio en doelregio?

Brongebied is het gebied waar uw afbeeldingsversie wordt gemaakt en doelregio's zijn de regio's waar een kopie van uw afbeeldingsversie wordt opgeslagen. Voor elke afbeeldingsversie u slechts één brongebied hebben. Zorg er ook voor dat u de locatie van het brongebied doorgeeft als een van de doelgebieden wanneer u een afbeeldingsversie maakt.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Hoe geef ik het brongebied op tijdens het maken van de afbeeldingsversie?

Tijdens het maken van een afbeeldingsversie u de **locatietag** in CLI en de **locatietag** in PowerShell gebruiken om het brongebied op te geven. Zorg ervoor dat de beheerde afbeelding die u gebruikt als basisafbeelding om de afbeeldingsversie te maken zich op dezelfde locatie bevindt als de locatie waar u de afbeeldingsversie wilt maken. Zorg er ook voor dat u de locatie van het brongebied doorgeeft als een van de doelgebieden wanneer u een afbeeldingsversie maakt.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Hoe geef ik het aantal replica's van afbeeldingsversies op dat in elke regio moet worden gemaakt?

Er zijn twee manieren waarop u het aantal replica's van afbeeldingsversies opgeven dat in elke regio moet worden gemaakt:
 
1. Het aantal regionale replica's dat het aantal replica's opgeeft dat u per regio wilt maken. 
2. Het algemene aantal replica's dat het standaardaantal per regio is, wordt niet opgegeven als het aantal regionale replica's niet is opgegeven. 

Als u het aantal regionale replica's wilt opgeven, geeft u de locatie door samen met het aantal replica's dat u in die regio wilt maken: "South Central US=2". 

Als het aantal regionale replica's niet bij elke locatie is opgegeven, is het standaardaantal replica's het algemene aantal replica's dat u hebt opgegeven. 

Als u het algemene aantal replica's in CLI wilt `az sig image-version create` opgeven, gebruikt u het argument **--replica-telling** in de opdracht.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Kan ik de gedeelde afbeeldingsgalerie op een andere locatie maken dan die voor de afbeeldingsdefinitie en afbeeldingsversie?

Ja, dat is mogelijk. Maar als aanbevolen praktijk raden we u aan om de brongroep, gedeelde afbeeldingsgalerie, afbeeldingsdefinitie en afbeeldingsversie op dezelfde locatie te houden.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Wat zijn de kosten voor het gebruik van de Gedeelde Afbeeldingsgalerie?

Er zijn geen kosten verbonden aan het gebruik van de shared image gallery-service, behalve de opslagkosten voor het opslaan van de afbeeldingsversies en netwerkuitgangskosten voor het repliceren van de afbeeldingsversies van bronregio naar doelregio's.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Welke API-versie moet ik gebruiken om Shared Image Gallery en Image Definition en Image Version te maken?

Als u wilt werken met galerieën voor gedeelde afbeeldingen, afbeeldingsdefinities en afbeeldingsversies, raden we u aan API-versie 2018-06-01 te gebruiken. Zone Redundant Storage (ZRS) vereist versie 2019-03-01 of hoger.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Welke API-versie moet ik gebruiken om gedeelde VM- of virtuele machineschaalset te maken uit de afbeeldingsversie?

Voor implementaties van VM- en Virtuele machineschaalsets met een afbeeldingsversie raden we u aan API-versie 2018-04-01 of hoger te gebruiken.
