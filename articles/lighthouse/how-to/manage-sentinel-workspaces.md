---
title: Azure Sentinel-werk ruimten op schaal beheren
description: Meer informatie over het effectief beheren van Azure Sentinel op gedelegeerde klant resources.
ms.date: 06/17/2020
ms.topic: how-to
ms.openlocfilehash: bca5b6fdc84fa5a7a5553fe64c0218c5f0b44aa6
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85269017"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Azure Sentinel-werk ruimten op schaal beheren

Als service provider hebt u mogelijk meerdere tenants voor klanten voor het beheer van gedelegeerde resources voor Azure voor bereid. Met Azure Lighthouse kunnen service providers bewerkingen tegelijk uitvoeren in meerdere Azure Active Directory (Azure AD)-tenants, waardoor beheer taken efficiënter worden.

Azure Sentinel levert beveiligings analyses en bedreigings informatie, waardoor er één oplossing is voor waarschuwings detectie, zicht baarheid van bedreigingen, proactieve jacht en reactie op bedreigingen. Met Azure Lighthouse kunt u meerdere Azure-Sentinel-werk ruimten beheren op verschillende tenants op schaal. Dit maakt scenario's mogelijk, zoals het uitvoeren van query's op meerdere werk ruimten of het maken van werkmappen voor het visualiseren en bewaken van gegevens van uw verbonden gegevens bronnen om inzicht te krijgen. IP, zoals query's en playbooks blijven aanwezig in uw beheer Tenant, maar kunnen worden gebruikt voor het uitvoeren van beveiligings beheer in de tenants van de klant.

Dit onderwerp bevat een overzicht van hoe u [Azure Sentinel](../../sentinel/overview.md) kunt gebruiken op schaal bare wijze voor de zicht baarheid van meerdere tenants en beheerde beveiligings Services.

## <a name="architectural-considerations"></a>Overwegingen voor architectuur

Voor een Managed Security service provider (MSSP) die een Security-as-a-service-aanbieding wil maken met behulp van Azure Sentinel, kan een enkel Security Operations Center (SOC) nodig zijn om meerdere Azure Sentinel-werk ruimten die zijn geïmplementeerd in afzonderlijke tenants van de klant, centraal te bewaken, beheren en configureren. Op dezelfde manier kunnen ondernemingen met meerdere Azure AD-tenants meerdere Azure-Sentinel-werk ruimten die worden geïmplementeerd via hun tenants centraal beheren.

Dit gecentraliseerde implementatie model biedt de volgende voor delen:

- Het eigendom van de gegevens blijft bij elke beheerde Tenant.
- Biedt ondersteuning voor vereisten voor het opslaan van gegevens binnen geografische grenzen.
- Zorgt voor gegevens isolatie, omdat gegevens voor meerdere klanten niet in dezelfde werk ruimte worden opgeslagen. 
- Hiermee wordt voor komen dat gegevens worden exfiltration uit de beheerde tenants, zodat de gegevens kunnen worden nageleefd.
- Gerelateerde kosten worden in rekening gebracht voor elke beheerde Tenant, in plaats van naar de beheer Tenant.
- Gegevens van alle gegevens bronnen en gegevens connectors die zijn geïntegreerd met Azure Sentinel (zoals Azure AD-activiteiten logboeken, Office 365-Logboeken of waarschuwingen van micro soft Threat Protection) blijven binnen elke Tenant van de klant.
- De netwerk latentie wordt verminderd.
- Eenvoudig om nieuwe dochter ondernemingen of klanten toe te voegen of te verwijderen.

## <a name="granular-role-based-access-control-rbac"></a>Gedetailleerd toegangs beheer op basis van rollen (RBAC)

Elk klant abonnement dat door een MSSP wordt beheerd, moet [onboardd zijn voor Azure delegated resource management](onboard-customer.md). Hierdoor kunnen toegewezen gebruikers in de Tenant beheren toegang hebben tot en beheer bewerkingen uitvoeren op Azure Sentinel-werk ruimten die zijn geïmplementeerd in de tenants van de klant.

Wanneer u een autorisatie maakt, kunt u de ingebouwde Azure Sentinel-rollen toewijzen aan gebruikers, groepen of service-principals in uw beheer Tenant:

- [Azure Sentinel Reader](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel responder](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel-bijdrager](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Het is ook mogelijk dat u aanvullende ingebouwde rollen wilt toewijzen om extra functies uit te voeren. Zie [machtigingen in azure Sentinel](../../sentinel/roles.md)voor informatie over specifieke rollen die kunnen worden gebruikt met Azure Sentinel.

Zodra u uw klanten hebt voor bereid, kunnen aangewezen gebruikers zich aanmelden bij uw Tenant voor beheer en [rechtstreeks toegang krijgen tot de Azure Sentinel-werk ruimte van de klant](../../sentinel/multiple-tenants-service-providers.md) met de rollen die zijn toegewezen.

## <a name="view-and-manage-incidents-across-workspaces"></a>Incidenten in werk ruimten weer geven en beheren

Als u Azure-Sentinel-resources voor meerdere klanten beheert, kunt u incidenten in meerdere werk ruimten in één keer weer geven en beheren. Zie voor meer informatie [werken met incidenten in veel werk ruimten tegelijk](../../sentinel/multiple-workspace-view.md) en [Breid Azure Sentinel uit in werk ruimten en tenants](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Zorg ervoor dat aan de gebruikers in uw Tenant voor beheer Lees-en schrijf machtigingen zijn toegewezen voor alle werk ruimten die worden beheerd. Als een gebruiker alleen lees machtigingen heeft voor sommige werk ruimten, kunnen er waarschuwings berichten worden weer gegeven bij het selecteren van incidenten in deze werk ruimten, en kan de gebruiker die incidenten niet wijzigen of anderen die u hebt geselecteerd met die namen (zelfs als u wel machtigingen hebt voor de andere).

## <a name="configure-playbooks-for-mitigation"></a>Playbooks voor risico beperking configureren

[Playbooks](../../sentinel/tutorial-respond-threats-playbook.md) kan worden gebruikt voor automatische risico beperking wanneer een waarschuwing wordt geactiveerd. Deze playbooks kunnen hand matig worden uitgevoerd, maar ze kunnen ook automatisch worden uitgevoerd wanneer specifieke waarschuwingen worden geactiveerd. De playbooks kan worden geïmplementeerd in de Tenant beheren of de Tenant van de klant, met de antwoord procedures die zijn geconfigureerd op basis waarvan de gebruikers van de Tenant acties moeten ondernemen als reactie op een beveiligings risico.

## <a name="create-cross-tenant-workbooks"></a>Werkmappen met meerdere tenants maken

Met [Azure monitor werkmappen in azure Sentinel](../../sentinel/overview.md#workbooks) kunt u gegevens van uw verbonden gegevens bronnen visualiseren en bewaken om inzicht te krijgen. U kunt de ingebouwde werkmap sjablonen gebruiken in azure Sentinel of aangepaste werkmappen maken voor uw scenario's.

U kunt werkmappen implementeren in uw Tenant beheren en op schaal Dash boards maken om gegevens te bewaken en er query's op uit te zoeken tussen de tenants van de klant. Zie [controle op meerdere werk ruimten](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks)voor meer informatie. Houd er rekening mee dat sommige mogelijkheden [niet in meerdere werk ruimten worden ondersteund](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

U kunt werkmappen ook rechtstreeks implementeren in een afzonderlijke Tenant die u beheert voor scenario's die specifiek zijn voor die klant.

## <a name="run-queries-across-azure-sentinel-workspaces"></a>Query's uitvoeren in de onderverklikker-werk ruimten van Azure

U kunt Log Analytics query's voor bedreigingen detectie centraal maken en opslaan in de beheer Tenant. Deze query's kunnen vervolgens worden uitgevoerd in al uw Azure Sentinel-werk ruimten van uw klanten met behulp van de operator Union en de werk ruimte ()-expressie. Zie [query's in meerdere werk ruimten](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying)voor meer informatie.

## <a name="use-automation-for-cross-workspace-management"></a>Automatisering gebruiken voor beheer van meerdere werk ruimten

U kunt Automation gebruiken voor het beheren van meerdere Azure Sentinel-werk ruimten en het configureren van [jacht-query's](../../sentinel/hunting.md), playbooks en werkmappen. Zie [beheer op meerdere werk ruimten met Automation](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation)voor meer informatie.

Houd er rekening mee dat sommige mogelijkheden [momenteel niet worden ondersteund in meerdere werk ruimten](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Sentinel](../../sentinel/overview.md).
- Bekijk de [pagina met prijzen voor Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Meer informatie over [beheerervaring in meerdere tenants](../concepts/cross-tenant-management-experience.md).

