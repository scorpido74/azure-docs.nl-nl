---
title: Overzicht van de Azure API Management-ontwikkelaars Portal
titleSuffix: Azure API Management
description: Meer informatie over de ontwikkelaars Portal in API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: 1f10a577f681d1df4ba99c987df457ea1ca09470
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671608"
---
# <a name="azure-api-management-developer-portal-overview"></a>Overzicht van Azure API Management-ontwikkelaars Portal

Ontwikkelaars Portal is een automatisch gegenereerde, volledig aanpas bare website met de documentatie van uw Api's. Het is waar API-consumers uw Api's kunnen detecteren, meer informatie over het gebruik ervan moeten gebruiken, toegang te vragen en uit te proberen.

In dit artikel worden de verschillen beschreven tussen zelf-hostende en beheerde versies van de ontwikkelaars Portal in API Management. Ook wordt de architectuur uitgelegd en vindt u antwoorden op veelgestelde vragen.

![API Management ontwikkelaars Portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Beheerde en zelf-hostende versies

U kunt uw ontwikkelaars Portal op twee manieren bouwen:

- **Beheerde versie** : door de portal te bewerken en aan te passen, die in uw API Management-exemplaar is ingebouwd en toegankelijk is via de URL `<your-api-management-instance-name>.developer.azure-api.net`. Raadpleeg [Dit documentatie artikel](api-management-howto-developer-portal-customize.md) voor meer informatie over het openen en aanpassen van de beheerde Portal.
- **Zelf-hostende versie** : door uw portal buiten een API Management-exemplaar te implementeren en zelf te hosten. Met deze aanpak kunt u de code base van de portal bewerken en de verschafte kern functionaliteit uitbreiden. u kunt bijvoorbeeld aangepaste widgets implementeren voor integraties met systemen van derden. In dit scenario bent u de maintainer van de portal en bent u verantwoordelijk voor het upgraden van de portal naar de nieuwste versie. Raadpleeg de [github-opslag plaats met de bron code van de portal][1] en [de zelf studie over het implementeren van een widget][3]voor meer informatie en instructies. In de [zelf studie voor de beheerde versie](api-management-howto-developer-portal-customize.md) wordt het beheer paneel van de portal door lopen, wat gebruikelijk is voor de beheerde en zelf-hostende versies.

## <a name="portal-architectural-concepts"></a>Concepten van de portal architectuur

De portal onderdelen kunnen logisch worden onderverdeeld in twee categorieën: *code* en *inhoud*.

De *code* wordt beheerd in [de GitHub-opslag plaats][1] en omvat:

- Widgets: deze vertegenwoordigen visuele elementen en combi neer HTML, java script, de mogelijkheden, instellingen en inhouds toewijzing. Voor beelden zijn een afbeelding, een tekst alinea, een formulier, een lijst met Api's, enzovoort.
- Stijl definities: Hiermee geeft u op hoe widgets kunnen worden opgemaakt
- Engine: Hiermee worden statische webpagina's gegenereerd op basis van de Portal-inhoud en geschreven in Java script
- Visuele editor, die in de browser aanpassing en ontwerp ervaring toestaat

*Inhoud* is onderverdeeld in twee subcategorieën: inhoud van de *Portal* en *API Management inhoud*.

De *Portal-inhoud* is specifiek voor de portal en omvat:

- Pagina's: bijvoorbeeld landings pagina, API-zelf studies, blog berichten
- Media-afbeeldingen, animaties en andere op bestanden gebaseerde inhoud
- Indelingen: sjablonen, die overeenkomen met een URL en bepalen hoe pagina's worden weer gegeven
- Stijlen-waarden voor stijl definities, zoals letter typen, kleuren, randen
- Instellingen-configuratie, bijvoorbeeld favicon, meta gegevens van website

*Portal inhoud*, met uitzonde ring van media, wordt uitgedrukt als JSON-documenten.

*API Management inhoud* bevat entiteiten zoals Api's, bewerkingen, producten, abonnementen.

De portal is gebaseerd op een aangepaste Fork van het [Paperbits-Framework](https://paperbits.io/). De oorspronkelijke Paperbits-functionaliteit is uitgebreid om API Management specifieke widgets te bieden (bijvoorbeeld een lijst met Api's, een lijst met producten) en een connector voor API Management-service voor het opslaan en ophalen van inhoud.

## <a name="faq"></a>Veelgestelde vragen

In deze sectie beantwoorden we veelgestelde vragen over de ontwikkelaars Portal, die algemeen zijn. Raadpleeg [de sectie wiki van de GitHub-opslag plaats](https://github.com/Azure/api-management-developer-portal/wiki)voor vragen die specifiek zijn voor de zelf-hostende versie.

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> hoe kan ik migreren vanuit de preview-versie van de portal?

Door de preview-versie van de ontwikkelaars portal te gebruiken, hebt u de preview-inhoud in uw API Management-service ingericht. De standaard inhoud is aanzienlijk gewijzigd in de algemeen beschik bare versie voor betere gebruikers ervaring. Het omvat ook nieuwe widgets.

Als u de beheerde versie gebruikt, moet u de inhoud van de portal opnieuw instellen door te klikken op **inhoud opnieuw instellen** in de sectie **bewerkingen** menu. Als u deze bewerking bevestigt, wordt de inhoud van de portal verwijderd en wordt de nieuwe standaard inhoud ingericht. De engine van de portal is automatisch bijgewerkt in uw API Management-service.

![Portal-inhoud opnieuw instellen](media/api-management-howto-developer-portal/reset-content.png)

Als u de zelf-hostende versie gebruikt, gebruikt u de `scripts/cleanup.bat` en `scripts/generate.bat` uit de opslag plaats GitHub om bestaande inhoud te verwijderen en nieuwe inhoud in te richten. Zorg ervoor dat u de code van uw portal van tevoren bijwerkt naar de nieuwste versie van de GitHub-opslag plaats.

Als u de inhoud van de portal niet opnieuw wilt instellen, kunt u eventueel pas beschik bare widgets gebruiken in uw pagina's. Bestaande widgets zijn automatisch bijgewerkt naar de nieuwste versie.

Als uw portal is ingericht na de aankondiging van de algemene Beschik baarheid, moet deze al de nieuwe standaard inhoud gebruiken. Aan uw zijde is geen actie vereist.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Hoe kan ik migreren van de oude ontwikkelaars Portal naar de ontwikkelaars Portal?

Portals zijn incompatibel en u moet de inhoud hand matig migreren.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Beschikt de portal over alle functies van de oude Portal?

De ontwikkelaars Portal biedt geen ondersteuning meer voor *toepassingen* en *problemen*.

Verificatie met OAuth in de interactieve ontwikkelaars console wordt nog niet ondersteund. U kunt de voortgang volgen via [het github-probleem](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Is de oude Portal afgeschaft?

De oude portals voor ontwikkel aars en uitgevers zijn nu *verouderde* functies. ze ontvangen alleen beveiligings updates. Nieuwe functies worden alleen geïmplementeerd in de nieuwe ontwikkelaars Portal.

Afschaffing van de verouderde portals wordt afzonderlijk aangekondigd. Als u vragen of opmerkingen hebt, kunt u deze [in een specifiek github-probleem](https://github.com/Azure/api-management-developer-portal/issues/121)opheffen.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>De benodigde functionaliteit wordt niet ondersteund in de portal

U kunt een [functie aanvraag](https://aka.ms/apimwish) openen of [zelf de ontbrekende functionaliteit implementeren][3]. Als u de functionaliteit zelf implementeert, kunt u de ontwikkelaars portal zelf hosten of een pull-aanvraag openen in GitHub om de wijzigingen in de beheerde versie op te nemen.

### <a name="how-can-i-automate-portal-deployments"></a>Hoe kan ik Portal implementaties automatiseren?

U kunt via de REST API programmatisch toegang krijgen tot de inhoud van de ontwikkelaars Portal en deze beheren, ongeacht of u een beheerde of zelf-hostende versie gebruikt.

De API wordt beschreven in [de sectie wiki van de GitHub-opslag plaats][2]. Het kan worden gebruikt voor het automatiseren van de migratie van Portal-inhoud tussen omgevingen, bijvoorbeeld vanuit een test omgeving naar de productie omgeving. Meer informatie over dit proces vindt u [in dit documentatie artikel](https://aka.ms/apimdocs/migrateportal) op github.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Ondersteunt de portal Azure Resource Manager sjablonen en/of is deze compatibel met API Management DevOps Resource Kit?

Nee.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Moet ik extra VNet-connectiviteit inschakelen voor de afhankelijkheden van de beheerde Portal?

In de meeste gevallen-Nee.

Als uw API Management-service zich in een intern VNet bevindt, is uw ontwikkelaars Portal alleen toegankelijk vanuit het netwerk. De hostnaam van het beheer eindpunt moet worden omgezet naar het interne VIP van de service van de computer die u gebruikt voor toegang tot de beheer interface van de portal. Controleer of het beheer eindpunt is geregistreerd in de DNS. In het geval van een onjuiste configuratie ziet u een fout: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Als uw API Management-service zich in een intern VNet bevindt en u deze via Application Gateway via internet opent, moet u ervoor zorgen dat u verbinding met de ontwikkelaars Portal en de beheer eindpunten van API Management inschakelt.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ik heb een aangepast API Management domein toegewezen en de gepubliceerde Portal werkt niet

Nadat u het domein hebt bijgewerkt, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Ik heb een id-provider toegevoegd en deze kan niet worden weer geven in de portal

Nadat u een id-provider (bijvoorbeeld AAD, AAD B2C) hebt geconfigureerd, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ik heb delegering ingesteld en de portal gebruikt deze niet

Nadat u de overdracht hebt ingesteld, moet u [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de wijzigingen van kracht te laten worden.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>De wijzigingen in de configuratie van mijn andere API Management zijn niet door gegeven in de ontwikkelaars Portal

Voor de meeste configuratie wijzigingen (bijvoorbeeld VNet, aanmelden en product voorwaarden) moet [de portal opnieuw worden gepubliceerd](api-management-howto-developer-portal-customize.md#publish).

### <a name="cors"></a>Ik krijg een CORS-fout bij het gebruik van de interactieve console

De interactieve console maakt een API aan de client zijde via de browser. U kunt het CORS-probleem oplossen door [een CORS-beleid](api-management-cross-domain-policies.md#CORS) toe te voegen aan uw API (s). U kunt alle para meters hand matig opgeven of Joker `*` waarden gebruiken. Bijvoorbeeld:

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

> [!NOTE]
> 
> Als u het CORS-beleid toepast in het product bereik, in plaats van de API (s)-scope, en uw API gebruikmaakt van abonnements sleutel verificatie via een header, werkt de console niet.
>
> De browser verleent automatisch een HTTP-aanvraag voor opties, die geen header met de abonnements sleutel bevat. Vanwege de ontbrekende abonnements sleutel kan API Management de aanroep van de opties niet koppelen aan een product, zodat het CORS-beleid niet kan worden toegepast.
>
> Als tijdelijke oplossing kunt u de abonnements sleutel in een query parameter door geven.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Welke machtigingen heb ik nodig om de ontwikkelaars portal te bewerken?

Als u de `Oops. Something went wrong. Please try again later.` fout ziet wanneer u de portal in de beheer modus opent, beschikt u mogelijk niet over de vereiste machtigingen (RBAC).

De verouderde portals hebben de machtiging `Microsoft.ApiManagement/service/getssotoken/action` in het service bereik (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) nodig om de beheerder van de gebruiker toegang te geven tot de portals. De nieuwe portal vereist de machtiging `Microsoft.ApiManagement/service/users/token/action` op het bereik `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`.

U kunt het volgende Power shell-script gebruiken om een rol te maken met de vereiste machtiging. Vergeet niet om de para meter `<subscription-id>` te wijzigen. 

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
 
Zodra de rol is gemaakt, kan deze worden verleend aan een gebruiker uit de sectie **Access Control (IAM)** in de Azure Portal. Als u deze rol aan een gebruiker toewijst, wordt de machtiging in het service bereik toegewezen. De gebruiker kan SAS-tokens genereren namens *elke* gebruiker in de service. Aan het minimum moet deze rol worden toegewezen aan de beheerder van de service. De volgende Power shell-opdracht laat zien hoe u de rol toewijst aan een gebruiker `user1` op het laagste bereik om te voor komen dat er overbodige machtigingen aan de gebruiker worden verleend: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Nadat de machtigingen zijn verleend aan een gebruiker, moet de gebruiker zich afmelden en opnieuw aanmelden bij de Azure Portal om de nieuwe machtigingen van kracht te laten worden.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Ik zie de `Unable to start the portal. See if settings are specified correctly (...)` fout

Deze fout wordt weer gegeven wanneer een `GET` aanroep van `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` mislukt. De aanroep wordt uitgegeven vanuit de browser door de beheer interface van de portal.

Als uw API Management-service zich in een VNet bevindt, raadpleegt u de bovenstaande VNet-connectiviteit.

De aanroep fout kan ook worden veroorzaakt door een SSL-certificaat dat is toegewezen aan een aangepast domein en niet wordt vertrouwd door de browser. Als oplossing kunt u het aangepaste domein van het beheer eindpunt verwijderen-API Management het standaard eindpunt met een vertrouwd certificaat terugvallen.

### <a name="whats-the-browser-support-for-the-portal"></a>Wat is de browser ondersteuning voor de portal?

| Browser                     | Ondersteund       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | Nee              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> wordt ondersteund in de twee nieuwste productie versies.</small>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de nieuwe ontwikkelaars portal:

- [De beheerde ontwikkelaars Portal openen en aanpassen](api-management-howto-developer-portal-customize.md)
- [Zelf-hostende versie van de Portal instellen][2]
- [Uw eigen widget implementeren][3]

Bladeren door andere resources:

- [GitHub-opslag plaats met de bron code][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend