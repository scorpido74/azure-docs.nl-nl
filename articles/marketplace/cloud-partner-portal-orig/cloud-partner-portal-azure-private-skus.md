---
title: Particuliere SKU's en plannen | Azure Marketplace
description: Hoe u privé SKU's gebruiken om de beschikbaarheid van de aanbieding te beheren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280385"
---
<a name="private-skus-and-plans"></a>Particuliere SKU's en plannen
============

Met privé-SKU's u de beschikbaarheid van SKU's beperken tot specifieke klanten. Wanneer een SKU als privé is gemarkeerd, is deze niet beschikbaar in een openbare catalogus, inclusief op [Azure Marketplace](https://azuremarketplace.microsoft.com) en de [Azure-portal.](https://portal.azure.com) Op de Azure-portal kunnen alleen klanten met toegang tot de SKU deze zien. Daarnaast zouden ze ook worden gevraagd dat ze toegang hebben tot privé-aanbiedingen.

>[!NOTE]
>Privé-SKU's moeten nieuwe unieke SKU/Plan Id's hebben om conflicten met uw openbare SKU's te voorkomen.

U privé-SKU's gebruiken om de volgende scenario's te verwerken:

1.  Publiceer software die u alleen openbaar wilt maken voor specifieke klanten en niet openbaar beschikbaar is.
2.  Publiceer varianten van openbare software tegen een aangepaste prijs voor specifieke klanten.
3.  Publiceer varianten van openbare software met een aangepaste beschrijving en voorwaarden (via nieuwe aanbieding).

Als u alleen de prijs wilt wijzigen, u de schijven van een andere SKU in dezelfde aanbieding opnieuw gebruiken. Met privé SKU's hoeft u geen schijven opnieuw in te dienen via SKU's.

<a name="mark-a-sku-private"></a>Een SKU-privé markeren
---------------------

Als u een SKU als privé wilt markeren, schakelt u de optie in met de vraag of de SKU privé is:

![Een SKU markeren als privé](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

U de schijven in een andere SKU opnieuw gebruiken en de prijzen of de beschrijving wijzigen. Als u de schijven opnieuw wilt gebruiken, selecteert u **Ja** als antwoord op de prompt 'Gebruikt deze SKU afbeeldingen van een openbare SKU'.

Als de SKU is gemarkeerd als privé en de aanbieding andere SKU's heeft met herbruikbare schijven, moet u aangeven dat de SKU schijven van een andere SKU opnieuw gebruikt. U bent ook verplicht om de doelgroep voor de private SKU op te geven.

>[!NOTE]
>Nadat het is gepubliceerd, kan een openbare SKU niet privé worden gemaakt.

<a name="select-an-image"></a>Een afbeelding selecteren
------------------

U nieuwe schijven voor de privé-SKU leveren of dezelfde schijven opnieuw gebruiken die al in een andere SKU zijn geleverd, waarbij alleen de prijs of beschrijving wordt gewijzigd. Als u de schijven opnieuw wilt gebruiken, selecteert u **Ja** als antwoord op de prompt 'Is deze SKU-afbeelding opnieuw gebruiken' van een openbare SKU.

![Afbeelding opnieuw gebruiken aangeven](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Nadat u hebt bevestigd dat de SKU afbeeldingen opnieuw gebruikt, selecteert u de bron of *basis* SKU voor de afbeeldingen:

![Een afbeelding selecteren](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Wanneer u de aanbieding publiceert, worden de afbeeldingen van de geselecteerde SKU beschikbaar gesteld onder de privé-SKU-id met de aangepaste tarieven/voorwaarden. De private SKU zou alleen zichtbaar zijn voor de doelgroep.

Voor afbeeldingsupdates hoeft u alleen de afbeelding van de onderliggende SKU bij te werken. Achter de schermen wordt ook de afbeelding voor de private SKU automatisch bijgewerkt. Als u de afbeelding uit de onderliggende SKU verwijdert, wordt de afbeelding ook uit de privé-SKU verwijderd.

<a name="restricting-the-audience"></a>Het publiek beperken
------------------------

Privéaanbiedingen kunnen alleen worden gevonden en geïmplementeerd door gerichte gebruikers.
Momenteel ondersteunen we het targeten van gebruikers met behulp van abonnements-id's.

Deze abonnementen kunnen worden ingevoerd via een handmatig inschrijfformulier **voor maximaal 10 abonnementen**of door het uploaden van een CSV-bestand, waarmee maximaal **20.000 abonnementen kunnen worden opgenomen.**

Handmatige invoer voor beperkt publiek:

![Doelgroep handmatig beperken](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

CSV Upload voor beperkt publiek:

![CSV gebruiken om het publiek te beperken](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Voorbeeld van CSV-bestandsinhoud:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Wanneer u overschakelt van handmatige invoer naar CSV-uploadweergave of van CSV naar handmatige invoer, blijft de oude lijst met abonnements-id's met toegang tot de SKU niet behouden. Er wordt een waarschuwing weergegeven en de lijst wordt alleen overschreven bij het opslaan van de aanbieding.

<a name="managing-private-audiences"></a>Privédoelgroepen beheren
-------------------------

**Als u het publiek wilt bijwerken zonder het hele aanbod opnieuw te publiceren, brengt u de gewenste doelgroepwijzigingen aan (met behulp van de gebruikersinterface of de API) en start u vervolgens de actie 'Privédoelgroepen synchroniseren'.**

Als uw doelgroep 10 of minder abonnementen heeft, u deze volledig beheren met behulp van de CPP-gebruikersinterface.

Als uw doelgroep meer dan 10 abonnementen heeft, u dit beheren met een CSV-bestand dat u uploaden naar de CPP-gebruikersinterface of de API gebruiken.

Als u de API gebruikt en geen CSV-bestand wilt onderhouden, u de doelgroep rechtstreeks beheren met API volgens de onderstaande instructies.

> [!NOTE]
> Gebruik de Azure-abonnements-ID (abonnementen en SKU's) of Tenant-id (alleen plannen) om een doelgroep toe te voegen aan uw privéaanbieding.

###  <a name="managing-subscriptions-with-the-api"></a>Abonnementen beheren met de API

U de API gebruiken om een CSV te uploaden of uw doelgroep rechtstreeks te beheren (zonder een CSV te gebruiken). In het algemeen hoeft u alleen uw `restrictedAudience` aanbieding op te halen, het object bij te werken en deze wijzigingen vervolgens terug te sturen naar uw aanbieding om doelgroepleden toe te voegen of te verwijderen.

Zo werk je je doelgroeplijst programmatisch bij:

1. [Haal uw aanbiedingsgegevens op:](cloud-partner-portal-api-retrieve-specific-offer.md)

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Zoek objecten met beperkte doelgroep in elke SKU van de aanbieding met deze JPath-query:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Werk het object(en) voor beperkte doelgroep bij voor uw aanbieding.

    **Als u de abonnementslijst voor uw privé-aanbieding oorspronkelijk hebt geüpload vanuit csv-bestand:**

    Je object(en) *met beperkte doelgroep* ziet er zo uit.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Voor elk object met beperkte doelgroep:

    a. Download de `restrictedAudience.uploadedCsvUri`inhoud van . De inhoud is gewoon een CSV-bestand met headers. Bijvoorbeeld:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Voeg zo nodig abonnementen toe of verwijder deze in het gedownloade CSV-bestand.

    c. Upload het bijgewerkte CSV-bestand naar een locatie, zoals [Azure Blob-opslag](../../storage/blobs/storage-blobs-overview.md) of [OneDrive,](https://onedrive.live.com)en maak een alleen-lezen koppeling naar uw bestand. Dit wordt je nieuwe *SasUrl.*

    d. Werk `restrictedAudience.uploadedCsvUri` de sleutel bij met uw nieuwe *SasUrl.*

    **Als u handmatig de oorspronkelijke lijst met abonnementen voor uw privé-aanbieding hebt ingevoerd via de Cloud Partner Portal:**

    Je object(en) *met beperkte doelgroep* ziet er ongeveer zo uit:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Voeg voor elk object met beperkte doelgroep `restrictedAudience.manualEntries` desnoodzaak items toe of verwijder deze in de lijst.

4. Wanneer u klaar bent met het bijwerken van alle *beperkte Audience-objecten* voor elke SKU van uw privé-aanbieding, [werkt u de aanbieding bij:](cloud-partner-portal-api-creating-offer.md)

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Daarmee is je bijgewerkte doelgroeplijst nu van kracht.

<a name="previewing-private-offers"></a>Privéaanbiedingen bekijken
-------------------------

Tijdens de voorvertoning/faseringstap hebben alleen de preview-abonnementen op aanbiedingsniveau toegang tot de SKU. In deze testfase u een voorbeeld van de aanbieding bekijken zoals deze lijkt op uw doelgroep.

Abonnementen voor levelpreview aanbieden om toegang te krijgen tot gefaseerde aanbiedingen:

![Voorbeeld van abonnements-id's bekijken](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Nadat de aanbieding live is, kunnen alleen de abonnementen voor beperkte doelgroepen (ingevoerd via handmatige invoer of CSV) de privé-SKU bekijken en implementeren. We raden u aan **uw eigen abonnementen altijd op te nemen in het beperkte publiek** voor de privé-SKU voor validatiedoeleinden.

>[!NOTE]
>Voor foutopsporingsdoeleinden hebben microsoft-ondersteunings- en engineeringteams ook toegang tot deze privéaanbiedingen.
