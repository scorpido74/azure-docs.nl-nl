---
title: Beheerconcepten voor Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het beheren van een beheerd Azure Directory Domain Services-domein en het gedrag van gebruikersaccounts en wachtwoorden
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 682935fa2324b8de4992ab2f90c7f71e05c4f8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264230"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Beheerconcepten voor gebruikersaccounts, wachtwoorden en beheer in Azure Active Directory Domain Services

Wanneer u een beheerd beheerd Azure Active Directory Domain Services (AD DS)-domein maakt en uitvoert, zijn er enkele verschillen in gedrag in vergelijking met een traditionele on-premises AD DS-omgeving. U gebruikt dezelfde beheerhulpprogramma's in Azure AD DS als een zelfbeheerd domein, maar u hebt geen directe toegang tot de domeincontrollers (DC). Er zijn ook enkele verschillen in gedrag voor wachtwoordbeleid en wachtwoordhashes, afhankelijk van de bron van het aanmaken van het gebruikersaccount.

In dit conceptuele artikel wordt beschreven hoe u een door Azure AD DS beheerd domein en het verschillende gedrag van gebruikersaccounts beheert, afhankelijk van de manier waarop ze zijn gemaakt.

## <a name="domain-management"></a>Domeinbeheer

In Azure AD DS maken de domeincontrollers (DC's) die alle bronnen bevatten, zoals gebruikers en groepen, referenties en beleidsregels, deel uit van de beheerde service. Voor redundantie worden twee DC's gemaakt als onderdeel van een door Azure AD DS beheerd domein. U zich niet aanmelden bij deze DC's om beheertaken uit te voeren. In plaats daarvan maakt u een beheer-VM die is verbonden met het beheerde Azure AD DS-domein en installeert u vervolgens uw reguliere AD DS-beheerhulpprogramma's. U bijvoorbeeld de mmc-modules van Active Directory Administrative Center of Microsoft Management Console (MMC) gebruiken, zoals DNS- of groepsbeleidsobjecten.

## <a name="user-account-creation"></a>Het aanmaken van gebruikersaccounts

Gebruikersaccounts kunnen op meerdere manieren worden gemaakt in Azure AD DS. De meeste gebruikersaccounts worden gesynchroniseerd vanuit Azure AD, wat ook gebruikersaccount kan bevatten dat is gesynchroniseerd vanuit een on-premises AD DS-omgeving. U ook handmatig accounts rechtstreeks maken in Azure AD DS. Sommige functies, zoals de initiële wachtwoordsynchronisatie of het wachtwoordbeleid, gedragen zich anders, afhankelijk van hoe en waar gebruikersaccounts worden gemaakt.

* Het gebruikersaccount kan worden gesynchroniseerd vanuit Azure AD. Dit omvat cloud-only gebruikersaccounts die rechtstreeks in Azure AD zijn gemaakt en hybride gebruikersaccounts die zijn gesynchroniseerd vanuit een on-premises AD DS-omgeving met Azure AD Connect.
    * De meeste gebruikersaccounts in Azure AD DS worden gemaakt via het synchronisatieproces van Azure AD.
* Het gebruikersaccount kan handmatig worden gemaakt in een door Azure AD DS beheerd domein en bestaat niet in Azure AD.
    * Als u serviceaccounts moet maken voor toepassingen die alleen in Azure AD DS worden uitgevoerd, u deze handmatig maken in het beheerde domein. Aangezien synchronisatie een manier is van Azure AD, worden gebruikersaccounts die zijn gemaakt in Azure AD DS niet gesynchroniseerd met Azure AD.

## <a name="password-policy"></a>Wachtwoordbeleid

Azure AD DS bevat een standaardwachtwoordbeleid dat instellingen definieert voor zaken als accountuitsluiting, maximale wachtwoordleeftijd en wachtwoordcomplexiteit. Instellingen zoals accountuitsluitingsbeleid zijn van toepassing op alle gebruikers in Azure AD DS, ongeacht hoe de gebruiker is gemaakt zoals beschreven in de vorige sectie. Een paar instellingen, zoals minimale wachtwoordlengte en wachtwoordcomplexiteit, zijn alleen van toepassing op gebruikers die rechtstreeks in Azure AD DS zijn gemaakt.

U uw eigen aangepaste wachtwoordbeleid maken om het standaardbeleid in Azure AD DS te overschrijven. Deze aangepaste beleidsregels kunnen vervolgens worden toegepast op specifieke groepen gebruikers als dat nodig is.

Zie [Beleid voor wachtwoord- en accountuitsluiting op beheerde domeinen][password-policy]voor meer informatie over de manier waarop wachtwoordbeleid wordt toegepast, afhankelijk van de bron van het aanmaken van de gebruiker.

## <a name="password-hashes"></a>Wachtwoordhashes

Azure AD DS heeft wachtwoordhashes nodig in een indeling die geschikt is voor NT LAN Manager (NTLM) en Kerberos-verificatie om gebruikers op het beheerde domein te verifiëren. Azure AD genereert of slaat geen wachtwoordhashes op in de indeling die vereist is voor NTLM- of Kerberos-verificatie totdat u Azure AD DS inschakelt voor uw tenant. Om veiligheidsredenen slaat Azure AD ook geen wachtwoordreferenties op in formulier clear-text. Daarom kan Azure AD deze NTLM- of Kerberos-wachtwoordhashes niet automatisch genereren op basis van de bestaande referenties van gebruikers.

Voor gebruikersaccounts die alleen in de cloud zijn, moeten gebruikers hun wachtwoorden wijzigen voordat ze Azure AD DS kunnen gebruiken. Dit wachtwoordwijzigingsproces zorgt ervoor dat de wachtwoordhashes voor Kerberos- en NTLM-verificatie worden gegenereerd en opgeslagen in Azure AD.

Voor gebruikers die zijn gesynchroniseerd vanuit een on-premises AD DS-omgeving met Azure AD Connect, [kunt u synchronisatie van wachtwoordhashes inschakelen.][hybrid-phs]

> [!IMPORTANT]
> Azure AD Connect synchroniseert alleen verouderde wachtwoordhashes wanneer u Azure AD DS inschakelt voor uw Azure AD-tenant. Verouderde wachtwoordhashes worden niet gebruikt als u alleen Azure AD Connect gebruikt om een on-premises AD DS-omgeving te synchroniseren met Azure AD.
>
> Als uw oudere toepassingen geen NTLM-verificatie of LDAP-eenvoudige bindingen gebruiken, raden we u aan ntlm-wachtwoordhashsynchronisatie voor Azure AD DS uit te schakelen. Zie [Zwakke cijfersuites en NTLM-hashsynchronisatie uitschakelen][secure-domain]voor meer informatie.

Zodra de juiste gegevens zijn geconfigureerd, worden de bruikbare wachtwoordhashes opgeslagen in het beheerde Azure AD DS-domein. Als u het door Azure AD DS beheerde domein verwijdert, worden ook alle wachtwoordhashes die op dat moment zijn opgeslagen, verwijderd. Gesynchroniseerde referentiegegevens in Azure AD kunnen niet opnieuw worden gebruikt als u later een door Azure AD DS beheerd domein maakt- u moet de wachtwoordhashsynchronisatie opnieuw configureren om de wachtwoordhashes opnieuw op te slaan. Eerder met domeinen verbonden VM's of gebruikers kunnen niet onmiddellijk verifiëren - Azure AD moet de wachtwoordhashes genereren en opslaan in het nieuwe door Azure AD DS beheerde domein. Zie [Wachtwoordhashsynchronisatieproces voor Azure AD DS en Azure AD Connect voor][azure-ad-password-sync]meer informatie.

> [!IMPORTANT]
> Azure AD Connect mag alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Azure AD Connect wordt niet geïnstalleerd in een door Azure AD DS beheerd domein om objecten te synchroniseren met Azure AD.

## <a name="forests-and-trusts"></a>Forests en vertrouwensrelaties

Een *forest* is een logische constructie die wordt gebruikt door AD DS (Active Directory Domain Services) om een of meer *domeinen*te groeperen. De domeinen slaan vervolgens objecten op voor gebruikers of groepen en bieden verificatieservices.

In Azure AD DS bevat het forest slechts één domein. On-premises AD DS-forests bevatten vaak veel domeinen. In grote organisaties, vooral na fusies en overnames, u eindigen met meerdere on-premises forests die vervolgens meerdere domeinen bevatten.

Standaard wordt een door Azure AD DS beheerd domein gemaakt als *gebruikersforest.* Dit type forest synchroniseert alle objecten uit Azure AD, inclusief gebruikersaccounts die zijn gemaakt in een on-premises AD DS-omgeving. Gebruikersaccounts kunnen rechtstreeks verifiëren tegen het door Azure AD DS beheerde domein, bijvoorbeeld om u aan te melden bij een vm die is verbonden aan een domein. Een gebruikersforest werkt wanneer de wachtwoordhashes kunnen worden gesynchroniseerd en gebruikers geen exclusieve aanmeldingsmethoden zoals smartcardverificatie gebruiken.

In een Azure AD *DS-bronforest* verifiëren gebruikers zich via een eenrichtingsforestvertrouwensrelatie van hun on-premises AD DS. *trust* Met deze benadering worden de gebruikersobjecten en wachtwoordhashes niet gesynchroniseerd met Azure AD DS. De gebruikersobjecten en -referenties bestaan alleen in de on-premises AD DS. Met deze aanpak kunnen ondernemingen resources en toepassingsplatforms in Azure hosten die afhankelijk zijn van klassieke verificatie, zoals LDAPS, Kerberos of NTLM, maar eventuele verificatieproblemen of -problemen worden verwijderd. Azure AD DS-bronforests zijn momenteel in preview.

Zie [Wat zijn resourceforests?][concepts-forest] En [Hoe werken forestvertrouwensrelaties in Azure AD DS voor][concepts-trust] meer informatie over foresttypen in Azure AD DS?

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKU

In Azure AD DS zijn de beschikbare prestaties en functies gebaseerd op de SKU. U selecteert een SKU wanneer u het beheerde domein maakt en u SKU's wijzigen als uw bedrijfsvereisten veranderen nadat het beheerde domein is geïmplementeerd. In de volgende tabel worden de beschikbare SKU's en de verschillen tussen deze tabel beschreven:

| SKU-naam   | Maximaal aantal objecten | Back-upfrequentie | Maximaal aantal uitgaande forestvertrouwensrelaties |
|------------|----------------------|------------------|----|
| Standard   | Onbeperkt            | Elke 7 dagen     | 0  |
| Enterprise | Onbeperkt            | Elke 3 dagen     | 5  |
| Premium    | Onbeperkt            | Dagelijks            | 10 |

Voordat deze Azure AD DS SKU's zijn gebruikt, is een factureringsmodel gebruikt op basis van het aantal objecten (gebruikers- en computeraccounts) in het beheerde Azure AD DS-beheerde domein. Er zijn geen variabele prijzen meer op basis van het aantal objecten in het beheerde domein.

Zie de [prijspagina van Azure AD DS][pricing]voor meer informatie .

### <a name="managed-domain-performance"></a>Beheerde domeinprestaties

De domeinprestaties variëren afhankelijk van de manier waarop verificatie voor een toepassing wordt geïmplementeerd. Aanvullende rekenbronnen kunnen helpen de responstijd van query's te verbeteren en de tijd die wordt besteed aan synchronisatiebewerkingen te verminderen. Naarmate het SKU-niveau toeneemt, worden de rekenresources die beschikbaar zijn voor het beheerde domein verhoogd. Controleer de prestaties van uw toepassingen en plan de benodigde resources.

Als uw bedrijf of toepassing om wijziging vraagt en u extra rekenkracht nodig hebt voor uw door Azure AD DS beheerde domein, u overschakelen naar een andere SKU.

### <a name="backup-frequency"></a>Back-upfrequentie

De back-upfrequentie bepaalt hoe vaak een momentopname van het beheerde domein wordt gemaakt. Back-ups zijn een geautomatiseerd proces dat wordt beheerd door het Azure-platform. In het geval van een probleem met uw beheerde domein, kan Azure-ondersteuning u helpen bij het herstellen van back-ups. Aangezien synchronisatie slechts op één manier *plaatsvindt vanuit* Azure AD, hebben eventuele problemen in een door Azure AD DS beheerd domein geen invloed op Azure AD of on-premises AD DS-omgevingen en -functionaliteit.

Naarmate het SKU-niveau toeneemt, neemt de frequentie van die back-upmomentopnamen toe. Bekijk uw bedrijfsvereisten en herstelpuntdoelstelling (RPO) om de vereiste back-upfrequentie voor uw beheerde domein te bepalen. Als uw bedrijfs- of toepassingsvereisten veranderen en u vaker back-ups nodig hebt, u overschakelen naar een andere SKU.

### <a name="outbound-forests"></a>Uitgaande bossen

In de vorige sectie werd eenmalige uitgaande forestvertrouwensrelaties beschreven van een Azure AD DS-beheerd domein naar een on-premises AD DS-omgeving (momenteel in preview). De SKU bepaalt het maximum aantal forestvertrouwensrelaties dat u maken voor een door Azure AD DS beheerd domein. Bekijk de vereisten voor uw bedrijf en toepassing om te bepalen hoeveel vertrouwensrelaties u daadwerkelijk nodig hebt en kies de juiste Azure AD DS SKU. Nogmaals, als uw bedrijfsvereisten veranderen en u extra forestvertrouwensrelaties moet maken, u overschakelen naar een andere SKU.

## <a name="next-steps"></a>Volgende stappen

Maak een [door Azure AD DS beheerd domein om][create-instance]aan de slag te gaan.

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
