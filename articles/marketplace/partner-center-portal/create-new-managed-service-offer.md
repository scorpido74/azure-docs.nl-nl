---
title: Een beheerde service aanbieding maken in micro soft Commercial Marketplace
description: Het maken van een nieuwe, beheerde service aanbieding voor een vermelding in azure Marketplace met behulp van de portal voor commerciële Marketplace in het partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.date: 08/07/2020
ms.openlocfilehash: 34af52b32d9e31ad003c5e95e288f88b157a944a
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710614"
---
# <a name="create-a-managed-service-offer"></a>Een aanbieding voor beheerde service maken

Beheerde service biedt hulp bij het inschakelen van [Azure Lighthouse](../../lighthouse/overview.md) -scenario's. Wanneer een klant een beheerd service aanbod aanvaardt, kunnen ze resources vrijgeven voor het beheer van de [gedelegeerde resources van Azure](../../lighthouse/concepts/azure-delegated-resource-management.md). Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

U moet beschikken over een [Silver-of Gold-competentie voor het Cloud platform](https://partner.microsoft.com/membership/cloud-platform-competency) of een [Azure expert msp](https://partner.microsoft.com/membership/azure-expert-msp) -aanbod om een beheerde service te kunnen publiceren.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuwe**  >  **beheerde service**voor aanbiedingen.

    ![Illustreert het navigatie menu.](./media/new-offer-managed-service.png)

>[!NOTE]
>Nadat een aanbieding is gepubliceerd, worden wijzigingen die zijn aangebracht in het partner centrum alleen weer gegeven in online winkels na het opnieuw publiceren van de aanbieding. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id**in. Dit is een unieke id voor elke aanbieding in uw account.

* Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
* Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u bijvoorbeeld **test-aanbieding-1**invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
* De aanbiedings-ID kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Voer een **alias**voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

* Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
* De aanbiedings alias kan niet worden gewijzigd nadat u **maken**hebt geselecteerd.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Installatie van aanbieding

### <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Volgens het [certificerings beleid van beheerde services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)is een doel voor de **lead** vereist. Hiermee maakt u een record in uw CRM-systeem telkens wanneer een klant uw aanbieding implementeert.

Zie [Lead Management Overview](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace en de juridische contracten die uw aanbieding ondersteunen.

### <a name="category"></a>Categorie

Selecteer mini maal één en Maxi maal vijf categorieën die worden gebruikt voor het plaatsen van uw aanbieding in de juiste Zoek gebieden voor Marketplace. Neem contact op met de manier waarop uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding.

### <a name="terms-and-conditions"></a>Voorwaarden

Geef uw eigen juridische voor waarden op in het veld voor **waarden** . U kunt ook de URL opgeven waar uw voor waarden kunnen worden gevonden. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="offer-listing"></a>Aanbieding weer geven

Op deze pagina kunt u Marketplace-Details (zoals aanbiedings naam, beschrijving en installatie kopieën) voor uw aanbieding definiëren.

> [!NOTE]
> De inhoud van het aanbiedings aanbod (zoals de beschrijving, documenten, scherm afbeeldingen en gebruiks voorwaarden) is niet vereist voor het Engels, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook acceptabel om een *nuttige koppelings-URL* te bieden om inhoud te bieden in een andere taal dan de versie die wordt gebruikt in de inhoud van de aanbieding.

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de Azure Portal:

:::image type="content" source="media/example-managed-services.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Titel
2. Beschrijving
3. Handige koppelingen
4. Schermopnamen

### <a name="name"></a>Naam

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd voor de **aanbiedings alias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan worden aangemerkt (en u kunt symbolen van het handels merk of copyright bevatten). De naam mag niet langer zijn dan 50 tekens en mag geen emojis bevatten.

### <a name="search-results-summary"></a>Samen vatting van zoek resultaten

Geef een korte beschrijving van uw aanbieding (Maxi maal 100 tekens), die kan worden gebruikt in Zoek resultaten voor Marketplace.

### <a name="long-summary"></a>Lange samen vatting

Geef een langere beschrijving van uw aanbieding (Maxi maal 256 tekens). Deze lange samen vatting kan ook worden gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Beschrijving

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer de URL in van het privacybeleid van uw organisatie (gehost op uw site). U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

### <a name="useful-links"></a>Handige koppelingen

Bied optionele aanvullende online documenten over uw oplossing. Voeg extra nuttige koppelingen toe door te klikken op **+ een koppeling toevoegen**.

### <a name="contact-information"></a>Contactgegevens

In deze sectie moet u de naam, het e-mail adres en het telefoon nummer opgeven voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft en kan worden verschaft aan CSP-partners.

### <a name="support-urls"></a>Url's voor ondersteuning

Als u ondersteuning hebt voor websites voor **Azure Global-klanten** en/of **Azure Government klanten**, kunt u hier de url's opgeven.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

In deze sectie kunt u logo's en installatie kopieën opgeven die worden gebruikt wanneer uw aanbieding wordt weer gegeven aan de klant. Alle installatie kopieën moeten de PNG-indeling hebben.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door https://upload.xboxlive.com Partner Center gebruikte service niet blokkeert.

#### <a name="store-logos"></a>Winkel logo's

Geef een PNG-bestand voor het logo van **grote** grootte op. Het partner centrum gebruikt deze om een **klein** en **gemiddeld** logo te maken. U kunt deze desgewenst later vervangen door andere installatie kopieën.

- **Groot** (van 216 x 216 tot 350 x 350 px, vereist)
- **Gemiddeld** (90 x 90 px, optioneel)
- **Klein** (48 x 48 px, optioneel)

Deze logo's worden op verschillende plaatsen in de vermelding gebruikt:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Schermopnamen

Voeg Maxi maal vijf scherm opnamen toe die laten zien hoe uw aanbieding werkt. Alle scherm afbeeldingen moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video's

U kunt optioneel Maxi maal vijf Video's toevoegen die uw aanbieding aantonen. Deze Video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuur afbeelding van de video in (1280 x 720 pixels).

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

- [Best practices voor Marketplace-aanbiedingen](../gtm-offer-listing-best-practices.md)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="preview"></a>Preview

Voordat u uw aanbieding naar de bredere Marketplace-aanbieding publiceert, moet u deze eerst beschikbaar maken voor een beperkte preview-doel groep. Zo kunt u bevestigen hoe u in de Azure Marketplace wordt weer gegeven voordat u deze beschikbaar maakt voor klanten. Micro soft-ondersteunings-en engineering teams kunnen tijdens deze preview-periode ook uw aanbieding bekijken.

U kunt het voor beeld van de doel groep definiëren door Azure-abonnement-Id's in te voeren in de sectie **voor beeld van doel groep** . U kunt Maxi maal 10 abonnements-Id's hand matig invoeren of een CSV-bestand uploaden met Maxi maal 100 abonnement-Id's.

Klanten die aan deze abonnementen zijn gekoppeld, kunnen de aanbieding in azure Marketplace bekijken voordat deze live gaat. Zorg ervoor dat u hier uw eigen abonnementen opneemt, zodat u een voor beeld van uw aanbieding kunt bekijken.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="plan-overview"></a>Overzicht van plan

Elke aanbieding moet een of meer abonnementen hebben (voorheen Sku's genoemd). U kunt meerdere plannen toevoegen om verschillende functie sets tegen verschillende prijzen te ondersteunen of om een specifiek abonnement voor een beperkt publiek van specifieke klanten aan te passen. Klanten kunnen de plannen weer geven die voor hen beschikbaar zijn onder de bovenliggende aanbieding.

U kunt Maxi maal 100 abonnementen maken voor elke aanbieding: Maxi maal 45 van deze kunnen privé zijn. Meer informatie over privé-abonnementen [vindt u in de micro soft Commercial Marketplace](../private-offers.md).

Op de **overzichts** pagina van het abonnement selecteert u **+ nieuw plan maken**. Voer vervolgens een **plan-id** en een **plan naam**in. Beide waarden mogen alleen kleine letters, streepjes en onderstrepings tekens bevatten, met een maximum van 50 tekens. Deze waarden zijn mogelijk zichtbaar voor klanten en kunnen niet worden gewijzigd nadat u de aanbieding hebt gepubliceerd.

Selecteer **maken** nadat u deze waarden hebt ingevoerd om verder te werken aan uw plan. Er zijn drie secties die moeten worden voltooid: **plan vermelding**, **prijzen en beschik baarheid**en **technische configuratie**.

### <a name="plan-listing"></a>Aanbieding plannen

Geef eerst een **samen vatting van de zoek resultaten** op voor het plan. Dit is een korte beschrijving van uw abonnement (Maxi maal 100 tekens), dat kan worden gebruikt in Zoek resultaten voor Marketplace.

Voer vervolgens een **Beschrijving** in die een gedetailleerdere beschrijving van het plan biedt.

### <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

Op dit moment is er slechts één prijs model dat kan worden gebruikt voor een beheerde service aanbieding: **Bring your own License (BYOL)**. Dit betekent dat u uw klanten rechtstreeks factureert voor kosten met betrekking tot deze aanbieding, en micro soft brengt geen kosten in rekening.

In de sectie **zicht baarheid van plan** kunt u aangeven of dit plan [privé](../../marketplace/private-offers.md)moet zijn. Als u het selectie vakje **Dit is een persoonlijk abonnement is** uitgeschakeld, is uw abonnement niet beperkt tot specifieke klanten (of een bepaald aantal klanten).

> [!NOTE]
> Privé plannen worden niet ondersteund met abonnementen die zijn gemaakt via een wederverkoper van het programma van de Cloud Solution Provider (CSP).

Als u dit plan alleen beschikbaar wilt maken voor specifieke klanten, selecteert u **Ja**. Wanneer u dit doet, moet u de klanten identificeren door hun abonnement-Id's op te geven. Deze kunnen worden ingevoerd op één (voor Maxi maal 10 abonnementen) of door een CSV-bestand (voor Maxi maal 10.000 abonnementen over alle plannen) te uploaden. Zorg ervoor dat u hier uw eigen abonnementen opneemt, zodat u de aanbieding kunt testen en valideren.

> [!IMPORTANT]
> Zodra een plan als openbaar is gepubliceerd, kunt u het niet wijzigen in persoonlijk. Gebruik een privé abonnement om te bepalen welke klanten uw aanbieding mogen accepteren en resources kunnen delegeren. Met een openbaar abonnement kunt u de beschik baarheid van bepaalde klanten of zelfs voor een bepaald aantal klanten niet beperken (hoewel u ervoor kiest om het abonnement niet volledig te verkopen). U kunt de [toegang tot een overdracht verwijderen](../../lighthouse/how-to/remove-delegation.md) nadat een klant alleen een aanbieding heeft geaccepteerd als u een **autorisatie** hebt opgenomen waarbij de **roldefinitie** is ingesteld op de functie voor het verwijderen van de [registratie toewijzing van beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) tijdens het publiceren van de aanbieding. U kunt ook contact opnemen met de klant en vragen om [uw toegang te verwijderen](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

### <a name="technical-configuration"></a>Technische configuratie

In deze sectie van uw abonnement wordt een manifest gemaakt met autorisatie gegevens voor het beheren van klant resources. Deze informatie is vereist om [Azure delegated resource management](../../lighthouse/concepts/azure-delegated-resource-management.md)in te scha kelen.

Controleer de [tenants, rollen en gebruikers in azure Lighthouse-scenario's](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) om te begrijpen welke rollen worden ondersteund en wat de aanbevolen procedures zijn voor het definiëren van uw autorisaties.

> [!NOTE]
> Zoals hierboven vermeld, worden de gebruikers en rollen in uw **autorisatie** vermeldingen toegepast op elke klant die het plan heeft gekocht. Als u de toegang tot een specifieke klant wilt beperken, moet u een privé-abonnement publiceren voor hun exclusieve gebruik.

#### <a name="manifest"></a>Manifest

Geef eerst een **versie** op voor het manifest. Gebruik de indeling *n. n. n* (bijvoorbeeld 1.2.5).

Voer vervolgens uw **Tenant-id**in. Dit is een GUID die is gekoppeld aan de Tenant-ID van de Azure Active Directory (Azure AD) van uw organisatie. dat wil zeggen, de Tenant beheren van waaruit u de resources van uw klanten wilt openen. Als u dit niet hebt, kunt u het vinden door over te grenzen van de account naam in de rechter bovenhoek van de Azure Portal of door te klikken op **overschakelen naar een andere map**.

Als u een nieuwe versie van uw aanbieding publiceert en een bijgewerkt manifest moet maken, selecteert u **+ Nieuw manifest**. Zorg ervoor dat u het versie nummer van de vorige manifest versie verhoogt.

#### <a name="authorization"></a>Autorisatie

Autorisaties definiëren de entiteiten in uw Tenant beheren die toegang hebben tot resources en abonnementen voor klanten die het abonnement hebben gekocht. Aan elk van deze entiteiten wordt een ingebouwde rol toegewezen waarmee specifieke toegangs niveaus worden verleend.

U kunt Maxi maal twintig autorisaties voor elk abonnement maken.

> [!TIP]
> In de meeste gevallen moet u rollen toewijzen aan een Azure AD-gebruikers groep of Service-Principal, in plaats van aan een reeks afzonderlijke gebruikers accounts. Hiermee kunt u toegang voor afzonderlijke gebruikers toevoegen of verwijderen zonder dat u het plan hoeft bij te werken en opnieuw te publiceren wanneer uw toegangs vereisten veranderen. Wanneer u rollen aan Azure AD-groepen toewijst, [moet u ervoor zorgen dat het **groeps type** **Security** en niet **Office 365**is](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Zie voor aanvullende aanbevelingen [tenants, rollen en gebruikers in azure Lighthouse-scenario's](../../lighthouse/concepts/tenants-users-roles.md).

Voor elke **autorisatie**moet u het volgende opgeven. U kunt vervolgens en zo vaak als nodig **autorisaties toevoegen** om meer gebruikers en roldefinities toe te voegen.

- **Azure AD-object-id**: de Azure ad-id van een gebruiker, gebruikers groep of toepassing waaraan bepaalde machtigingen worden toegekend (zoals gedefinieerd door de roldefinitie) voor de resources van uw klanten.
- **Weergave naam van Azure AD-object**: een beschrijvende naam om de klant te helpen het doel van deze autorisatie te begrijpen. De klant krijgt deze naam te zien bij het delegeren van resources.
- **Roldefinitie**: Selecteer een van de beschik bare ingebouwde Azure AD-rollen in de lijst. Met deze rol bepaalt u de machtigingen die de gebruiker in het veld ID van het **Azure AD-object** heeft op de resources van uw klanten. Zie voor beschrijvingen van deze rollen [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) en [functie ondersteuning voor Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).
  > [!NOTE]
  > Als toepasselijke nieuwe ingebouwde rollen worden toegevoegd aan Azure, worden ze hier beschikbaar, hoewel er enige vertraging kan optreden voordat ze worden weer gegeven.
- **Toewijs bare rollen**: deze optie wordt alleen weer gegeven als u gebruikers toegangs beheerder hebt geselecteerd in de **roldefinitie** voor deze autorisatie. Als dat het geval is, moet u hier een of meer toewijs bare rollen toevoegen. De gebruiker in het **object-ID-veld van Azure AD** kan deze rollen toewijzen aan [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md), wat vereist is om [beleid te implementeren dat kan worden hersteld](../../lighthouse/how-to/deploy-policy-remediation.md). Houd er rekening mee dat er geen andere machtigingen zijn gekoppeld aan de rol beheerder van gebruikers toegang voor deze gebruiker.

> [!TIP]
> Om ervoor te zorgen dat u zo nodig de [toegang tot een overdracht kunt verwijderen](../../lighthouse/how-to/remove-delegation.md) , moet u een **autorisatie** toevoegen waarbij de **roldefinitie** is ingesteld op de rol van de [registratie toewijzing voor beheerde services verwijderen](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de Tenant van de klant.

Zodra u alle secties voor uw abonnement hebt voltooid, kunt u **+ nieuw plan maken** , net zo vaak als u nodig hebt om extra plannen te maken. Selecteer **Opslaan** als u klaar bent.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Zodra u alle vereiste delen van de aanbieding hebt voltooid, selecteert u **controleren en publiceren** in de rechter bovenhoek van de portal.

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
  - **Niet gestart** : de sectie is niet gerakend en moet worden voltooid.
  - **Onvolledig** : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. Ga terug naar de sectie (s) en werk deze bij.
  - **Voltooid** : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef in de sectie **opmerkingen voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden**te selecteren. We sturen u een e-mail wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** voor de aanbieding om uw aanbieding te publiceren naar het open bare (of als u een privé-aanbieding naar de persoonlijke doel groep hebt).

### <a name="customer-experience-and-offer-management"></a>Gebruikers ervaring en aanbod beheer

Wanneer een klant uw aanbieding implementeert, kunnen ze abonnementen of resource groepen voor [Azure delegated resource management](../../lighthouse/concepts/azure-delegated-resource-management.md)delegeren. Zie [het voorbereidings proces van de klant](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)voor meer informatie over dit proces.

U kunt op elk gewenst moment [een bijgewerkte versie van uw aanbieding publiceren](update-existing-offer.md) . U kunt bijvoorbeeld een nieuwe functie definitie toevoegen aan een eerder gepubliceerde aanbieding. Als u dit doet, krijgen klanten die de aanbieding al hebben toegevoegd, een pictogram op de pagina [**service providers**](../../lighthouse/how-to/view-manage-service-providers.md) in de Azure Portal waarmee ze kunnen zien dat er een update beschikbaar is. Elke klant kan de wijzigingen controleren en besluiten of ze willen bijwerken naar de nieuwe versie.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
- [Meer informatie over Azure Lighthouse](../../lighthouse/overview.md)
