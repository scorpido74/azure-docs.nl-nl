---
title: Plannen maken voor een aanbieding van een virtuele machine op Azure Marketplace
description: Meer informatie over het maken van plannen voor een aanbieding van een virtuele machine op Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: bc5e98484560fcc15e0ea3e289069c84687f158c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040544"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Plannen maken voor een aanbieding van een virtuele machine

U kunt in Partner Center diverse opties voor het plannen bieden binnen dezelfde aanbieding. Voor een aanbieding is ten minste één abonnement vereist (voorheen een SKU genoemd), wat kan variëren per verdiensten maximaliseren-doel, Azure-regio,-functies of VM-installatie kopieën.

U kunt Maxi maal 100 abonnementen maken voor elke aanbieding: Maxi maal 45 van deze kunnen privé zijn. Meer informatie over privé-abonnementen [vindt u in de micro soft Commercial Marketplace](private-offers.md).

Nadat u uw plannen hebt gemaakt, selecteert u het tabblad Overzicht van het **plan** dat u wilt weer geven:

- Namen van plannen
- Licentie modellen
- Doel groep (openbaar of privé)
- Huidige publicatie status
- Beschikbare acties

De acties die beschikbaar zijn in het deel venster **plan overzicht** variëren, afhankelijk van de huidige status van uw abonnement.

- Als de plan status een concept is, selecteert u **concept verwijderen** .
- Als de status van het plan Live is gepubliceerd, selecteert u **stoppen verkoop plan beëindigen** of **persoonlijke doel groep synchroniseren** .

## <a name="create-a-new-plan"></a>Een nieuw abonnement maken

Selecteer bovenaan **Nieuw abonnement maken** . Het dialoog venster **nieuw plan** wordt weer gegeven.

Maak in het vak **abonnement-id** een unieke plan-id voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in het webadres van het product. Gebruik alleen kleine letters en cijfers, streepjes of onderstrepings tekens en Maxi maal 50.

> [!NOTE]
> De plan-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Voer in het vak **naam van abonnement** een naam in voor dit abonnement. Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak een unieke naam waarmee duidelijk de verschillen tussen de plannen worden aangegeven. U kunt bijvoorbeeld **Windows Server** met *betalen per gebruik* -, *BYOL* -, *Geavanceerd* -en *Enter prise* -abonnementen invoeren.

Selecteer **Maken** .

## <a name="plan-setup"></a>Installatie plannen

Stel de configuratie op hoog niveau in voor het type abonnement, geef op of er een technische configuratie van een ander abonnement wordt gebruikt en Identificeer de Azure-regio's waar het plan beschikbaar moet zijn. Uw selecties bepalen welke velden worden weer gegeven op andere deel Vensters voor hetzelfde abonnement.

### <a name="reuse-a-technical-configuration"></a>Een technische configuratie opnieuw gebruiken

Als u meer dan één abonnement van hetzelfde type hebt en de pakketten identiek zijn, kunt u de **technische configuratie van een ander abonnement selecteren met dit abonnement** . Met deze optie kunt u een van de andere plannen van hetzelfde type voor deze aanbieding selecteren en kunt u de technische configuratie ervan opnieuw gebruiken.

> [!NOTE]
> Wanneer u de technische configuratie van een ander abonnement opnieuw gebruikt, verdwijnt het hele tabblad **technische configuratie** uit dit abonnement. De technische configuratie details van het andere abonnement, inclusief eventuele updates die u in de toekomst maakt, worden ook voor dit abonnement gebruikt. Deze instelling kan niet worden gewijzigd nadat het plan is gepubliceerd.

### <a name="azure-regions"></a>Azure-regio's

Uw abonnement moet beschikbaar worden gesteld in ten minste één Azure-regio.

Selecteer de optie **Azure Global** om uw plan beschikbaar te maken voor klanten in alle wereld wijde Azure-regio's die een commerciële Marketplace-integratie hebben. Zie [geografische Beschik baarheid en valuta ondersteuning](marketplace-geo-availability-currencies.md)voor meer informatie.

Selecteer de optie **Azure Government** om uw plan beschikbaar te maken in de regio [Azure Government](../azure-government/documentation-government-welcome.md) . Deze regio voorziet in gecontroleerde toegang voor klanten van Amerikaanse federale, staats-, lokale of tribalee entiteiten, en voor partners die in aanmerking komen voor hen. Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de VS).

Voordat u naar [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)publiceert, moet u uw abonnement testen en valideren in de omgeving, omdat bepaalde eind punten kunnen verschillen. Als u uw abonnement wilt instellen en testen, vraagt u een proef account aan op de pagina met de [Microsoft Azure Government proef versie](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke Azure-regio, kunt u die regio niet verwijderen.

### <a name="azure-government-certifications"></a>Azure Government-certificeringen

Deze optie is alleen zichtbaar als u **Azure Government** als de Azure-regio hebt geselecteerd in de vorige sectie.

Azure Government services verwerken gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS. Als u uw certificeringen voor deze Program ma's wilt controleren, kunt u Maxi maal 100 koppelingen opgeven. Deze kunnen links naar uw vermelding in het programma staan, of koppelingen naar beschrijvingen van uw naleving met hen op uw eigen websites. Deze koppelingen zijn alleen zichtbaar voor Azure Government klanten.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="plan-listing"></a>Aanbieding plannen

In deze sectie configureert u de details van het overzicht van het plan. Dit deel venster geeft specifieke informatie weer, die kan verschillen van andere abonnementen in dezelfde aanbieding.

### <a name="plan-name"></a>Plan naam

Dit veld wordt automatisch ingevuld met de naam die u hebt opgegeven bij het maken van het abonnement. Deze naam wordt op Azure Marketplace weer gegeven als de titel van dit abonnement. De waarde is beperkt tot 100 tekens.

### <a name="plan-summary"></a>Samen vatting plannen

Geef een korte samen vatting van uw abonnement, niet de aanbieding. Deze samen vatting is beperkt tot 100 tekens.

### <a name="plan-description"></a>Plan beschrijving

Beschrijf wat dit software plan uniek maakt en Beschrijf eventuele verschillen tussen abonnementen binnen uw aanbieding. Beschrijf het abonnement alleen, niet de aanbieding. De beschrijving van het abonnement kan Maxi maal 2.000 tekens bevatten.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

In dit deel venster configureert u het volgende:

- De markten waar dit abonnement beschikbaar is. Elk plan moet beschikbaar zijn op ten minste één [markt](marketplace-geo-availability-currencies.md).
- De prijs per uur.
- Hiermee wordt aangegeven of het plan zichtbaar moet worden voor iedereen of alleen voor specifieke klanten (een persoonlijke doel groep).

### <a name="markets"></a>Landen

Elk plan moet beschikbaar zijn op ten minste één markt. Schakel het selectie vakje in voor elke markt locatie waar dit abonnement beschikbaar moet zijn voor aankoop. (Gebruikers op deze markten kunnen de aanbieding nog steeds implementeren voor alle Azure-regio's die zijn geselecteerd in de rubriek [installatie plannen](#plan-setup) .) De knop **BTW-overgeschreven** bevat landen/regio's waarin micro soft verkoop-en gebruiks belasting voor u verkrijgt. Publiceren naar China is beperkt tot abonnementen die *gratis* zijn of gebruikmaken van *uw eigen licentie* (BYOL).

Als u al prijzen hebt ingesteld voor uw abonnement in Amerikaanse dollar (USD) valuta en een andere markt locatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wissel koersen. Controleer altijd de prijs voor elke markt voordat u publiceert. Controleer uw prijzen door **export prijzen (XLSX)** te selecteren nadat u uw wijzigingen hebt opgeslagen.

Wanneer u een markt verwijdert, kunnen klanten van die markt die gebruikmaken van actieve implementaties geen nieuwe implementaties maken of de bestaande implementaties opschalen. Bestaande implementaties worden niet beïnvloed.

### <a name="pricing"></a>Prijzen

Selecteer voor het **licentie model** op **gebruik gebaseerd maandelijks gefactureerd plan** om de prijzen voor dit abonnement te configureren of selecteer **Bring your own License** zodat klanten dit plan met hun bestaande licentie kunnen gebruiken.

Voor een op gebruik gebaseerd maandelijks gefactureerd abonnement gebruikt u een van de volgende drie opties voor prijs invoer:

- **Per kern** : prijs per kern in USD opgeven. Micro soft berekent de prijzen per kern grootte en converteert deze in lokale valuta's met behulp van de huidige wissel koers.
- **Per kern grootte** : Geef prijzen per kern grootte op in USD. De prijzen worden door micro soft berekend en omgezet in lokale valuta's met behulp van de huidige wissel koers.
- **Per markt en kern formaat** : Geef prijzen voor elke kern grootte op voor alle markten. U kunt de prijzen importeren vanuit een spread sheet.

> [!NOTE]
> Sla de prijs wijzigingen op om de export van prijs gegevens in te scha kelen. Nadat een prijs voor een markt in uw abonnement is gepubliceerd, kunt u deze later niet meer wijzigen. Om ervoor te zorgen dat de prijzen goed zijn voordat u ze publiceert, exporteert u het werk blad prijzen en bekijkt u de prijzen op elke markt.

### <a name="free-trial"></a>Gratis proefversie

U kunt uw klanten een *gratis proef versie* van één maand of drie maanden of zes maanden aanbieden.

### <a name="visibility"></a>Zicht

U kunt elk plan ontwerpen dat zichtbaar is voor iedereen of alleen voor een vooraf geselecteerde doel groep. Lidmaatschappen in deze beperkte doel groep toewijzen met behulp van Azure-abonnement-Id's.

**Openbaar** : uw abonnement kan worden gezien door iedereen.

**Persoonlijke doel groep** : Maak uw plan alleen zichtbaar voor een voorgeselecteerde doel groep. Nadat het is gepubliceerd als een persoonlijk abonnement, kunt u de doel groep bijwerken of wijzigen in openbaar. Nadat u een plan openbaar hebt gemaakt, moet het openbaar blijven. Het kan niet worden teruggezet naar een privé-abonnement.

> [!NOTE]
> Een persoonlijke of beperkte doel groep wijkt af van de preview-doel groep die u hebt gedefinieerd in het **voorbeeld** venster. Een preview-doel groep heeft toegang tot uw aanbieding _voordat_ deze live naar Azure Marketplace wordt gepubliceerd. Hoewel de keuze van de privé doelgroep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle persoonlijke en open bare plannen voor validatie doeleinden bekijken.

**Beperkte doel groep (Azure-abonnement-id's)** : wijs de doel groep toe die toegang heeft tot dit privé schema met behulp van Azure-abonnement-id's. Voeg eventueel een beschrijving toe van elk Azure-abonnements-ID dat u hebt toegewezen. U kunt Maxi maal 10 abonnements-Id's hand matig of Maxi maal 20.000 Id's toevoegen als u een CSV-werk blad importeert. Azure-abonnements-Id's worden weer gegeven als GUID'S en alle letters moeten kleine letters zijn.

>[!Note]
>Persoonlijke aanbiedingen worden niet ondersteund met Azure-abonnementen die zijn gemaakt via een wederverkoper van het Cloud Solution Provider-programma (CSP).

### <a name="hide-a-plan"></a>Een abonnement verbergen

Als uw virtuele machine alleen indirect moet worden gebruikt wanneer ernaar wordt verwezen via een andere oplossings sjabloon of beheerde toepassing, schakelt u dit selectie vakje in om de virtuele machine te publiceren, maar deze te verbergen bij klanten die mogelijk rechtstreeks worden gezocht of hiernaar kunnen worden gebladerd.

> [!NOTE]
> Verborgen abonnementen bieden geen ondersteuning voor preview-koppelingen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="technical-configuration"></a>Technische configuratie

Geef de installatie kopieën en andere technische eigenschappen op die aan dit plan zijn gekoppeld. Zie Details van de aanbieding van een [VM-aanbieding configureren](azure-vm-create-listing.md)voor meer informatie.

> [!NOTE]
> Het tabblad **technische configuratie** wordt niet weer gegeven als u dit plan hebt geconfigureerd voor het opnieuw gebruiken van pakketten van een ander abonnement op het tabblad **installatie plannen** .

### <a name="operating-system"></a>Besturingssysteem

Voer de volgende handelingen uit in het deel venster van het **besturings systeem** :

- Voor **besturingssysteem familie** selecteert u het besturings systeem **Windows** of **Linux** .
- Voor **release** of **leverancier** selecteert u de Windows-versie of Linux-leverancier.
- Voer een beschrijvende naam voor het besturings systeem in voor de **beschrijvende naam** van het systeem. Deze naam is zichtbaar voor klanten.

### <a name="recommended-vm-sizes"></a>Aanbevolen VM-grootten

Selecteer Maxi maal zes aanbevolen grootten voor virtuele machines die u wilt weer geven op Azure Marketplace.

### <a name="open-ports"></a>Poorten openen

Open open bare of particuliere poorten op een geïmplementeerde virtuele machine.

### <a name="storage-option-for-deployment"></a>Opslag optie voor implementatie

Selecteer bij **schijf implementatie optie** het type schijf implementatie dat door uw klanten kan worden gebruikt voor de virtuele machine. Micro soft raadt aan de implementatie alleen te beperken tot de **implementatie van Managed disks** .

### <a name="properties"></a>Eigenschappen

Voor **ondersteuning van versneld netwerken** selecteert u of uw virtuele machine [versneld netwerken](https://go.microsoft.com/fwlink/?linkid=2124513)ondersteunt.

### <a name="generations"></a>Items

Het genereren van een virtuele machine definieert de virtuele hardware die wordt gebruikt. Op basis van de behoeften van uw klant kunt u een virtuele machine van de eerste generatie, een virtuele machine van de tweede generatie of beide publiceren.

1. Wanneer u een nieuwe aanbieding maakt, selecteert u een **generatie type** en voert u de gevraagde afbeeldings gegevens in:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details.png" alt-text="Een weer gave van de vervolg keuzelijst generatie.":::

2. Selecteer **generatie toevoegen** om nog een generatie aan een plan toe te voegen:

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Een weer gave van de vervolg keuzelijst generatie.":::

    Voer vervolgens de details van de generatie in:

    :::image type="content" source="./media/create-vm/azure-vm-generations-details.png" alt-text="Een weer gave van de vervolg keuzelijst generatie.":::

    De **generatie-id** die u kiest, is zichtbaar voor klanten op locaties zoals product-URL'S en arm-sjablonen (indien van toepassing). Gebruik alleen kleine letters, alfanumerieke tekens, streepjes of onderstrepingen; het kan niet worden gewijzigd nadat deze is gepubliceerd.

3. Als u een bestaande virtuele machine wilt bijwerken die al een generatie 1 heeft gepubliceerd, bewerkt u de gegevens op de pagina **technische configuratie** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-updating.png" alt-text="Een weer gave van de vervolg keuzelijst generatie.":::

Zie [ondersteuning voor virtuele machines van](../virtual-machines/generation-2.md)de tweede generatie op Azure voor meer informatie over de verschillen tussen de mogelijkheden van de eerste en tweede generatie.

### <a name="vm-images"></a>VM-installatiekopieën

Geef een schijf versie en de SAS-URI (Shared Access Signature) voor de installatie kopieën van de virtuele machine op. Voeg Maxi maal 16 gegevens schijven toe voor elke VM-installatie kopie. Geef slechts één nieuwe versie van de installatie kopie op per abonnement in een opgegeven inzending. Nadat een installatie kopie is gepubliceerd, kunt u deze niet meer bewerken, maar wel verwijderen. Als u een versie verwijdert, kunnen zowel nieuwe als bestaande gebruikers een nieuw exemplaar van de verwijderde versie implementeren.

- **Schijf versie** : de versie van de installatie kopie die u opgeeft.
- **SAS-URI** : de locatie in uw Azure Storage-account waar u de virtuele harde schijf van het besturings systeem hebt opgeslagen. Zie voor meer informatie over het ophalen van een SAS-URI [de gedeelde toegangs handtekening-URI voor uw VM-installatie kopie ophalen](azure-vm-get-sas-uri.md).
- Installatie kopieën van de gegevens schijf zijn ook Uri's van gedeelde Access-hand tekeningen voor VHD die zijn opgeslagen in hun Azure-opslag accounts.
- Voeg slechts één afbeelding per inzending toe aan een plan.

Ongeacht welk besturings systeem u gebruikt, voegt u alleen het minimum aantal gegevens schijven toe dat voor de oplossing nodig is. Tijdens de implementatie kunnen klanten geen schijven verwijderen die deel uitmaken van een installatie kopie, maar ze mogen altijd schijven toevoegen tijdens of na de implementatie.

Selecteer **concept opslaan** voordat u doorgaat en terug naar **overzicht** van het plan.

## <a name="next-steps"></a>Volgende stappen

- [Een previewdoelgroep toevoegen](azure-vm-create-preview.md)
