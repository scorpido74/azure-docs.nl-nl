---
title: Persoonlijke Sku's en plannen | Azure Marketplace
description: Het gebruik van privé-Sku's voor het beheren van de beschik baarheid van de aanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280385"
---
<a name="private-skus-and-plans"></a>Persoonlijke Sku's en abonnementen
============

Met persoonlijke Sku's kunt u de beschik baarheid van Sku's tot specifieke klanten beperken. Wanneer een SKU is gemarkeerd als privé, is deze niet beschikbaar in een open bare catalogus, inclusief op [Azure Marketplace](https://azuremarketplace.microsoft.com) en de [Azure Portal](https://portal.azure.com). Op het Azure Portal kunnen alleen klanten met toegang tot de SKU het zien. Daarnaast zouden ze ook moeten worden gevraagd dat ze toegang hebben tot persoonlijke aanbiedingen.

>[!NOTE]
>Persoonlijke Sku's moeten nieuwe unieke SKU/plan-Id's hebben om conflicten met uw open bare Sku's te voor komen.

U kunt met behulp van private Sku's de volgende scenario's afhandelen:

1.  Publiceer software die u alleen openbaar beschikbaar wilt maken voor specifieke klanten en niet openbaar beschikbaar is.
2.  Publiceer variaties van open bare software tegen een aangepaste prijs voor specifieke klanten.
3.  Publiceer variaties van open bare software met een aangepaste beschrijving en termen (via een nieuwe aanbieding).

Als u de prijs alleen wilt wijzigen, kunt u de schijven uit een andere SKU gebruiken in dezelfde aanbieding. Met private Sku's hoeft u geen schijven opnieuw te verzenden tussen Sku's.

<a name="mark-a-sku-private"></a>Een SKU privé markeren
---------------------

Als u een SKU als privé markeert, schakelt u de optie om te vragen of de SKU privé is:

![Een SKU markeren als privé](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

U kunt de schijven opnieuw gebruiken in een andere SKU en de prijzen of de beschrijving wijzigen. Als u de schijven opnieuw wilt gebruiken, selecteert u **Ja** als reactie op de prompt ' voert deze SKU installatie kopieën van een open bare SKU opnieuw uit '.

Als de SKU is gemarkeerd als privé en de aanbieding andere Sku's heeft met herbruikbare schijven, moet u aangeven dat de SKU schijven opnieuw gebruikt vanuit een andere SKU. U moet ook de doel groep voor de privé-SKU opgeven.

>[!NOTE]
>Nadat de app is gepubliceerd, kan een open bare SKU niet privé worden gemaakt.

<a name="select-an-image"></a>Een installatie kopie selecteren
------------------

U kunt nieuwe schijven voor de privé-SKU opgeven of dezelfde schijven die al in een andere SKU zijn geleverd hergebruiken, alleen de prijzen of beschrijving wijzigen. Als u de schijven opnieuw wilt gebruiken, selecteert u **Ja** als reactie op de prompt deze SKU wordt opnieuw gebruikt voor het opnieuw gebruiken van een open bare SKU.

![Aangeven dat de afbeelding opnieuw mag worden gebruikt](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Nadat u hebt bevestigd dat de SKU installatie kopieën opnieuw gebruikt, selecteert u de bron-of *basis* -SKU voor de installatie kopieën:

![Een installatie kopie selecteren](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Wanneer u het aanbod publiceert, worden de installatie kopieën van de geselecteerde SKU beschikbaar gesteld onder de persoonlijke SKU-ID met de aangepaste tarieven/voor waarden. De privé-SKU is alleen zichtbaar voor de doel groep.

Voor installatie kopie-updates hoeft u alleen de onderliggende installatie kopie van de SKU bij te werken. Achter de schermen wordt de afbeelding voor de privé-SKU ook automatisch bijgewerkt. Als u de installatie kopie uit de onderliggende SKU verwijdert, wordt de installatie kopie ook uit de privé-SKU verwijderd.

<a name="restricting-the-audience"></a>De doel groep beperken
------------------------

Persoonlijke aanbiedingen kunnen alleen worden gevonden en geïmplementeerd door doel gebruikers.
Momenteel ondersteunen we het doel van gebruikers met behulp van abonnements-Id's.

Deze abonnementen kunnen worden ingevoerd via een invoer formulier **voor Maxi maal 10 abonnementen**, of door een CSV-bestand te uploaden, dat **Maxi maal 20.000 abonnementen**toestaat.

Hand matige invoer voor beperkte doel groep:

![Doel groep hand matig beperken](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

CSV-upload voor beperkte doel groep:

![CSV gebruiken om de doel groep te beperken](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Voor beeld van CSV-bestand inhoud:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Wanneer u overschakelt van hand matige invoer naar CSV-upload weergave of van CSV naar hand matige invoer, wordt de oude lijst met abonnements-Id's met toegang tot de SKU niet bewaard. Er wordt een waarschuwing weer gegeven en de lijst wordt alleen overschreven bij het opslaan van de aanbieding.

<a name="managing-private-audiences"></a>Persoonlijke doel groepen beheren
-------------------------

**Als u de doel groep wilt bijwerken zonder de volledige aanbieding opnieuw te publiceren, maakt u de gewenste wijzigingen in de doel groep (met behulp van de gebruikers interface of de API) en initieert u vervolgens de actie ' persoonlijke doel groepen synchroniseren '.**

Als uw doel groep 10 of minder abonnementen heeft, kunt u deze volledig beheren via de CCP-gebruikers interface.

Als uw doel groep meer dan 10 abonnementen is, kunt u deze beheren met behulp van een CSV-bestand dat u kunt uploaden naar de CCP-gebruikers interface of met behulp van de API.

Als u de API gebruikt en geen CSV-bestand wilt behouden, kunt u het publiek via de onderstaande instructies rechtstreeks beheren via de API.

> [!NOTE]
> Gebruik de Azure-abonnements-ID (plannen en Sku's) of de Tenant-ID (alleen abonnementen) om een doel groep toe te voegen aan uw privé-aanbieding.

###  <a name="managing-subscriptions-with-the-api"></a>Abonnementen beheren met de API

U kunt de API gebruiken voor het uploaden van een CSV of het direct beheren van uw publiek (zonder een CSV-bestand). Over het algemeen hoeft u alleen maar uw aanbieding op te halen, `restrictedAudience` het object bij te werken en vervolgens deze wijzigingen te verzenden naar uw aanbieding om leden van een doel groep toe te voegen of te verwijderen.

U gaat als volgt te werk om de lijst met doel groepen programmatisch bij te werken:

1. [Uw aanbiedings gegevens ophalen](cloud-partner-portal-api-retrieve-specific-offer.md) :

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Zoek naar beperkte doel objecten in elke SKU van de aanbieding met behulp van deze JPath-query:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Werk de beperkte doelgroeps object (en) bij voor uw aanbieding.

    **Als u de lijst met abonnementen voor uw persoonlijke aanbieding oorspronkelijk hebt geüpload vanuit een CSV-bestand:**

    Uw *restrictedAudience* -object (en) zien er als volgt uit.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Voor elk beperkt doel object:

    a. Down load de inhoud `restrictedAudience.uploadedCsvUri`van. De inhoud is gewoon een CSV-bestand met kopteksten. Bijvoorbeeld:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Voeg waar nodig abonnementen in het gedownloade CSV-bestand toe of verwijder deze.

    c. Upload het bijgewerkte CSV-bestand naar een locatie, zoals [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md) of [OneDrive](https://onedrive.live.com), en maak een alleen-lezen koppeling naar uw bestand. Dit is uw nieuwe *SasUrl*.

    d. Werk de `restrictedAudience.uploadedCsvUri` sleutel bij met uw nieuwe *SasUrl*.

    **Als u de oorspronkelijke lijst met abonnementen voor uw persoonlijke aanbieding hand matig hebt ingevoerd in de Cloud Partner-portal:**

    Uw *restrictedAudience* -object (en) ziet er ongeveer als volgt uit:

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

    a. Voeg voor elk beperkt doel object vermeldingen toe aan de lijst of `restrictedAudience.manualEntries` Verwijder deze indien nodig.

4. Als u alle *restrictedAudience* -objecten voor elke SKU van uw persoonlijke aanbieding hebt bijgewerkt, [werkt u de aanbieding](cloud-partner-portal-api-creating-offer.md)bij:

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    De bijgewerkte lijst met doel groepen is nu van kracht.

<a name="previewing-private-offers"></a>Voor beeld van persoonlijke aanbiedingen
-------------------------

Tijdens de voor beeld-en faserings stap hebben alleen de preview-abonnementen voor het aanbod niveau toegang tot de SKU. In deze test fase kunt u een voor beeld bekijken van de aanbieding zoals deze aan uw doel klanten zou worden weer gegeven.

Preview-abonnementen voor aanbiedings niveau voor toegang tot gefaseerde aanbiedingen:

![Preview-abonnement-Id's](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Wanneer de aanbieding Live is, kunnen alleen de abonnementen voor beperkte doel einden (ingevoerd via hand matige invoer of CSV) de privé-SKU bekijken en implementeren. We raden u aan om **altijd uw eigen abonnementen voor validatie doeleinden op te nemen in de beperkte doel groep** voor de privé-SKU.

>[!NOTE]
>Voor fout opsporing hebben micro soft-ondersteunings-en technische teams ook toegang tot deze persoonlijke aanbiedingen.
