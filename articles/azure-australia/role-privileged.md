---
title: Access Control op basis van rollen en Privileged Identity Management in azure Australië
description: Richt lijnen voor het implementeren van rollen op basis van Access Control en Privileged Identity Management binnen de Australische regio's om te voldoen aan de specifieke vereisten van het beleid voor de Australische overheid, de regelgeving en de wetgeving.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e2a94f82e4830bd1e9c96039f5ef8fe6546b0d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982666"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>Op rollen gebaseerde Access Control (RBAC) en Privileged Identity Management (PIM)

Beheer privileges beheren is een belang rijke stap bij het garanderen van beveiliging binnen elke IT-omgeving. Het beperken van de beheer bevoegdheid via het gebruik van de minimale bevoegdheids beveiliging is een vereiste van de [ACSC-ISM](https://acsc.gov.au/infosec/ism/index.htm) en maakt deel uit van de [ACSC Essentials](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) -lijst van beveiligings aanbevelingen.

Micro soft biedt een reeks besturings elementen voor het implementeren van just-in-time en alleen-genoeg toegang binnen Microsoft Azure. Meer informatie over deze besturings elementen is essentieel voor een effectief beveiligings postuur in de Cloud. Deze hand leiding bevat een overzicht van de besturings elementen zelf en de belangrijkste aandachtspunten bij het implementeren van deze.

## <a name="role-based-access-control-rbac"></a>RBAC (op rollen gebaseerd toegangsbeheer)

Access Control op basis van rollen is centraal voor het beheer van toegang tot alle resources binnen Microsoft Azure en het beheer van Azure Active Directory (Azure AD). RBAC kan worden geïmplementeerd naast een aantal aanvullende functies die beschikbaar zijn in Azure. Dit artikel is gericht op het implementeren van effectief RBAC met behulp van Azure Beheergroepen, Azure Active Directory groepen en Azure Privileged Identity Management (PIM).

Op hoog niveau zijn voor het implementeren van RBAC drie onderdelen vereist:

![RBAC-overzicht](media/rbac-overview.png)

* **Beveiligings**-principals: Een beveiligingsprincipal kan een van de volgende zijn: een gebruiker, een groep, [service](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)-principals of een [beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Aan beveiligings-principals moeten bevoegdheden worden toegewezen met behulp van Azure Active Directory groepen.

* **Roldefinities**: Een roldefinitie, ook wel rol genoemd, is een verzameling machtigingen. Deze machtigingen definiëren de bewerkingen die kunnen worden uitgevoerd door de beveiligings-principals die zijn toegewezen aan de roldefinitie. Deze functionaliteit wordt verschaft door Azure-resource rollen en Azure Active Directory beheerders rollen. Azure wordt geleverd met een set ingebouwde rollen (koppeling) die kan worden uitgebreid met aangepaste rollen.

* **Bereik**: Het bereik is de set Azure-resources waarop een roldefinitie van toepassing is. Azure-rollen kunnen worden toegewezen aan Azure-resources met behulp van Azure Beheergroepen.

Deze drie onderdelen combi neren om beveiligings-principals de toegang die is gedefinieerd in de roldefinities te verlenen aan alle resources die onder het bereik van Azure Beheergroepen vallen. dit wordt een roltoewijzing genoemd. Er kunnen meerdere roldefinities worden toegewezen aan een beveiligingsprincipal en meerdere beveiligings-principals kunnen worden toegewezen aan één bereik.

### <a name="azure-active-directory-groups"></a>Azure Active Directory groepen

Wanneer u machtigingen toewijst aan individuen of teams, moet de toewijzing zo mogelijk worden gekoppeld aan een Azure Active Directory groep en niet rechtstreeks aan de desbetreffende gebruiker worden toegewezen. Dit is dezelfde aanbevolen procedure die wordt overgenomen van on-premises Active Directory-implementaties. Waar mogelijk Azure Active Directory groepen moeten worden gemaakt per team, in aanvulling op de logische structuur van de Azure-Beheergroepen die u hebt gemaakt.

In een hybride Cloud scenario kunnen on-premises Windows Server-Active Directory beveiligings groepen worden gesynchroniseerd met uw Azure Active Directory-exemplaar. Als u al RBAC on-premises hebt geïmplementeerd met behulp van deze Windows Server Active Directory-beveiligings groepen, kunnen deze groepen, zodra ze zijn gesynchroniseerd, worden gebruikt voor het implementeren van RBAC voor uw Azure-resources. Als dat niet het geval is, kan uw cloud omgeving worden beschouwd als een schone pastel voor het ontwerpen en implementeren van een robuust beheer plan voor bevoegdheden dat is gebouwd rond uw Azure Active Directory-implementatie.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Azure-resource rollen versus Azure Active Directory beheerders rollen

Microsoft Azure biedt een groot aantal ingebouwde rollen voor [Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) en [Azure Active Directory beheer](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Beide typen rollen bieden specifieke gedetailleerde toegang tot Azure-resources of voor Azure AD-beheerders. Het is belang rijk te weten dat Azure-resource rollen niet kunnen worden gebruikt voor het bieden van beheerders toegang tot Azure AD en Azure AD-rollen die geen specifieke toegang bieden tot Azure-resources.

Hier volgen enkele voor beelden van de toegangs typen die kunnen worden toegewezen aan een Azure-resource met behulp van een ingebouwde rol:

* Toestaan dat één gebruiker de virtuele machines in een abonnement kan beheren en een andere gebruiker de virtuele netwerken kan beheren.
* Een DBA-groep toestaan de SQL-databases in een abonnement te beheren.
* Toestaan dat een gebruiker alle resources in een resourcegroep kan beheren, zoals virtuele machines, websites en subnetten.
* Toestaan dat een toepassing toegang heeft tot alle resources in een resourcegroep.

Voor beelden van de toegangs typen die kunnen worden toegewezen aan Azure AD-beheer zijn:

* Mede werkers van helpdesk medewerkers toestaan gebruikers wachtwoorden opnieuw in te stellen
* Mede werkers toestaan externe gebruikers uit te nodigen voor een Azure AD-exemplaar voor B2B-samen werking
* Beheer personeel Lees toegang geven voor aanmelding en controle rapporten
* Personeel toestaan alle gebruikers en groepen te beheren, met inbegrip van het opnieuw instellen van wacht woorden.

Het is belang rijk dat u de volledige lijst met toegestane acties begrijpt die een ingebouwde rol heeft, om ervoor te zorgen dat er geen onnodige toegang wordt verleend. De lijst met ingebouwde rollen en de toegang die ze bieden, is voortdurend in ontwikkeling, de volledige lijst met de rollen en de bijbehorende definities kan worden weer gegeven door de bijbehorende documentatie te bekijken of door gebruik te maken van de Azure PowerShell-cmdlet:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

of de Azure CLI-opdracht:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

Het is ook mogelijk om aangepaste Azure-resource rollen te maken zoals vereist. Deze aangepaste rollen kunnen worden gemaakt in de Azure Portal, via Power shell of de Azure CLI. Bij het maken van aangepaste rollen is het essentieel om ervoor te zorgen dat het doel van de rol uniek is en dat de functie ervan niet al wordt verschaft door een bestaande Azure-resource functie. Dit reduceert de continue beheer complexiteit en vermindert het risico van beveiligings-principals die onnodige bevoegdheden ontvangen. Een voor beeld hiervan is het maken van een aangepaste Azure-resource functie die zich bevindt tussen de ingebouwde Azure-resource rollen, "Inzender voor virtuele machines" en "beheerder van de virtuele machine".

De aangepaste rol kan worden gebaseerd op de bestaande rol Inzender, waarmee de volgende toegang wordt verleend:

| Azure-resource | Toegangsniveau |
| --- | --- |
| Virtuele machines | Kan beheren, maar geen toegang tot |
| Virtual Network gekoppeld aan de VM | Geen toegang tot |
| Opslag die is gekoppeld aan de VM | Geen toegang tot |
|

Met de aangepaste rol kan deze basis toegang worden behouden, maar de aangewezen gebruikers hebben een aantal algemene bevoegdheden waarmee de netwerk configuratie van de virtuele machines kan worden gewijzigd.

Azure-resource rollen hebben ook het voor deel dat ze kunnen worden toegewezen aan resources via Azure Beheergroepen.

### <a name="azure-management-groups"></a>Azure-beheergroepen

Azure Beheergroepen kan door een organisatie worden gebruikt voor het beheren van de toewijzing van rollen aan alle abonnementen en hun resources binnen een Azure-pacht. Azure Beheergroepen zijn ontworpen zodat u beheer hiërarchieën kunt maken, waaronder de mogelijkheid om uw organisatie structuur hiërarchisch toe te wijzen binnen Azure. Het maken van organisatorische business units als afzonderlijke logische entiteiten maakt het mogelijk dat machtigingen binnen een organisatie worden toegepast op basis van de specifieke vereisten van elk team. Azure Beheergroepen kan worden gebruikt om een beheer hiërarchie te definiëren met een diepte van Maxi maal zes niveaus.

![Beheergroepen](media/management-groups.png)

Azure-Beheergroepen worden toegewezen aan Azure-abonnementen binnen een Azure-pacht. Op deze manier kan een organisatie Azure-resources die tot specifieke bedrijfs eenheden behoren, scheiden en een gedetailleerd niveau bepalen van kosten beheer en bevoegdheids toewijzing.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Micro soft heeft just-in-time (JIT) en Just-voldoende toegang (JEA) geïmplementeerd via Azure Privileged Identity Management. Met deze service kunnen beheerders bevoegde toegang tot Azure-resources beheren, beheren en controleren. Met PIM kunnen beveiligings-principals "in aanmerking komend" worden gemaakt voor een rol door administratieve mede werkers, zodat gebruikers de activering kunnen aanvragen via de Azure Portal of via Power shell-cmdlets. Standaard kan roltoewijzing worden geactiveerd voor een periode van 1 tot 72 uur. De gebruiker kan, indien nodig, een uitbrei ding aanvragen bij de roltoewijzing en de optie om de roltoewijzing permanent te maken bestaat. De vereiste voor multi-factor Authentication kan eventueel worden afgedwongen wanneer gebruikers de activering van hun in aanmerking komende rollen aanvragen. Zodra de toegewezen periode van de activering van de functie is verlopen, heeft de beveiligingsprincipal niet langer de bevoegde toegang die is verleend door de rol.

Het gebruik van PIM voor komt het aantal veelvoorkomende bevoegdheids toewijzings problemen die kunnen optreden in omgevingen die niet just-in-time-toegang gebruiken of geen routine controles van bevoegdheids toewijzing uitvoeren. Een veelvoorkomend probleem is het toewijzen van verhoogde bevoegdheden die worden verg eten en die blijvend zijn nadat de taak waarvoor verhoogde bevoegdheden zijn vereist, is voltooid. Een ander probleem is de verspreiding van verhoogde bevoegdheden binnen een omgeving door het klonen van de toegang die is toegewezen aan een beveiligingsprincipal bij het configureren van andere vergelijk bare beveiligings-principals.

## <a name="key-design-considerations"></a>Belangrijkste overwegingen bij het ontwerpen

Bij het ontwerpen van een RBAC-strategie met de bedoeling om de minimale bevoegdheids beveiliging af te dwingen, moet u rekening houden met de volgende beveiligings vereisten:

* Aanvragen voor bevoegde toegang worden gevalideerd
* Beheerders bevoegdheden zijn beperkt tot de minimale toegang die is vereist om de specifieke taken uit te voeren
* Beheerders bevoegdheden zijn beperkt tot de minimale tijds duur die nodig is om de specifieke taken uit te voeren
* Er worden regel matig beoordelingen van verleende beheerders bevoegdheden uitgevoerd

Het proces van het ontwerpen van een RBAC-strategie vereist een gedetailleerde beoordeling van bedrijfs functies om inzicht te krijgen in het verschil in de toegang tussen afzonderlijke zakelijke rollen en het type en de frequentie van het werk waarvoor verhoogde bevoegdheden zijn vereist. Het verschil in functie tussen een back-upoperator, een beveiligings beheerder en een auditor heeft verschillende toegangs niveaus op verschillende tijdstippen nodig met verschillende niveaus van doorlopende controle.

## <a name="validate-requests-for-access"></a>Aanvragen voor toegang valideren

Verhoogde bevoegdheden moeten expliciet worden goedgekeurd. Ter ondersteuning hiervan moet een goedkeurings proces worden ontwikkeld en moeten de juiste mede werkers worden uitgevoerd om te valideren dat alle aanvragen voor aanvullende bevoegdheden legitiem zijn. Privileged Identity Management biedt meerdere opties voor het goed keuren van de roltoewijzing. Een aanvraag voor het activeren van een rol kan zodanig worden geconfigureerd dat zelf goed keuring wordt toegestaan of kan worden gegatedd en vereisen dat fiatteurs hand matig alle rollen activerings aanvragen controleren en goed keuren. Activerings aanvragen kunnen ook worden geconfigureerd om aanvullende ondersteunende informatie te vereisen die is opgenomen in de activerings aanvraag, zoals ticket nummers.

### <a name="restrict-privilege-based-on-duties"></a>Bevoegdheid beperken op basis van taken

Het beperken van het Bevoegdheids niveau dat wordt verleend aan beveiligings-principals is van cruciaal belang omdat de toewijzing van bevoegdheden een veelvoorkomende aanvals vector voor IT-beveiliging is. De typen resources die worden beheerd en de teams die verantwoordelijk zijn, moeten worden beoordeeld zodat het minimale bevoegdheids niveau dat vereist is voor dagelijkse taken, kan worden toegewezen. Aanvullende bevoegdheden die verder gaan dan die welke nodig zijn voor dagelijkse taken, mogen nooit worden verleend voor de periode die nodig is om een specifieke taak uit te voeren. Een voor beeld hiervan is het verlenen van ' Inzender ' toegang tot de beheerder van een klant, maar hiermee kunnen ze "eigenaar"-machtigingen voor een Azure-resource aanvragen voor een specifieke taak waarvoor tijdelijke toegang op hoog niveau is vereist.

Dit zorgt ervoor dat elke afzonderlijke beheerder alleen verhoogde toegang heeft voor de kortste periode. In het licht van deze procedures wordt de algehele kwets baarheid voor de IT-infra structuur van organisaties verminderd.

### <a name="regular-evaluation-of-administrative-privilege"></a>Regel matige evaluatie van beheerders bevoegdheden

Het is essentieel dat beveiligings-principals in een omgeving regel matig worden gecontroleerd om ervoor te zorgen dat het juiste bevoegdheids niveau momenteel wordt toegewezen. Microsoft Azure biedt een aantal manieren om de bevoegdheden te controleren en te evalueren die zijn toegewezen aan Azure-beveiligings-principals. Privileged Identity Management staat beheerders toe om regel matig ' toegangs beoordelingen ' uit te voeren van de rollen die zijn toegekend aan beveiligings-principals. Een toegangs beoordeling kan worden uitgevoerd om zowel de toewijzing van Azure-resource functies als de toewijzing van Azure Active Directory beheerdersrol te controleren. Een toegangs beoordeling kan worden geconfigureerd met de volgende eigenschappen:

* **Naam controleren en begin-en eind datums controleren**: Beoordelingen moeten zo worden geconfigureerd dat ze lang genoeg zijn voor de aangewezen gebruikers.

* **Te controleren rol**: Elke toegangs beoordeling is gericht op één Azure-rol.

* **Aangewezen revisoren**: Er zijn drie opties voor het uitvoeren van een beoordeling. U kunt de beoordeling toewijzen aan iemand anders om deze te volt ooien. u kunt dit zelf doen, of u kunt elke gebruiker hun eigen toegang laten beoordelen.

* **Gebruikers moeten een reden opgeven voor toegang**: Gebruikers kunnen verplicht zijn om een reden op te geven voor het behoud van hun bevoegdheids niveau bij het volt ooien van de toegangs beoordeling.

De voortgang van openstaande toegangs beoordelingen kan op elk gewenst moment worden bewaakt via een dash board in de Azure Portal. De toegang tot de functie die wordt gecontroleerd, blijft ongewijzigd totdat de toegangs beoordeling is voltooid. Het is ook mogelijk om alle PIM-gebruikers toewijzingen en-activeringen binnen een bepaalde tijds periode te [controleren](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) .

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel over [systeem bewaking in azure Australië](system-monitor.md).
