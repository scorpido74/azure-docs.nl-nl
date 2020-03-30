---
title: Overzicht van azure API Management-ontwikkelaarsportal
titleSuffix: Azure API Management
description: Meer informatie over de ontwikkelaarsportal in API-beheer.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335900"
---
# <a name="azure-api-management-developer-portal-overview"></a>Overzicht van azure API Management-ontwikkelaarsportal

Developer portal is een automatisch gegenereerde, volledig aanpasbare website met de documentatie van uw API's. Het is waar API-consumenten uw API's kunnen ontdekken, leren hoe ze ze kunnen gebruiken, toegang kunnen aanvragen en uitproberen.

In dit artikel worden de verschillen beschreven tussen zelfgehoste en beheerde versies van de ontwikkelaarsportal in API-beheer. Het legt ook de architectuur uit en geeft antwoorden op veelgestelde vragen.

![API Management-ontwikkelaarsportal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Beheerde en zelfgehoste versies

U uw ontwikkelaarsportal op twee manieren bouwen:

- **Beheerde versie** - door het bewerken en aanpassen van de portal, die is `<your-api-management-instance-name>.developer.azure-api.net`ingebouwd in uw API Management-instantie en toegankelijk is via de URL . Raadpleeg [dit documentatieartikel](api-management-howto-developer-portal-customize.md) voor meer informatie over het openen en aanpassen van de beheerde portal.
- **Zelf gehoste versie** - door uw portal buiten een API-beheerexemplaar te implementeren en zelf te hosten. Met deze aanpak u de codebase van de portal bewerken en de meegeleverde kernfunctionaliteit uitbreiden - bijvoorbeeld aangepaste widgets implementeren voor integraties met systemen van derden. In dit scenario bent u de beheerder van de portal en bent u verantwoordelijk voor het upgraden van de portal naar de nieuwste versie. Voor meer informatie en instructies, verwijzen naar de [GitHub repository met de broncode van de portal][1] en [de tutorial over de uitvoering van een widget][3]. De [zelfstudie voor de beheerde versie](api-management-howto-developer-portal-customize.md) loopt door het beheerderspaneel van de portal, wat gebruikelijk is voor de beheerde en de zelfgehoste versies.

## <a name="portal-architectural-concepts"></a>Portal architectonische concepten

De portalcomponenten kunnen logischerwijs worden onderverdeeld in twee categorieën: *code* en *inhoud.*

*De code* wordt bewaard in [de GitHub-repository][1] en bevat:

- Widgets - die visuele elementen vertegenwoordigen en HTML, JavaScript, stylingvermogen, instellingen en contentmapping combineren. Voorbeelden zijn een afbeelding, een tekstalinea, een formulier, een lijst met API's, enz.
- Stylingdefinities - die aangeven hoe widgets kunnen worden gestyled
- Engine - die statische webpagina's genereert uit portalinhoud en is geschreven in JavaScript
- Visual editor - die het mogelijk maakt voor in-browser maatwerk en authoring ervaring

*Inhoud* is onderverdeeld in twee subcategorieën: *portalinhoud* en *API Management-inhoud.*

*Portal-inhoud* is specifiek voor de portal en omvat:

- Pagina's - bijvoorbeeld, bestemmingspagina, API-zelfstudies, blogberichten
- Media - afbeeldingen, animaties en andere inhoud op basis van bestanden
- Indelingen - sjablonen die zijn gekoppeld aan een URL en bepalen hoe pagina's worden weergegeven
- Stijlen - waarden voor stylingdefinities, zoals lettertypen, kleuren, randen
- Instellingen - configuratie, bijvoorbeeld favicon, metagegevens van de website

*Portal-inhoud*, met uitzondering van media, wordt uitgedrukt als JSON-documenten.

*API Management-inhoud* omvat entiteiten zoals API's, Operations, Producten, Abonnementen.

Het portaal is gebaseerd op een aangepaste vork van het [Paperbits framework.](https://paperbits.io/) De oorspronkelijke Paperbits-functionaliteit is uitgebreid met API Management-specifieke widgets (bijvoorbeeld een lijst met API's, een lijst met producten) en een connector naar de API Management-service voor het opslaan en ophalen van inhoud.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Veelgestelde vragen

In deze sectie beantwoorden we veelgestelde vragen over het ontwikkelaarsportaal, die van algemene aard zijn. Voor vragen die specifiek zijn voor de zelfgehoste versie, raadpleegt u [het wikigedeelte van de GitHub-repository.](https://github.com/Azure/api-management-developer-portal/wiki)

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Hoe kan ik migreren van de preview-versie van de portal?

Met behulp van de preview-versie van de ontwikkelaarsportal hebt u de preview-inhoud in uw API Management-service ingericht. De standaardinhoud is aanzienlijk gewijzigd in de algemeen beschikbare versie voor een betere gebruikerservaring. Het bevat ook nieuwe widgets.

Als u de beheerde versie gebruikt, stelt u de inhoud van de portal opnieuw in door op **Inhoud opnieuw instellen** te klikken in de sectie **Operations-menu.** Als u deze bewerking bevestigt, wordt alle inhoud van de portal verwijderd en wordt de nieuwe standaardinhoud ingericht. De engine van de portal is automatisch bijgewerkt in uw API Management-service.

![Portal-inhoud opnieuw instellen](media/api-management-howto-developer-portal/reset-content.png)

Als u de zelfgehoste versie gebruikt, gebruikt u de `scripts/cleanup.bat` en `scripts/generate.bat` vanuit de GitHub-opslagplaats om bestaande inhoud te verwijderen en nieuwe inhoud te bieden. Zorg ervoor dat je de code van je portal vooraf upgradet naar de nieuwste release vanuit de GitHub-repository.

Als u de inhoud van de portal niet opnieuw wilt instellen, u overwegen om nieuw beschikbare widgets op uw pagina's te gebruiken. Bestaande widgets zijn automatisch bijgewerkt naar de nieuwste versies.

Als uw portal is ingericht na de algemene beschikbaarheidsaankondiging, moet deze al voorzien zijn van de nieuwe standaardinhoud. Er is geen actie vereist van uw kant.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Hoe kan ik migreren van de oude ontwikkelaarsportal naar de ontwikkelaarsportal?

Portals zijn onverenigbaar en u moet de inhoud handmatig migreren.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Heeft het portaal alle kenmerken van het oude portaal?

De ontwikkelaarsportal ondersteunt geen *toepassingen* en *problemen*meer.

Verificatie met OAuth in de interactieve ontwikkelaarsconsole wordt nog niet ondersteund. U de voortgang volgen via [het GitHub-probleem.](https://github.com/Azure/api-management-developer-portal/issues/208)

### <a name="has-the-old-portal-been-deprecated"></a>Is het oude portaal afgeschaft?

De oude portals voor ontwikkelaars en uitgevers zijn nu *verouderde* functies - ze ontvangen alleen beveiligingsupdates. Nieuwe functies worden alleen geïmplementeerd in de nieuwe ontwikkelaarsportal.

De afschaffing van de legacy portals zal apart worden aangekondigd. Als u vragen, zorgen of opmerkingen hebt, stelt u deze aan te roepen [in een speciaal GitHub-probleem.](https://github.com/Azure/api-management-developer-portal/issues/121)

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Functionaliteit die ik nodig heb, wordt niet ondersteund in de portal

U een [functieaanvraag openen](https://aka.ms/apimwish) of [de ontbrekende functionaliteit zelf implementeren.][3] Als u de functionaliteit zelf implementeert, u de ontwikkelaarsportal zelf hosten of een pull-verzoek openen op GitHub om de wijzigingen in de beheerde versie op te nemen.

### <a name="how-can-i-automate-portal-deployments"></a>Hoe kan ik portalimplementaties automatiseren?

U de inhoud van de ontwikkelaarsportal programmatisch openen en beheren via de REST API, ongeacht of u een beheerde of een zelfgehoste versie gebruikt.

De API is gedocumenteerd in [de wikisectie van de GitHub-repository.][2] Het kan worden gebruikt voor het automatiseren van migraties van portalinhoud tussen omgevingen - bijvoorbeeld van een testomgeving naar de productieomgeving. Meer informatie over dit proces vindt u [in dit documentatieartikel](https://aka.ms/apimdocs/migrateportal) op GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Ondersteunt de portal Azure Resource Manager-sjablonen en/of is deze compatibel met API Management DevOps Resource Kit?

Nee.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Moet ik extra VNet-connectiviteit inschakelen voor de beheerde portalafhankelijkheden?

In de meeste gevallen - nee.

Als uw API Management-service zich in een intern VNet bevindt, is uw ontwikkelaarsportal alleen toegankelijk vanuit het netwerk. De hostnaam van het beheereindpunt moet worden opgelost tot de interne VIP van de service vanaf de machine die u gebruikt om toegang te krijgen tot de beheerinterface van de portal. Controleer of het beheereindpunt is geregistreerd in de DNS. In geval van verkeerde configuratie ziet `Unable to start the portal. See if settings are specified correctly in the configuration (...)`u een foutmelding: .

Als uw API Management-service zich in een intern VNet bevindt en u deze via Application Gateway vanaf internet opent, moet u de verbinding met de ontwikkelaarsportal en de beheereindpunten van API-beheer inschakelen.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ik heb een aangepast API Management-domein toegewezen en de gepubliceerde portal werkt niet

Nadat u het domein hebt bijgewerkt, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Ik heb een identiteitsprovider toegevoegd en ik kan deze niet zien in de portal

Nadat u een identiteitsprovider hebt geconfigureerd (bijvoorbeeld AAD, AAD B2C), moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ik heb delegatie ingesteld en het portaal gebruikt het niet

Nadat u de delegatie hebt ingesteld, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Mijn andere wijzigingen in de configuratie van API-beheer zijn niet doorgegeven in de ontwikkelaarsportal

De meeste configuratiewijzigingen (bijvoorbeeld VNet, aanmelding en productvoorwaarden) vereisen [een herpublicatie van de portal.](api-management-howto-developer-portal-customize.md#publish)

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Ik krijg een CORS-fout bij het gebruik van de interactieve console

De interactieve console maakt een API-aanvraag aan clientzijde vanuit de browser. Los het CORS-probleem op door [een CORS-beleid](api-management-cross-domain-policies.md#CORS) toe te voegen aan uw API(s).

U de status van het CORS-beleid controleren in de sectie **Portal-overzicht** van uw API-beheerservice in de Azure-portal. Een waarschuwingsvak geeft een afwezig of verkeerd geconfigureerd beleid aan.

![API Management-ontwikkelaarsportal](media/api-management-howto-developer-portal/cors-azure-portal.png)

Pas het CORS-beleid automatisch toe door op de knop **CORS inschakelen** te klikken.

U CORS ook handmatig inschakelen.

1. Klik op de koppeling Handmatig toepassen op de koppeling **op mondiaal niveau** om de gegenereerde beleidscode te bekijken.
2. Navigeer naar **Alle API's** in het gedeelte **API's** van uw API-beheerservice in de Azure-portal.
3. Klik op **</>** het pictogram in de sectie **Binnenkomende verwerking.**
4. Het beleid invoegen in de **<inbound>** sectie van het XML-bestand. Zorg ervoor **<origin>** dat de waarde overeenkomt met het domein van uw ontwikkelaarsportal.

> [!NOTE]
> 
> Als u het CORS-beleid toepast in de productscope, in plaats van de API(s) scope, en uw API abonnementssleutelverificatie via een koptekst gebruikt, werkt uw console niet.
>
> De browser geeft automatisch een HTTP-aanvraag voor opties uit, die geen koptekst met de abonnementssleutel bevat. Vanwege de ontbrekende abonnementssleutel kan API Management de aanroep OPTIONS niet koppelen aan een product, zodat het cors-beleid niet kan worden toegepast.
>
> Als tijdelijke oplossing u de abonnementssleutel doorgeven in een queryparameter.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Welke machtigingen heb ik nodig om de ontwikkelaarsportal te bewerken?

Als u de `Oops. Something went wrong. Please try again later.` fout ziet wanneer u de portal opent in de beheermodus, ontbreekt het mogelijk dat u niet over de vereiste machtigingen (RBAC) beschikt.

De verouderde portalen `Microsoft.ApiManagement/service/getssotoken/action` hadden de`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`toestemming op de servicescope ( ) nodig om de gebruikersbeheerder toegang te geven tot de portals. De nieuwe portal `Microsoft.ApiManagement/service/users/token/action` vereist de `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`toestemming op het bereik .

U het volgende PowerShell-script gebruiken om een rol te maken met de vereiste toestemming. Vergeet niet `<subscription-id>` om de parameter te wijzigen. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Zodra de rol is gemaakt, kan deze worden toegekend aan elke gebruiker vanuit de sectie **Toegangsbeheer (IAM)** in de Azure-portal. Als u deze rol aan een gebruiker toewijst, wordt de machtiging toegewezen aan het servicebereik. De gebruiker kan SAS-tokens genereren namens *elke* gebruiker in de service. Deze rol moet minimaal worden toegewezen aan de beheerder van de service. Met de volgende PowerShell-opdracht wordt uitgelegd `user1` hoe u de rol aan een gebruiker met het laagste bereik toewijst om te voorkomen dat de gebruiker onnodige machtigingen krijgt: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Nadat de machtigingen aan een gebruiker zijn verleend, moet de gebruiker zich afmelden en opnieuw inloggen bij de Azure-portal om de nieuwe machtigingen van kracht te laten worden.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Ik zie de `Unable to start the portal. See if settings are specified correctly (...)` fout

Deze fout wordt `GET` weergegeven `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` wanneer een oproep mislukt. De oproep wordt vanuit de browser gedaan door de administratieve interface van de portal.

Als uw API Management-service zich in een VNet bevindt, raadpleeg dan de bovenstaande VNet-connectiviteitsvraag.

De oproepfout kan ook worden veroorzaakt door een TLS/SSL-certificaat, dat is toegewezen aan een aangepast domein en niet wordt vertrouwd door de browser. Als beperking u het aangepaste beheerdomein voor eindpuntverwijderen - API-beheer valt terug naar het standaardeindpunt met een vertrouwd certificaat.

### <a name="whats-the-browser-support-for-the-portal"></a>Wat is de browserondersteuning voor de portal?

| Browser                     | Ondersteund       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | Nee              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> Ondersteund in de twee nieuwste productieversies.</small>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de nieuwe ontwikkelaarsportal:

- [Toegang tot de beheerde ontwikkelaarsportal en aanpassen](api-management-howto-developer-portal-customize.md)
- [Zelf gehoste versie van de portal instellen][2]
- [Uw eigen widget implementeren][3]

Blader door andere bronnen:

- [GitHub repository met de broncode][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend