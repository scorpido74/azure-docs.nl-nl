---
title: Algemene waarschuwingen en oplossingen in Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het oplossen van algemene waarschuwingen die zijn gegenereerd als onderdeel van de status status voor Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c83caf31e25ae2212ed120e77e017ac3849898e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612909"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Bekende problemen: veelvoorkomende waarschuwingen en oplossingen in Azure Active Directory Domain Services

Als centraal onderdeel van identiteit en verificatie voor toepassingen heeft Azure Active Directory Domain Services (Azure AD DS) soms problemen. Als u problemen ondervindt, zijn er enkele veelvoorkomende waarschuwingen en bijbehorende stappen voor het oplossen van problemen om u te helpen de zaken weer te laten werken. U op elk gewenst moment ook [een Azure-ondersteuningsaanvraag openen][azure-support] voor extra hulp bij het oplossen van problemen.

In dit artikel vindt u informatie over probleemoplossing voor veelvoorkomende waarschuwingen in Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Ontbrekende map

### <a name="alert-message"></a>Waarschuwingsbericht

*De Azure AD-map die is gekoppeld aan uw beheerde domein is mogelijk verwijderd. Het beheerde domein bevindt zich niet langer in een ondersteunde configuratie. Microsoft kan uw beheerde domein niet controleren, beheren, patchen en synchroniseren.*

### <a name="resolution"></a>Oplossing

Deze fout wordt meestal veroorzaakt wanneer een Azure-abonnement wordt verplaatst naar een nieuwe Azure AD-map en de oude Azure AD-map die is gekoppeld aan Azure AD DS wordt verwijderd.

Deze fout is onherstelbaar. Als u de waarschuwing wilt oplossen, [verwijdert u uw bestaande door Azure AD DS beheerde domein](delete-aadds.md) en maakt u deze opnieuw in uw nieuwe map. Als u problemen ondervindt bij het verwijderen van het beheerde Azure AD DS-domein, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C wordt uitgevoerd in deze map

### <a name="alert-message"></a>Waarschuwingsbericht

*Azure AD Domain Services kan niet worden ingeschakeld in een Azure AD B2C-map.*

### <a name="resolution"></a>Oplossing

Azure AD DS synchroniseert automatisch met een Azure AD-map. Als de Azure AD-map is geconfigureerd voor B2C, kan Azure AD DS niet worden geïmplementeerd en gesynchroniseerd.

Als u Azure AD DS wilt gebruiken, moet u uw beheerde domein opnieuw maken in een niet-Azure AD B2C-map met de volgende stappen:

1. [Verwijder het door Azure AD DS beheerde domein](delete-aadds.md) uit uw bestaande Azure AD-map.
1. Maak een nieuwe Azure AD-map die geen Azure AD B2C-map is.
1. [Maak een vervangend door Azure AD DS beheerd domein](tutorial-create-instance.md).

De status van het Azure AD DS-domein wordt automatisch binnen twee uur bijgewerkt en verwijdert de waarschuwing.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adres bevindt zich in een openbaar IP-bereik

### <a name="alert-message"></a>Waarschuwingsbericht

*Het IP-adresbereik voor het virtuele netwerk waarin u Azure AD Domain Services hebt ingeschakeld, bevindt zich in een openbaar IP-bereik. Azure AD Domain Services moeten zijn ingeschakeld in een virtueel netwerk met een privé-IP-adresbereik. Deze configuratie heeft invloed op de mogelijkheid van Microsoft om uw beheerde domein te controleren, beheren, patchen en synchroniseren.*

### <a name="resolution"></a>Oplossing

Voordat u begint, moet u [privé-IP v4-adresruimten](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)begrijpen.

Binnen een virtueel netwerk kunnen VM's aanvragen indienen bij Azure-resources in hetzelfde IP-adresbereik als geconfigureerd voor het subnet. Als u een openbaar IP-adresbereik configureert voor een subnet, bereiken aanvragen die binnen een virtueel netwerk worden doorgestuurd mogelijk niet de beoogde webbronnen. Deze configuratie kan leiden tot onvoorspelbare fouten met Azure AD DS.

> [!NOTE]
> Als u eigenaar bent van het IP-adresbereik in het internet dat is geconfigureerd in uw virtuele netwerk, kan deze waarschuwing worden genegeerd. Azure AD Domain Services kan zich echter niet verbinden tot de [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] met deze configuratie, omdat dit kan leiden tot onvoorspelbare fouten.

Als u deze waarschuwing wilt oplossen, verwijdert u uw bestaande door Azure AD DS beheerde domein en maakt u het opnieuw in een virtueel netwerk met een privé-IP-adresbereik. Dit proces is storend omdat het door Azure AD DS beheerde domein niet beschikbaar is en alle aangepaste resources die u hebt gemaakt, zoals gegevens of serviceaccounts, verloren gaan.

1. [Verwijder het door Azure AD DS beheerde domein](delete-aadds.md) uit uw map.
1. Als u het IP-adresbereik van het virtuele netwerk wilt bijwerken, zoekt en selecteert u *Virtueel netwerk* in de Azure-portal. Selecteer het virtuele netwerk voor Azure AD DS dat ten onrechte een openbare IP-adresbereikset heeft.
1. Selecteer **onder Instellingen** *adresruimte*.
1. Werk het adresbereik bij door het bestaande adresbereik te kiezen en het te bewerken of een extra adresbereik toe te voegen. Zorg ervoor dat het nieuwe IP-adresbereik zich in een privé-IP-bereik bevindt. Wanneer u klaar bent, **slaat u** de wijzigingen op.
1. Selecteer **Subnetten** in de linkernavigatie.
1. Kies het subnet dat u wilt bewerken of maak een extra subnet.
1. Werk een privé-IP-adresbereik bij of sla uw wijzigingen **op.**
1. [Maak een vervangend door Azure AD DS beheerd domein](tutorial-create-instance.md). Zorg ervoor dat u het bijgewerkte virtuele netwerksubnet met een privé-IP-adresbereik kiest.

De status van het Azure AD DS-domein wordt automatisch binnen twee uur bijgewerkt en verwijdert de waarschuwing.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Uw Azure-abonnement wordt niet gevonden

### <a name="alert-message"></a>Waarschuwingsbericht

*Uw Azure-abonnement dat is gekoppeld aan uw beheerde domein is verwijderd.  Azure AD Domain Services vereist een actief abonnement om goed te kunnen blijven functioneren.*

### <a name="resolution"></a>Oplossing

Azure AD DS vereist een actief abonnement en kan niet worden verplaatst naar een ander abonnement. Als het Azure AD DS-beheerde domein is verwijderd, moet u een Azure-abonnement en een door Azure AD DS beheerd domein opnieuw maken.

1. [Maak een Azure-abonnement](../cost-management-billing/manage/create-subscription.md).
1. [Verwijder het door Azure AD DS beheerde domein](delete-aadds.md) uit uw bestaande Azure AD-map.
1. [Maak een vervangend door Azure AD DS beheerd domein](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Uw Azure-abonnement is uitgeschakeld

### <a name="alert-message"></a>Waarschuwingsbericht

*Uw Azure-abonnement dat is gekoppeld aan uw beheerde domein is niet actief.  Azure AD Domain Services vereist een actief abonnement om goed te kunnen blijven functioneren.*

### <a name="resolution"></a>Oplossing

Azure AD DS vereist een actief abonnement. Als het Azure AD DS-beheerde domein niet actief is, moet u het abonnement verlengen om het abonnement opnieuw te activeren.

1. [Uw Azure-abonnement verlengen](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Zodra het abonnement is verlengd, u met een AD DS-melding van Azure het beheerde domein opnieuw inschakelen.

Wanneer het beheerde domein opnieuw is ingeschakeld, wordt de status van het Azure AD DS-domein binnen twee uur automatisch bijgewerkt en wordt de waarschuwing verwijderd.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Abonnement verplaatst mappen

### <a name="alert-message"></a>Waarschuwingsbericht

*Het abonnement dat wordt gebruikt door Azure AD Domain Services is verplaatst naar een andere map. Azure AD Domain Services moet een actief abonnement in dezelfde map hebben om goed te kunnen functioneren.*

### <a name="resolution"></a>Oplossing

Azure AD DS vereist een actief abonnement en kan niet worden verplaatst naar een ander abonnement. Als het Azure AD DS-beheerde domein is verplaatst naar de vorige map, of [uw beheerde domein](delete-aadds.md) uit de bestaande map verwijdert en [een vervangend Azure AD DS-beheerd domein in het gekozen abonnement maakt.](tutorial-create-instance.md)

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Bronnen voor uw beheerde domein kunnen niet worden gevonden

### <a name="alert-message"></a>Waarschuwingsbericht

*Een bron die wordt gebruikt voor uw beheerde domein is verwijderd. Deze bron is nodig om Azure AD Domain Services goed te laten functioneren.*

### <a name="resolution"></a>Oplossing

Azure AD DS maakt extra resources om goed te functioneren, zoals openbare IP-adressen, virtuele netwerkinterfaces en een load balancer. Als een van deze bronnen wordt verwijderd, bevindt het beheerde domein zich in een niet-ondersteunde status en voorkomt het beheer van het domein. Zie [Netwerkbronnen die worden gebruikt door Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds)voor meer informatie over deze bronnen.

Deze waarschuwing wordt gegenereerd wanneer een van deze vereiste resources wordt verwijderd. Als de bron minder dan 4 uur geleden is verwijderd, bestaat de kans dat het Azure-platform de verwijderde bron automatisch opnieuw kan maken. In de volgende stappen wordt beschreven hoe u de status status en tijdstempel controleert op het verwijderen van bronnen:

1. Zoek en selecteer **domeinservices**in de Azure-portal. Kies uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer **Gezondheid**in de navigatie aan de linkerkant .
1. Selecteer op de pagina Status de waarschuwing met de ID *AADDS109*.
1. De waarschuwing heeft een tijdstempel voor wanneer deze voor het eerst werd gevonden. Als die tijdstempel minder dan 4 uur geleden is, kan het Azure-platform de bron mogelijk automatisch opnieuw maken en de waarschuwing zelf oplossen.

    Als de waarschuwing meer dan 4 uur oud is, bevindt het beheerde Azure AD DS-domein zich in een niet-herstelbare status. [Verwijder het beheerde Azure AD DS-domein](delete-aadds.md) en maak vervolgens [een vervangend beheerd domein.](tutorial-create-instance.md)

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Het subnet dat aan uw beheerde domein is gekoppeld, is volledig

### <a name="alert-message"></a>Waarschuwingsbericht

*Het subnet dat is geselecteerd voor de implementatie van Azure AD Domain Services is vol en heeft geen ruimte voor de extra domeincontroller die moet worden gemaakt.*

### <a name="resolution"></a>Oplossing

Het virtuele netwerksubnet voor Azure AD DS heeft voldoende IP-adressen nodig voor de automatisch gemaakte resources. Deze IP-adresruimte bevat de noodzaak om vervangende resources te maken als er een onderhoudsgebeurtenis is. Als u het risico wilt minimaliseren dat er geen beschikbare IP-adressen meer beschikbaar zijn, implementeert u geen extra bronnen, zoals uw eigen VM's, in hetzelfde virtuele netwerksubnet als Azure AD DS.

Deze fout is onherstelbaar. Als u de waarschuwing wilt oplossen, [verwijdert u uw bestaande door Azure AD DS beheerde domein](delete-aadds.md) en maakt u deze opnieuw. Als u problemen ondervindt bij het verwijderen van het beheerde Azure AD DS-domein, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Serviceprincipal ongeautoriseerd

### <a name="alert-message"></a>Waarschuwingsbericht

*Een serviceprincipal die Azure AD Domain Services gebruikt om uw domein te onderhouden, is niet geautoriseerd om resources op het Azure-abonnement te beheren. De serviceprincipal moet machtigingen krijgen om uw beheerde domein te onderhouden.*

### <a name="resolution"></a>Oplossing

Sommige automatisch gegenereerde serviceprincipals worden gebruikt voor het beheren en maken van resources voor een door Azure AD DS beheerd domein. Als de toegangsmachtigingen voor een van deze serviceprincipals worden gewijzigd, kan het domein resources niet correct beheren. In de volgende stappen ziet u hoe u toegangsmachtigingen begrijpen en vervolgens toegangsmachtigingen verlenen aan een serviceprincipal:

1. Lees meer over [toegangsbeheer op basis van rollen en hoe u toegang verleent tot toepassingen in de Azure-portal.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Bekijk de toegang die de serviceprincipal met de ID *abba844e-bc0e-44b0-947a-dc74e5d09022* heeft en geef de toegang die op een eerdere datum werd geweigerd.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Te weinig IP-adres in het beheerde domein

### <a name="alert-message"></a>Waarschuwingsbericht

*We hebben vastgesteld dat het subnet van het virtuele netwerk in dit domein mogelijk niet genoeg IP-adressen heeft. Azure AD Domain Services heeft ten minste twee beschikbare IP-adressen nodig in het subnet waarin het is ingeschakeld. We raden u aan om ten minste 3-5 extra IP-adressen in het subnet te hebben. Dit kan zijn opgetreden als andere virtuele machines binnen het subnet worden geïmplementeerd, waardoor het aantal beschikbare IP-adressen wordt uitgeput of als er een beperking is op het aantal beschikbare IP-adressen in het subnet.*

### <a name="resolution"></a>Oplossing

Het virtuele netwerksubnet voor Azure AD DS heeft voldoende IP-adressen nodig voor de automatisch gemaakte resources. Deze IP-adresruimte bevat de noodzaak om vervangende resources te maken als er een onderhoudsgebeurtenis is. Als u het risico wilt minimaliseren dat er geen beschikbare IP-adressen meer beschikbaar zijn, implementeert u geen extra bronnen, zoals uw eigen VM's, in hetzelfde virtuele netwerksubnet als Azure AD DS.

Als u deze waarschuwing wilt oplossen, verwijdert u uw bestaande door Azure AD DS beheerde domein en maakt u deze opnieuw in een virtueel netwerk met een groot genoeg IP-adresbereik. Dit proces is storend omdat het door Azure AD DS beheerde domein niet beschikbaar is en alle aangepaste resources die u hebt gemaakt, zoals gegevens of serviceaccounts, verloren gaan.

1. [Verwijder het door Azure AD DS beheerde domein](delete-aadds.md) uit uw map.
1. Als u het IP-adresbereik van het virtuele netwerk wilt bijwerken, zoekt en selecteert u *Virtueel netwerk* in de Azure-portal. Selecteer het virtuele netwerk voor Azure AD DS met het kleine IP-adresbereik.
1. Selecteer **onder Instellingen** *adresruimte*.
1. Werk het adresbereik bij door het bestaande adresbereik te kiezen en het te bewerken of een extra adresbereik toe te voegen. Controleer of het nieuwe IP-adresbereik groot genoeg is voor het subnetbereik van Azure AD DS. Wanneer u klaar bent, **slaat u** de wijzigingen op.
1. Selecteer **Subnetten** in de linkernavigatie.
1. Kies het subnet dat u wilt bewerken of maak een extra subnet.
1. Werk een groot ip-adresbereik bij of geef **deze op en sla** de wijzigingen op.
1. [Maak een vervangend door Azure AD DS beheerd domein](tutorial-create-instance.md). Zorg ervoor dat u het bijgewerkte virtuele netwerksubnet kiest met een groot genoeg IP-adresbereik.

De status van het Azure AD DS-domein wordt automatisch binnen twee uur bijgewerkt en verwijdert de waarschuwing.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Resources kunnen niet worden hersteld

### <a name="alert-message"></a>Waarschuwingsbericht

*De resources die worden gebruikt door Azure AD Domain Services zijn in een onverwachte toestand gedetecteerd en kunnen niet worden hersteld.*

### <a name="resolution"></a>Oplossing

Deze fout is onherstelbaar. Als u de waarschuwing wilt oplossen, [verwijdert u uw bestaande door Azure AD DS beheerde domein](delete-aadds.md) en maakt u deze opnieuw. Als u problemen ondervindt bij het verwijderen van het beheerde Azure AD DS-domein, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Subnet ongeldig

### <a name="alert-message"></a>Waarschuwingsbericht

*Het subnet dat is geselecteerd voor implementatie van Azure AD Domain Services is ongeldig en kan niet worden gebruikt.*

### <a name="resolution"></a>Oplossing

Deze fout is onherstelbaar. Als u de waarschuwing wilt oplossen, [verwijdert u uw bestaande door Azure AD DS beheerde domein](delete-aadds.md) en maakt u deze opnieuw. Als u problemen ondervindt bij het verwijderen van het beheerde Azure AD DS-domein, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Resources zijn vergrendeld

### <a name="alert-message"></a>Waarschuwingsbericht

*Een of meer van de netwerkbronnen die door het beheerde domein worden gebruikt, kunnen niet worden gebruikt omdat de doelscope is vergrendeld.*

### <a name="resolution"></a>Oplossing

Resourcevergrendelingen kunnen worden toegepast op Azure-bronnen om wijziging of verwijdering te voorkomen. Aangezien Azure AD DS een beheerde service is, heeft het Azure-platform de mogelijkheid nodig om configuratiewijzigingen aan te brengen. Als een resourcevergrendeling wordt toegepast op sommige azure AD DS-componenten, kan het Azure-platform zijn beheertaken niet uitvoeren.

Voer de volgende stappen uit om te controleren of bronvergrendelingen op de Azure AD DS-componenten worden verwijderd:

1. Controleer voor elk van de Azure AD DS-netwerkcomponenten in uw brongroep, zoals virtueel netwerk, netwerkinterface of openbaar IP-adres, de bewerkingslogboeken in de Azure-portal. Deze bewerkingslogboeken moeten aangeven waarom een bewerking mislukt en waar een resourcevergrendeling wordt toegepast.
1. Selecteer de resource waar een vergrendeling wordt toegepast en selecteer vervolgens onder **Vergrendelingen,** selecteer en verwijder de vergrendeling(en).

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Resources zijn onbruikbaar

### <a name="alert-message"></a>Waarschuwingsbericht

*Een of meer van de netwerkbronnen die door het beheerde domein worden gebruikt, kunnen niet worden gebruikt vanwege beleidsbeperkingen(en).*

### <a name="resolution"></a>Oplossing

Beleidsregels worden toegepast op Azure-resources en resourcegroepen die bepalen welke configuratieacties zijn toegestaan. Aangezien Azure AD DS een beheerde service is, heeft het Azure-platform de mogelijkheid nodig om configuratiewijzigingen aan te brengen. Als een beleid wordt toegepast op sommige Azure AD DS-componenten, kan het Azure-platform zijn beheertaken mogelijk niet uitvoeren.

Voer de volgende stappen uit om te controleren op toegepast beleid voor de Azure AD DS-componenten en deze bij te werken:

1. Controleer voor elk van de Azure AD DS-netwerkcomponenten in uw brongroep, zoals virtueel netwerk, NIC of openbaar IP-adres, de bewerkingslogboeken in de Azure-portal. Deze bewerkingslogboeken moeten aangeven waarom een bewerking mislukt en waar een restrictief beleid wordt toegepast.
1. Selecteer de resource waarop een beleid **Policies**wordt toegepast en selecteer en bewerk het beleid, zodat het minder beperkend is.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronisatie is al een tijdje niet voltooid

### <a name="alert-message"></a>Waarschuwingsbericht

*Het beheerde domein is voor het laatst gesynchroniseerd met Azure AD op [datum]. Gebruikers kunnen zich mogelijk niet aanmelden voor het beheerde domein of groepslidmaatschap, mogelijk niet synchroon met Azure AD.*

### <a name="resolution"></a>Oplossing

[Controleer de Azure AD DS-status](check-health.md) op waarschuwingen die problemen in de configuratie van het beheerde domein aangeven. Problemen met de netwerkconfiguratie kunnen de synchronisatie vanuit Azure AD blokkeren. Als u waarschuwingen oplossen die wijzen op een configuratieprobleem, wacht u twee uur en controleert u of de synchronisatie is voltooid.

De volgende veelvoorkomende redenen zorgen ervoor dat synchronisatie wordt gestopt in een door Azure AD DS beheerde domeinen:

* Vereiste netwerkconnectiviteit is geblokkeerd. Zie [netwerkbeveiligingsgroepen en](alert-nsg.md) de [netwerkvereisten voor Azure AD-domeinservices](network-considerations.md)oplossen voor meer informatie over het controleren van het virtuele Azure-netwerk op problemen en wat er nodig is.
*  Wachtwoordsynchronisatie is niet ingesteld of voltooid toen het door Azure AD DS beheerde domein werd geïmplementeerd. U wachtwoordsynchronisatie instellen voor [alleen cloudgebruikers](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) of [hybride gebruikers vanaf on-prem.](tutorial-configure-password-hash-sync.md)

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Er is al een tijdje geen back-up gemaakt

### <a name="alert-message"></a>Waarschuwingsbericht

*Er is voor het laatst een back-up van het beheerde domein gemaakt op [datum].*

### <a name="resolution"></a>Oplossing

[Controleer de Azure AD DS-status](check-health.md) op waarschuwingen die problemen in de configuratie van het beheerde domein aangeven. Problemen met de netwerkconfiguratie kunnen voorkomen dat het Azure-platform back-ups maakt. Als u waarschuwingen oplossen die wijzen op een configuratieprobleem, wacht u twee uur en controleert u of de synchronisatie is voltooid.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Opschorting wegens uitgeschakeld abonnement

### <a name="alert-message"></a>Waarschuwingsbericht

*Het beheerde domein wordt opgeschort omdat het Azure-abonnement dat aan het domein is gekoppeld, niet actief is.*

### <a name="resolution"></a>Oplossing

> [!WARNING]
> Als een door Azure AD DS beheerd domein voor een langere periode wordt opgeschort, bestaat het gevaar dat het wordt verwijderd. Los de reden voor de opschorting zo snel mogelijk op. Zie [De opgeschorte statussen voor Azure AD DS begrijpen voor](suspension.md)meer informatie.

Azure AD DS vereist een actief abonnement. Als het Azure AD DS-beheerde domein niet actief is, moet u het abonnement verlengen om het abonnement opnieuw te activeren.

1. [Uw Azure-abonnement verlengen](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Zodra het abonnement is verlengd, u met een AD DS-melding van Azure het beheerde domein opnieuw inschakelen.

Wanneer het beheerde domein opnieuw is ingeschakeld, wordt de status van het Azure AD DS-domein binnen twee uur automatisch bijgewerkt en wordt de waarschuwing verwijderd.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Opschorting wegens een ongeldige configuratie

### <a name="alert-message"></a>Waarschuwingsbericht

*Het beheerde domein wordt opgeschort vanwege een ongeldige configuratie. De service kan de domeincontrollers voor uw beheerde domein lange tijd niet beheren, patchen of bijwerken.*

### <a name="resolution"></a>Oplossing

> [!WARNING]
> Als een door Azure AD DS beheerd domein voor een langere periode wordt opgeschort, bestaat het gevaar dat het wordt verwijderd. Los de reden voor de opschorting zo snel mogelijk op. Zie [De opgeschorte statussen voor Azure AD DS begrijpen voor](suspension.md)meer informatie.

[Controleer de Azure AD DS-status](check-health.md) op waarschuwingen die problemen in de configuratie van het beheerde domein aangeven. Als u waarschuwingen oplossen die wijzen op een configuratieprobleem, wacht u twee uur en controleert u of de synchronisatie is voltooid. Wanneer u klaar bent, [opent u een Azure-ondersteuningsaanvraag][azure-support] om het beheerde Azure AD DS-beheerde domein opnieuw in te schakelen.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen hebt, [opent u een Azure-ondersteuningsaanvraag][azure-support] voor extra hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
