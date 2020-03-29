---
title: 'Azure AD Connect-synchronisatie: gebruikers, groepen en contactpersonen begrijpen | Microsoft Documenten'
description: Hiermee worden gebruikers, groepen en contactpersonen in Azure AD Connect-synchronisatie uitgelegd.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661747754369c17ca98ae69d477e04124b6a2942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245483"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Synchronisatie van Azure AD Connect: gebruikers, groepen en contactpersonen begrijpen
Er zijn verschillende redenen waarom u meerdere Active Directory-forests zou hebben en er verschillende implementatietopologieën zijn. Veelvoorkomende modellen zijn een account-resource implementatie en GAL sync'ed forests na een fusie & overname. Maar zelfs als er pure modellen zijn, zijn hybride modellen ook gebruikelijk. De standaardconfiguratie in Azure AD Connect-synchronisatie gaat niet uit van een bepaald model, maar afhankelijk van hoe gebruikersmatching is geselecteerd in de installatiehandleiding, kunnen verschillende gedragingen worden waargenomen.

In dit onderwerp gaan we door hoe de standaardconfiguratie zich gedraagt in bepaalde topologieën. We gaan door de configuratie en de Synchronisatie regels Editor kan worden gebruikt om te kijken naar de configuratie.

Er zijn een paar algemene regels die de configuratie veronderstelt:
* Ongeacht welke order we importeren uit de bron Active Directories, het eindresultaat moet altijd hetzelfde zijn.
* Een actief account draagt altijd aanmeldingsgegevens bij, waaronder **userPrincipalName** en **sourceAnchor.**
* Een uitgeschakeld account zal userPrincipalName en sourceAnchor bijdragen, tenzij het een gekoppeld postvak is, als er geen actief account te vinden is.
* Een account met een gekoppeld postvak wordt nooit gebruikt voor userPrincipalName en sourceAnchor. Er wordt van uitgegaan dat een actieve account later zal worden gevonden.
* Een contactobject kan worden ingericht in Azure AD als contactpersoon of als gebruiker. U weet het pas echt als alle Active Directory-forests van de bron zijn verwerkt.

## <a name="groups"></a>Groepen
Belangrijke punten om rekening mee te houden bij het synchroniseren van groepen van Active Directory naar Azure AD:

* Azure AD Connect sluit ingebouwde beveiligingsgroepen uit van adreslijstsynchronisatie.

* Azure AD Connect biedt geen ondersteuning voor het synchroniseren van [lidmaatschappen](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) van primaire groepen naar Azure AD.

* Azure AD Connect biedt geen ondersteuning voor het synchroniseren van lidmaatschappen van [dynamische distributiegroepen](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) naar Azure AD.

* Ga als lid van de groep Active Directory naar Azure AD als een groep met e-mail:

    * Als het *kenmerk proxyAdres* van de groep leeg is, moet het *e-mailkenmerk* een waarde hebben

    * Als het kenmerk *proxyAdres* van de groep niet leeg is, moet het ten minste één SMTP-proxyadreswaarde bevatten. Hier volgen enkele voorbeelden:
    
      * Een Active Directory-groep waarvan het kenmerk proxyAdres waarde heeft *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* is niet e-mailingeschakeld in Azure AD. Het heeft geen SMTP-adres.
      
      * Een Active Directory-groep waarvan het kenmerk proxyAddress waarden *heeft\@{"X500:/0=contoso.com/ou=users/cn=testgroep","SMTP:johndoe contoso.com"}* wordt e-mail ingeschakeld in Azure AD.
      
      * Een Active Directory-groep waarvan het kenmerk proxyAddress waarden heeft *{"X500:/0=contoso.com/ou=users/cn=testgroep",\@"smtp:johndoe contoso.com"}* wordt ook e-mail ingeschakeld in Azure AD.

## <a name="contacts"></a>Contactpersonen
Het hebben van contactpersonen die een gebruiker in een ander forest vertegenwoordigen, is gebruikelijk na een fusie & overname waarbij een GALSync-oplossing twee of meer Exchange-forests overbrugt. Het contactobject wordt altijd vanuit de verbindingsruimte naar het metaverse verbonden met behulp van het kenmerk e-mail. Als er al een contactobject of gebruikersobject met hetzelfde e-mailadres is, worden de objecten samengevoegd. Dit is geconfigureerd in de regel **In van AD – Join contact .** Er is ook een regel met de naam **In van AD – Algemeen contact** met een kenmerkstroom naar het metaverse kenmerk **sourceObjectType** met de constante **contactpersoon**. Deze regel heeft een zeer lage prioriteit, dus als een gebruikersobject is verbonden met hetzelfde doel, dan zal de regel **In van AD – User Common** de waardegebruiker aan dit kenmerk bijdragen. Met deze regel heeft dit kenmerk de waarde Contactpersoon als er geen gebruiker is lid en de waarde Gebruiker als ten minste één gebruiker is gevonden.

Voor het inrichten van een object op Azure AD maakt de uitgaande regel **Out to AAD – Contact Join** een contactobject als het metaverse kenmerk **sourceObjectType** is ingesteld op **Contact**. Als dit kenmerk is ingesteld op **Gebruiker,** maakt de **regel Uit naar AAD – User Join** in plaats daarvan een gebruikersobject.
Het is mogelijk dat een object wordt gepromoot van Contact naar Gebruiker wanneer meer actieve mappen worden geïmporteerd en gesynchroniseerd.

In een GALSync-topologie vinden we bijvoorbeeld contactobjecten voor iedereen in het tweede forest wanneer we het eerste forest importeren. Hiermee worden nieuwe contactobjecten in de AAD-connector opgenomen. Wanneer we later het tweede forest importeren en synchroniseren, zullen we de echte gebruikers vinden en ze aansluiten bij de bestaande metaverse objecten. Vervolgens verwijderen we het contactobject in AAD en maken we in plaats daarvan een nieuw gebruikersobject.

Als u een topologie hebt waarbij gebruikers worden weergegeven als contactpersonen, moet u ervoor zorgen dat u gebruikers in het e-mailkenmerk in de installatiehandleiding matcht. Als u een andere optie selecteert, hebt u een orderafhankelijke configuratie. Contactobjecten worden altijd lid van het e-mailkenmerk, maar gebruikersobjecten worden alleen lid van het e-mailkenmerk als deze optie is geselecteerd in de installatiehandleiding. U dan eindigen met twee verschillende objecten in het metaverse met hetzelfde e-mailkenmerk als het contactobject vóór het object is geïmporteerd. Tijdens het exporteren naar Azure AD wordt er een fout gegenereerd. Dit gedrag is door het ontwerp en zou wijzen op slechte gegevens of dat de topologie niet correct werd geïdentificeerd tijdens de installatie.

## <a name="disabled-accounts"></a>Uitgeschakelde accounts
Uitgeschakelde accounts worden ook gesynchroniseerd met Azure AD. Uitgeschakelde accounts zijn gebruikelijk om bronnen in Exchange weer te geven, bijvoorbeeld vergaderruimtes. De uitzondering is gebruikers met een gekoppeld postvak; zoals eerder vermeld, zullen deze nooit een account inrichten bij Azure AD.

De aanname is dat als er een uitgeschakeld gebruikersaccount wordt gevonden, we later geen ander actief account meer vinden en het object is ingericht op Azure AD met de userPrincipalName en sourceAnchor gevonden. In het geval dat een ander actief account wordt samengevoegd tot hetzelfde metaverse object, worden de userPrincipalName en sourceAnchor gebruikt.

## <a name="changing-sourceanchor"></a>Bronanker wijzigen
Wanneer een object is geëxporteerd naar Azure AD, mag het sourceAnchor niet meer worden gewijzigd. Wanneer het object is geëxporteerd, wordt de metaverse **kenmerkcloudSourceAnchor** ingesteld met de **sourceAnchor-waarde** die is geaccepteerd door Azure AD. Als **sourceAnchor** is gewijzigd en niet overeenkomt met **cloudSourceAnchor,** wordt de regel **uit naar AAD – User Join** het kenmerk error **sourceAnchor verwijderd.** In dit geval moeten de configuratie of gegevens worden gecorrigeerd, zodat dezelfde sourceAnchor weer in de metaverse aanwezig is voordat het object opnieuw kan worden gesynchroniseerd.

## <a name="additional-resources"></a>Aanvullende resources
* [Synchronisatie van Azure AD Connect: synchronisatieopties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

