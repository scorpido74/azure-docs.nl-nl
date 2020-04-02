---
title: Uw API-beheerservice configureren met Git - Azure | Microsoft Documenten
description: Meer informatie over het opslaan en configureren van uw API Management-serviceconfiguratie met Git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: b9184808b71cce03882022fd37967fe421e64062
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548989"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Configuratie van API Management-service opslaan en configureren met behulp van Git

Elk API Management-serviceexemplaar onderhoudt een configuratiedatabase met informatie over de configuratie en metagegevens voor de serviceinstantie. Wijzigingen kunnen worden aangebracht in de service-instantie door een instelling in de Azure-portal te wijzigen, een PowerShell-cmdlet te gebruiken of een REST API-aanroep te maken. Naast deze methoden u ook de configuratie van uw service-instantie beheren met Git, waardoor servicebeheerscenario's mogelijk worden, zoals:

* Configuratieversiering - verschillende versies van uw serviceconfiguratie downloaden en opslaan
* Wijzigingen in de bulkconfiguratie : breng wijzigingen aan in meerdere onderdelen van uw serviceconfiguratie in uw lokale opslagplaats en integreer de wijzigingen terug naar de server met één bewerking
* Vertrouwde Git toolchain en workflow - gebruik de Git tooling en workflows die u al kent

In het volgende diagram ziet u een overzicht van de verschillende manieren om uw API Management-serviceinstantie te configureren.

![Git configureren][api-management-git-configure]

Wanneer u wijzigingen aanbrengt in uw service met behulp van de Azure-portal, PowerShell-cmdlets of de REST API, beheert u uw serviceconfiguratiedatabase met behulp van het `https://{name}.management.azure-api.net` eindpunt, zoals aan de rechterkant van het diagram wordt weergegeven. De linkerkant van het diagram laat zien hoe u uw serviceconfiguratie beheren `https://{name}.scm.azure-api.net`met Git- en Git-repository voor uw service op.

De volgende stappen geven een overzicht van het beheer van uw API Management-serviceinstantie met Git.

1. Toegang tot Git-configuratie in uw service
2. Uw serviceconfiguratiedatabase opslaan in uw Git-repository
3. Kloon de Git repo naar uw lokale machine
4. Trek de nieuwste repo naar beneden naar uw lokale machine, en plegen en duwen veranderingen terug naar uw repo
5. De wijzigingen van uw repo implementeren in uw serviceconfiguratiedatabase

In dit artikel wordt beschreven hoe u Git inschakelt en gebruikt om uw serviceconfiguratie te beheren en wordt een referentie weergegeven voor de bestanden en mappen in de Git-opslagplaats.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Toegang tot Git-configuratie in uw service

Als u uw Git-configuratie-instellingen wilt weergeven en configureren, u op het menu **Beveiliging** klikken en naar het tabblad **Configuratieopslagplaats** navigeren.

![GIT inschakelen][api-management-enable-git]

> [!IMPORTANT]
> Alle geheimen die niet zijn gedefinieerd als Benoemde waarden, worden opgeslagen in de repository en blijven in de geschiedenis totdat u de toegang van Git uitschakelt en opnieuw inschakelt. Benoemde waarden bieden een veilige plek om constante tekenreekswaarden, inclusief geheimen, te beheren voor alle API-configuratie en -beleid, zodat u ze niet rechtstreeks in uw beleidsoverzichten hoeft op te slaan. Zie [Benoemde waarden gebruiken in azure API-beheerbeleid](api-management-howto-properties.md)voor meer informatie.
>
>

Zie [Git-toegang](/rest/api/apimanagement/2019-01-01/tenantaccess?EnableGit)inschakelen of uitschakelen met behulp van de REST API voor informatie over het in- of uitschakelen van Git-toegang met de REST API.

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>De serviceconfiguratie opslaan in de Git-repository

De eerste stap voor het klonen van de repository is het opslaan van de huidige status van de serviceconfiguratie in de repository. Klik **op Opslaan in opslagplaats**.

Breng de gewenste wijzigingen aan op het bevestigingsscherm en klik op **Ok** om op te slaan.

Na enkele ogenblikken wordt de configuratie opgeslagen en wordt de configuratiestatus van de repository weergegeven, inclusief de datum en tijd van de laatste configuratiewijziging en de laatste synchronisatie tussen de serviceconfiguratie en de repository.

Zodra de configuratie is opgeslagen in de repository, kan deze worden gekloond.

Zie [Configuratiemomentopname vastleggen met de REST API](/rest/api/apimanagement/2019-01-01/tenantaccess?CommitSnapshot)voor informatie over het uitvoeren van deze bewerking met de REST API.

## <a name="to-clone-the-repository-to-your-local-machine"></a>De opslagplaats klonen naar uw lokale machine

Als u een opslagplaats wilt klonen, hebt u de URL naar uw opslagplaats, een gebruikersnaam en een wachtwoord nodig. Als u gebruikersnaam en andere referenties wilt krijgen, klikt u boven aan de pagina op **Toegangsreferenties** boven aan de pagina.

Als u een wachtwoord wilt genereren, moet u er eerst op toezien dat de **vervaldatum** is ingesteld op de gewenste vervaldatum en -tijd en klikt u vervolgens op **Genereren**.

> [!IMPORTANT]
> Noteer dit wachtwoord. Zodra u deze pagina verlaat, wordt het wachtwoord niet meer weergegeven.
>

In de volgende voorbeelden wordt gebruik gemaakt van de Git Bash-tool van [Git voor Windows,](https://www.git-scm.com/downloads) maar u elke Git-tool gebruiken die u kent.

Open uw Git-gereedschap in de gewenste map en voer de volgende opdracht uit om de git-opslagplaats naar uw lokale machine te klonen met behulp van de opdracht van de Azure-portal.

```
git clone https://{name}.scm.azure-api.net/
```

Geef de gebruikersnaam en het wachtwoord op wanneer daarom wordt gevraagd.

Als u fouten ontvangt, probeert `git clone` u uw opdracht te wijzigen om de gebruikersnaam en het wachtwoord op te nemen, zoals in het volgende voorbeeld wordt weergegeven.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Als dit een fout oplevert, probeert u URL-codering van het wachtwoordgedeelte van de opdracht. Een snelle manier om dit te doen is door Visual Studio te openen en de volgende opdracht uit te geven in **het venster Direct .** Als u het **venster Direct wilt openen,** opent u een oplossing of project in Visual Studio (of maakt u een nieuwe lege consoletoepassing) en kiest u **Windows**, **Onmiddellijk** in het menu **Foutopsporing.**

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Gebruik het gecodeerde wachtwoord samen met uw gebruikersnaam en opslagplaatslocatie om de git-opdracht te construeren.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Zodra de repository is gekloond, u bekijken en ermee werken in uw lokale bestandssysteem. Zie [Naslaginformatie over bestands- en mapstructuur van de lokale Git-opslagplaats](#file-and-folder-structure-reference-of-local-git-repository)voor meer informatie.

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Uw lokale opslagplaats bijwerken met de meest recente configuratie van service-instantie

Als u wijzigingen aanbrengt in uw API Management-serviceinstantie in de Azure-portal of de REST API gebruikt, moet u deze wijzigingen opslaan in de opslagplaats voordat u uw lokale opslagplaats bijwerken met de laatste wijzigingen. Klik hiervoor op **Configuratie opslaan om te repository** op het tabblad **Configuratieopslagplaats** in de Azure-portal en geef vervolgens de volgende opdracht uit in uw lokale opslagplaats.

```
git pull
```

Voordat `git pull` u deze uitvoert, moet u ervoor zorgen dat u zich in de map voor uw lokale opslagplaats bevindt. Als u de `git clone` opdracht net hebt voltooid, moet u de map in uw repo wijzigen door een opdracht als volgt uit te voeren.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Wijzigingen van uw lokale repo naar de serverrepo pushen
Als u wijzigingen van uw lokale repository naar de serverrepository wilt pushen, moet u uw wijzigingen vastleggen en deze vervolgens naar de serverrepository pushen. Als u uw wijzigingen wilt vastleggen, opent u uw Git-opdrachtgereedschap, schakelt u over naar de map van uw lokale opslagplaats en geeft u de volgende opdrachten uit.

```
git add --all
git commit -m "Description of your changes"
```

Voer de volgende opdracht uit om alle commits naar de server te pushen.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Wijzigingen in de serviceconfiguratie implementeren in het api-beheerserviceexemplaar

Zodra uw lokale wijzigingen zijn vastgelegd en naar de serverrepository zijn gepusht, u deze implementeren in uw API Management-serviceinstantie.

Zie [Git-wijzigingen implementeren in de configuratiedatabase met behulp van de REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/tenantconfiguration)voor informatie over het uitvoeren van deze bewerking met de REST API.

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Verwijzing naar bestands- en mapstructuur van de lokale Git-opslagplaats

De bestanden en mappen in de lokale git-opslagplaats bevatten de configuratie-informatie over de service-instantie.

| Item | Beschrijving |
| --- | --- |
| map root-api-beheer |Bevat configuratie op het hoogste niveau voor de service-instantie |
| apismap |Bevat de configuratie voor de api's in de service-instantie |
| groepenmap |Bevat de configuratie voor de groepen in de service-instantie |
| beleidsmap |Bevat het beleid in de service-instantie |
| portalStyles- map |Bevat de configuratie voor de aanpassingen van de ontwikkelaarsportal in de service-instantie |
| productmap |Bevat de configuratie voor de producten in de service-instantie |
| map sjablonen |Bevat de configuratie voor de e-mailsjablonen in de service-instantie |

Elke map kan een of meer bestanden bevatten, en in sommige gevallen een of meer mappen, bijvoorbeeld een map voor elke API, product of groep. De bestanden in elke map zijn specifiek voor het entiteitstype dat wordt beschreven door de mapnaam.

| Bestandstype | Doel |
| --- | --- |
| json |Configuratie-informatie over de desbetreffende entiteit |
| html |Beschrijvingen over de entiteit, vaak weergegeven in de ontwikkelaarsportal |
| xml |Beleidsinstructies |
| css |Stijlbladen voor het aanpassen van ontwikkelaarsportalen |

Deze bestanden kunnen worden gemaakt, verwijderd, bewerkt en beheerd op uw lokale bestandssysteem en de wijzigingen die zijn geïmplementeerd in uw API Management-serviceinstantie.

> [!NOTE]
> De volgende entiteiten zijn niet opgenomen in de Git-repository en kunnen niet worden geconfigureerd met Git.
>
> * [Gebruikers](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user)
> * [Abonnementen](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription)
> * [Benoemde waarden](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/property)
> * Andere entiteiten voor ontwikkelaarsportalen dan stijlen
>

### <a name="root-api-management-folder"></a>Map voor api-beheer van root's
De `api-management` hoofdmap `configuration.json` bevat een bestand dat informatie op het hoogste niveau bevat over de service-instantie in de volgende indeling.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

De eerste vier`RegistrationEnabled` `UserRegistrationTerms`instellingen `UserRegistrationTermsEnabled`( `UserRegistrationTermsConsentRequired`, , en ) worden toegewezen aan de volgende instellingen op het tabblad **Identiteiten** in de sectie **Beveiliging.**

| Identiteitsinstelling | Kaarten aan |
| --- | --- |
| RegistrationEnabled |Aanwezigheid van **gebruikersnaam en wachtwoord** identiteitsprovider |
| Gebruikersregistratievoorwaarden |**Gebruiksvoorwaarden op** het tekstvak voor gebruikersaanmelding |
| Gebruikersregistratievoorwaarden ingeschakeld |**Gebruiksvoorwaarden weergeven op aanmeldingspagina** |
| Toestemming voor gebruikersregistratievereist |**Het selectievakje Toestemming vereisen** |
| VereisenUserSigninEnabled |**Anonieme gebruikers omleiden naar aanmeldingspagina** |

De volgende vier`DelegationEnabled` `DelegationUrl`instellingen `DelegatedSubscriptionEnabled`( `DelegationValidationKey`, , en ) worden toegewezen aan de volgende instellingen op het tabblad **Delegatie** in de sectie **Beveiliging.**

| Delegatieinstelling | Kaarten aan |
| --- | --- |
| DelegatieIngeschakeld |**Aanmelden voor de leger & aanmeldingsselectievakje** |
| Deurl van de delegatie |**Tekstvak voor het eindpunt van de leger** |
| GedelegeerdAbonnementingeschakeld |**Selectievakje Productabonnement delegeren** |
| Devalidatiesleutel voor delegatie |**Tekstvak validatiesleutel delegeren** |

De uiteindelijke `$ref-policy`instelling wordt toegewezen aan het bestand met globale beleidsoverzichten voor de serviceinstantie.

### <a name="apis-folder"></a>apismap
De `apis` map bevat een map voor elke API in de service-instantie, die de volgende items bevat.

* `apis\<api name>\configuration.json`- dit is de configuratie voor de API en bevat informatie over de URL van de backendservice en de bewerkingen. Dit is dezelfde informatie die zou worden geretourneerd als u `export=true` `application/json` een [specifieke API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apis/get) met in formaat zou aanroepen.
* `apis\<api name>\api.description.html`- dit is de beschrijving van de `description` API en komt overeen met de eigenschap van de [API-entiteit](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.entityproperty).
* `apis\<api name>\operations\`- deze `<operation name>.description.html` map bevat bestanden die worden toegewezen aan de bewerkingen in de API. Elk bestand bevat de beschrijving van één bewerking in `description` de API, die wordt toegewezen aan de eigenschap van de [bewerkingsentiteit](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) in de REST-API.

### <a name="groups-folder"></a>groepenmap
De `groups` map bevat een map voor elke groep die is gedefinieerd in de serviceinstantie.

* `groups\<group name>\configuration.json`- dit is de configuratie voor de groep. Dit is dezelfde informatie die zou worden geretourneerd als u de bewerking [Een specifieke groepsbewerking bellen.](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/group/get)
* `groups\<group name>\description.html`- dit is de beschrijving van de `description` groep en komt overeen met het eigendom van de [groepsentiteit](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>beleidsmap
De `policies` map bevat de beleidsinstructies voor uw service-exemplaar.

* `policies\global.xml`- bevat beleid dat is gedefinieerd op basis van globale scope voor uw service-instantie.
* `policies\apis\<api name>\`- als u beleid hebt dat is gedefinieerd op API-bereik, worden deze in deze map opgenomen.
* `policies\apis\<api name>\<operation name>\`map - als u beleid hebt gedefinieerd op het werkingsgebied, worden deze in deze map opgenomen in `<operation name>.xml` bestanden die worden toegewezen aan de beleidsinstructies voor elke bewerking.
* `policies\products\`- als u beleid hebt dat is gedefinieerd op basis van `<product name>.xml` de productscope, worden deze in deze map opgenomen, die bestanden bevat die worden toegewezen aan de beleidsoverzichten voor elk product.

### <a name="portalstyles-folder"></a>portalStyles- map
De `portalStyles` map bevat configuratie- en stijlbladen voor aanpassing van ontwikkelaarsportalen voor de serviceinstantie.

* `portalStyles\configuration.json`- de namen bevat van de stijlbladen die door de ontwikkelaarsportal worden gebruikt
* `portalStyles\<style name>.css`- `<style name>.css` elk bestand bevat stijlen`Preview.css` voor `Production.css` de ontwikkelaarsportal (en standaard).

### <a name="products-folder"></a>productmap
De `products` map bevat een map voor elk product dat is gedefinieerd in de serviceinstantie.

* `products\<product name>\configuration.json`- dit is de configuratie voor het product. Dit is dezelfde informatie die zou worden geretourneerd als u de bewerking Een specifiek product gebruiken voor [het uitvoeren van een specifiek product](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/product/get) zou bellen.
* `products\<product name>\product.description.html`- dit is de beschrijving van het `description` product en komt overeen met de eigenschap van de [productentiteit](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) in de REST API.

### <a name="templates"></a>sjablonen
De `templates` map bevat configuratie voor de [e-mailsjablonen](api-management-howto-configure-notifications.md) van de service-instantie.

* `<template name>\configuration.json`- dit is de configuratie voor de e-mailsjabloon.
* `<template name>\body.html`- dit is de hoofdtekst van de e-mailsjabloon.

## <a name="next-steps"></a>Volgende stappen
Zie voor informatie over andere manieren om uw service-instantie te beheren:

* Uw serviceinstantie beheren met de volgende PowerShell-cmdlets
  * [Referentiemateriaal voor PowerShell-cmdlets voor service-implementatie](https://docs.microsoft.com/powershell/module/wds)
  * [Referentiemateriaal voor PowerShell-cmdlets voor servicebeheer](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Uw serviceinstantie beheren met de REST API
  * [API-api-verwijzing API-api voor API-beheer](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




