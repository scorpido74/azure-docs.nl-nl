---
title: Veelgestelde vragen over het opnieuw instellen van selfservicewachtwoorden - Azure Active Directory
description: Veelgestelde vragen over het opnieuw instellen van azure AD-zelfservicewachtwoorden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae3ace24d6f33702c15a364b913f841d90fd8872
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654201"
---
# <a name="password-management-frequently-asked-questions"></a>Veelgestelde vragen voor wachtwoordbeheer

De volgende zijn enkele veelgestelde vragen (FAQ) voor alle dingen met betrekking tot het resetten van wachtwoorden.

Als u een algemene vraag hebt over Azure Active Directory (Azure AD) en selfservice wachtwoordreset (SSPR) die hier niet wordt beantwoord, u de community om hulp vragen op het [Azure AD-forum.](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD) Tot de leden van de community behoren ingenieurs, productmanagers, MVP's en collega-IT-professionals.

Deze veelgestelde vragen is opgesplitst in de volgende secties:

* [Vragen over registratie van het opnieuw instellen van wachtwoorden](#password-reset-registration)
* [Vragen over het opnieuw instellen van wachtwoorden](#password-reset)
* [Vragen over wachtwoordwijziging](#password-change)
* [Vragen over wachtwoordbeheerrapporten](#password-management-reports)
* [Vragen over terugschrijven van wachtwoorden](#password-writeback)

## <a name="password-reset-registration"></a>Registratie van het opnieuw ingestelde wachtwoord

* **V: Kunnen mijn gebruikers hun eigen wachtwoordresetgegevens registreren?**

  > **A:** Ja. Zolang het opnieuw instellen van wachtwoorden is ingeschakeld en ze een licentiehttps://aka.ms/ssprsetup) hebben, kunnen gebruikers naar de registratieportal voor het opnieuw instellen van wachtwoorden gaan (om hun verificatiegegevens te registreren. Gebruikers kunnen zich ook registrerenhttps://myapps.microsoft.com)via het Access Panel ( . Als u zich wilt registreren via het toegangspaneel, moeten ze hun profielfoto selecteren, **Profiel**selecteren en vervolgens de optie **Registreren voor het opnieuw instellen van wachtwoorden** selecteren.
  >
  >
* **V: Als ik wachtwoordreset inschakel voor een groep en vervolgens besluit om deze voor iedereen in te schakelen, moeten mijn gebruikers zich opnieuw registreren?**

  > **A:** №. Gebruikers die verificatiegegevens hebben ingevuld, hoeven zich niet opnieuw te registreren.
  >
  >
* **V: Kan ik namens mijn gebruikers gegevens over het opnieuw instellen van wachtwoorden definiëren?**

  > **A:** Ja, u dit doen met Azure AD Connect, PowerShell, de [Azure-portal](https://portal.azure.com)of het [Microsoft 365-beheercentrum.](https://admin.microsoft.com) Zie [Gegevens die worden gebruikt door azure AD selfservice wachtwoord opnieuw instellen](howto-sspr-authenticationdata.md)voor meer informatie.
  >
  >
* **V: Kan ik gegevens synchroniseren voor beveiligingsvragen van on-premises?**

  > **A:** Nee, dit is vandaag niet mogelijk.
  >
  >
* **V: Kunnen mijn gebruikers gegevens zo registreren dat andere gebruikers deze gegevens niet kunnen zien?**

  > **A:** Ja. Wanneer gebruikers gegevens registreren met behulp van de registratieportal voor het opnieuw instellen van wachtwoorden, worden de gegevens opgeslagen in privéverificatievelden die alleen zichtbaar zijn voor globale beheerders en de gebruiker.
  >
  >
* **V: Moeten mijn gebruikers worden geregistreerd voordat ze een wachtwoordkunnen resetten?**

  > **A:** №. Als u namens hen voldoende verificatiegegevens definieert, hoeven gebruikers zich niet te registreren. Het opnieuw instellen van wachtwoorden werkt zolang u de gegevens die zijn opgeslagen in de juiste velden in de map correct hebt opgemaakt.
  >
  >
* **V: Kan ik namens mijn gebruikers de verificatietelefoon, verificatie-e-mail of alternatieve verificatietelefoonvelden synchroniseren of instellen?**

  > **A:** De velden die door een globale beheerder kunnen worden ingesteld, worden gedefinieerd in de vereisten van het artikel [SSPR-gegevens](howto-sspr-authenticationdata.md).
  >
  >
* **V: Hoe bepaalt de registratieportal welke opties mijn gebruikers moeten weergeven?**

  > **A:** De registratieportal voor het opnieuw instellen van wachtwoorden toont alleen de opties die u voor uw gebruikers hebt ingeschakeld. Deze opties vindt u onder de sectie Beleid voor het opnieuw instellen van **gebruikerswachtwoorden** van het tabblad **Configureren van** uw map. Als u bijvoorbeeld geen beveiligingsvragen inschakelt, kunnen gebruikers zich niet registreren voor die optie.
  >
  >
* **V: Wanneer wordt een gebruiker als geregistreerd beschouwd?**

  > **A:** Een gebruiker wordt beschouwd als geregistreerd voor SSPR wanneer hij ten minste het aantal methoden heeft geregistreerd dat nodig is om een wachtwoord dat u in de [Azure-portal](https://portal.azure.com)hebt **ingesteld, opnieuw in te stellen.**
  >
  >

## <a name="password-reset"></a>Wachtwoord opnieuw instellen

* **V: Voorkomt u dat gebruikers meerdere pogingen doen om een wachtwoord in korte tijd opnieuw in te stellen?**

  > **A:** Ja, er zijn beveiligingsfuncties ingebouwd in het opnieuw instellen van wachtwoorden om het te beschermen tegen misbruik. 
  >
  > Gebruikers kunnen binnen 24 uur slechts vijf pogingen tot het opnieuw instellen van wachtwoorden proberen voordat ze 24 uur worden vergrendeld. 
  >
  > Gebruikers kunnen proberen een telefoonnummer te valideren, een sms-bericht te sturen of beveiligingsvragen en -antwoorden slechts vijf keer binnen een uur te valideren voordat ze 24 uur worden vergrendeld. 
  >
  > Gebruikers kunnen een e-mail maximaal 10 keer verzenden binnen een periode van 10 minuten voordat ze 24 uur lang buitengesloten zijn.
  >
  > De tellers worden gereset zodra een gebruiker zijn wachtwoord opnieuw instelt.
  >
  >
* **V: Hoe lang moet ik wachten met het ontvangen van een e-mail, sms of telefoontje van het opnieuw instellen van het wachtwoord?**

  > **A:** E-mails, sms-berichten en telefoongesprekken moeten binnen een minuut aankomen. De normale case is 5 tot 20 seconden.
  > Als u de melding in dit tijdsbestek niet ontvangt:
  > * Controleer je ongewenste map.
  > * Controleer of het nummer of e-mail waarmee u wordt gecontacteerd, degene is die u verwacht.
  > * Controleer of de verificatiegegevens in de map correct zijn opgemaakt, bijvoorbeeld +1 4255551234 of *gebruiker\@contoso.com*. 
* **V: Welke talen worden ondersteund door het opnieuw instellen van het wachtwoord?**

  > **A:** De gebruikersinterface voor het opnieuw instellen van wachtwoorden, sms-berichten en spraakoproepen worden gelokaliseerd in dezelfde talen die worden ondersteund in Office 365.
  >
  >
* **V: Welke onderdelen van de wachtwoordreset-ervaring worden gebrandmerkt wanneer ik de organisatorische merkitems instel op het tabblad Configureren van mijn directory?**

  > **A:** De portal voor het opnieuw instellen van wachtwoorden toont het logo van uw organisatie en stelt u in staat om de link 'Contact opnemen met uw beheerder' te configureren om een aangepaste e-mail of URL aan te wijzen. Elke e-mail die wordt verzonden via een wachtwoordreset, bevat het logo, de kleuren en de naam van uw organisatie in de hoofdtekst van de e-mail en wordt aangepast vanuit de instellingen voor die specifieke naam.
  >
  >
* **V: Hoe kan ik mijn gebruikers informeren over waar ze naartoe moeten om hun wachtwoorden opnieuw in te stellen?**

  > **A:** Probeer enkele van de suggesties in ons [SSPR-implementatieartikel.](howto-sspr-deployment.md#plan-communications)
  >
  >
* **V: Kan ik deze pagina gebruiken vanaf een mobiel apparaat?**

  > **A:** Ja, deze pagina werkt op mobiele apparaten.
  >
  >
* **V: Ondersteunt u het ontgrendelen van lokale Active Directory-accounts wanneer gebruikers hun wachtwoorden opnieuw instellen?**

  > **A:** Ja. Wanneer een gebruiker zijn wachtwoord opnieuw instelt, wordt het account van die gebruiker automatisch ontgrendeld wanneer hij zijn wachtwoord opnieuw instelt als het wachtwoord is geïmplementeerd.
  >
  >
* **V: Hoe kan ik wachtwoordreset rechtstreeks integreren in de aanmeldingservaring van mijn gebruiker op het bureaublad?**

  > **A:** Als u een Azure AD Premium-klant bent, u Microsoft Identity Manager zonder extra kosten installeren en de on-premises oplossing voor het opnieuw instellen van wachtwoorden implementeren.
  >
  >
* **V: Kan ik verschillende beveiligingsvragen instellen voor verschillende locaties?**

  > **A:** Nee, dit is vandaag niet mogelijk.
  >
  >
* **V: Hoeveel vragen kan ik configureren voor de verificatieoptie beveiligingsvragen?**

  > **A:** U maximaal 20 aangepaste beveiligingsvragen configureren in de [Azure-portal.](https://portal.azure.com)
  >
  >
* **V: Hoe lang kunnen beveiligingsvragen zijn?**

  > **A:** Beveiligingsvragen kunnen 3 tot 200 tekens lang zijn.
  >
  >
* **V: Hoe lang kunnen de antwoorden op beveiligingsvragen zijn?**

  > **A:** Antwoorden kunnen 3 tot 40 tekens lang zijn.
  >
  >
* **V: Worden dubbele antwoorden op beveiligingsvragen afgewezen?**

  > **A:** Ja, we wijzen dubbele antwoorden op beveiligingsvragen af.
  >
  >
* **V: Kan een gebruiker dezelfde beveiligingsvraag meerdere tijd registreren?**

  > **A:** №. Nadat een gebruiker een bepaalde vraag heeft geregistreerd, kan hij zich niet een tweede keer voor die vraag registreren.
  >
  >
* **V: Is het mogelijk om een minimumlimiet van beveiligingsvragen in te stellen voor registratie en reset?**

  > **A:** Ja, er kan één limiet worden ingesteld voor registratie en een andere voor het opnieuw instellen. Drie tot vijf beveiligingsvragen kunnen worden vereist voor registratie, en drie tot vijf vragen kunnen worden vereist voor reset.
  >
  >
* **V: Ik heb mijn beleid geconfigureerd om gebruikers te verplichten beveiligingsvragen te gebruiken voor het opnieuw instellen, maar de Azure-beheerders lijken anders te zijn geconfigureerd.**

  > **A:** Dit is het verwachte gedrag. Microsoft dwingt voor elke Azure-beheerdersrol standaard een sterk beleid met twee-staps-verificatie af voor het opnieuw instellen van wachtwoorden. Dit voorkomt dat beheerders beveiligingsvragen kunnen gebruiken. Meer informatie over dit beleid vindt u in het [wachtwoordbeleid en -beperkingen in het](concept-sspr-policy.md) Azure Active Directory-artikel.
  >
  >
* **V: Als een gebruiker zich meer heeft geregistreerd dan het maximum aantal vragen dat nodig is om opnieuw in te stellen, hoe worden de beveiligingsvragen dan geselecteerd tijdens het opnieuw instellen?**

  > **A:** *N-aantal* beveiligingsvragen wordt willekeurig geselecteerd uit het totale aantal vragen waarvoor een gebruiker zich heeft geregistreerd, waarbij *N* het bedrag is dat is ingesteld voor het aantal vragen dat nodig is om de optie opnieuw in **te stellen.** Als een gebruiker bijvoorbeeld vijf beveiligingsvragen heeft geregistreerd, maar slechts drie zijn vereist om een wachtwoord opnieuw in te stellen, worden drie van de vijf vragen willekeurig geselecteerd en bij het opnieuw instellen weergegeven. Om te voorkomen dat vraag hameren, als de gebruiker krijgt de antwoorden op de vragen verkeerd het selectieproces begint opnieuw.
  >
  >
* **V: Hoe lang zijn de e-mail- en SMS-toegangscodes geldig?**

  > **A:** De sessielevensduur voor het opnieuw instellen van het wachtwoord is 15 minuten. Vanaf het begin van de wachtwoordresetbewerking heeft de gebruiker 15 minuten de tijd om zijn wachtwoord opnieuw in te stellen. De e-mail en sms-toegangscode zijn 5 minuten geldig tijdens de wachtwoordresetsessie.
  >
  >
* **V: Kan ik voorkomen dat gebruikers hun wachtwoord opnieuw instellen?**

  > **A:** Ja, als u een groep gebruikt om SSPR in te schakelen, u een individuele gebruiker uit de groep verwijderen waarmee gebruikers hun wachtwoord kunnen opnieuw instellen. Als de gebruiker een globale beheerder is, behoudt hij de mogelijkheid om zijn wachtwoord opnieuw in te stellen en kan dit niet worden uitgeschakeld.
  >
  >

## <a name="password-change"></a>Wachtwoord wijzigen

* **V: Waar moeten mijn gebruikers naartoe om hun wachtwoorden te wijzigen?**

  > **A:** Gebruikers kunnen hun wachtwoorden overal wijzigen waar ze hun profielfoto of pictogram zien, zoals in de rechterbovenhoek van hun [Office 365-portal](https://portal.office.com) of [Access Panel-ervaringen.](https://myapps.microsoft.com) Gebruikers kunnen hun wachtwoorden wijzigen via de [pagina Access Panel Profile.](https://account.activedirectory.windowsazure.com/r#/profile) Gebruikers kunnen ook worden gevraagd om hun wachtwoorden automatisch te wijzigen op de aanmeldingspagina van Azure AD als hun wachtwoorden zijn verlopen. Ten slotte kunnen gebruikers rechtstreeks naar de [azure AD-wachtwoordwijzigingsportal](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) bladeren als ze hun wachtwoord willen wijzigen.
  >
  >
* **V: Kunnen mijn gebruikers op de hoogte worden gesteld in de Office-portal wanneer hun on-premises wachtwoord verloopt?**

  > **A:** Ja, dit is vanaf vandaag mogelijk als u Active Directory Federation Services (AD FS) gebruikt. Als u AD FS gebruikt, volgt u de instructies in het [artikel Claims voor wachtwoordbeleid verzenden met AD FS.](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) Als u wachtwoordhashsynchronisatie gebruikt, is dit vandaag niet mogelijk. We synchroniseren wachtwoordbeleid niet van on-premises mappen, dus het is niet mogelijk voor ons om vervaldatummeldingen te plaatsen naar cloudervaringen. In beide gevallen is het ook mogelijk om [gebruikers van wie de wachtwoorden op het punt staan te verlopen via PowerShell op de hoogte te stellen.](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx)
  >
  >
* **V: Kan ik voorkomen dat gebruikers hun wachtwoord wijzigen?**

  > **A:** Voor alleen cloudgebruikers kunnen wachtwoordwijzigingen niet worden geblokkeerd. Voor on-premises gebruikers u instellen dat de optie **Gebruiker geen wachtwoord kan wijzigen** in geselecteerd. De geselecteerde gebruikers kunnen hun wachtwoord niet wijzigen.
  >
  >

## <a name="password-management-reports"></a>Rapporten over wachtwoordbeheer

* **V: Hoe lang duurt het voordat gegevens worden weergegeven in de rapporten voor wachtwoordbeheer?**

  > **A:** Gegevens moeten binnen 5 tot 10 minuten worden weergegeven in de rapporten voor wachtwoordbeheer. In sommige gevallen kan het tot een uur duren voordat het wordt weergegeven.
  >
  >
* **V: Hoe kan ik de wachtwoordbeheerrapporten filteren?**

  > **A:** Als u de rapporten voor wachtwoordbeheer wilt filteren, selecteert u het kleine vergrootglas rechts van de kolomlabels, boven aan het rapport. Als u rijker wilt filteren, u het rapport downloaden naar Excel en een draaitabel maken.
  >
  >
* **V: Wat is het maximum aantal gebeurtenissen dat is opgeslagen in de rapporten voor wachtwoordbeheer?**

  > **A:** Tot 75.000 registratiegebeurtenissen voor het opnieuw instellen van wachtwoorden of het opnieuw instellen van wachtwoorden worden opgeslagen in de rapporten voor wachtwoordbeheer, die tot 30 dagen teruggaan. We werken eraan om dit aantal uit te breiden met meer evenementen.
  >
  >
* **V: Hoe ver terug gaan de wachtwoordbeheerrapporten?**

  > **A:** In de rapporten voor wachtwoordbeheer worden bewerkingen weergegeven die in de afgelopen 30 dagen zijn uitgevoerd. Als u deze gegevens nu wilt archiveren, u de rapporten periodiek downloaden en op een aparte locatie opslaan.
  >
  >
* **V: Is er een maximum aantal rijen dat kan worden weergegeven in de rapporten voor wachtwoordbeheer?**

  > **A:** Ja. Er kunnen maximaal 75.000 rijen worden weergegeven in een van de rapporten voor wachtwoordbeheer, ongeacht of ze worden weergegeven in de gebruikersinterface of worden gedownload.
  >
  >
* **V: Is er een API om toegang te krijgen tot de gegevens over het opnieuw instellen van wachtwoorden of registratierapportagegegevens?**

  > **A:** Ja. Zie [Informatie over het programmatisch openen van gebeurtenisgebeurtenissen voor](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)het melden van wachtwoorden voor het bijwerken van wachtwoorden voor meer informatie over het programmatisch instellen van het wachtwoord.
  >
  >

## <a name="password-writeback"></a>Wachtwoord terugschrijven

* **V: Hoe werkt wachtwoord writeback achter de schermen?**

  > **A:** Zie het artikel [Hoe wachtwoordwriteback werkt](howto-sspr-writeback.md) voor een uitleg over wat er gebeurt wanneer u wachtwoordterugschrijfinformatie inschakelt en hoe gegevens via het systeem terugstromen naar uw on-premises omgeving.
  >
  >
* **V: Hoe lang duurt het terugschrijven van wachtwoorden naar het werk? Is er een synchronisatie vertraging zoals er is met wachtwoord hash sync?**

  > **A:** Wachtwoord terugschrijven is direct. Het is een synchrone pijplijn die fundamenteel anders werkt dan wachtwoordhashsynchronisatie. Met wachtwoordterugschrijfinformatie kunnen gebruikers realtime feedback krijgen over het succes van hun wachtwoordreset of wijzigingsbewerking. De gemiddelde tijd voor een succesvolle terugschrijven van een wachtwoord is minder dan 500 ms.
  >
  >
* **V: Als mijn on-premises account is uitgeschakeld, hoe wordt mijn cloudaccount en de toegang beïnvloed?**

  > **A:** Als uw on-premises id is uitgeschakeld, worden uw cloud-id en toegang ook uitgeschakeld bij het volgende synchronisatieinterval via Azure AD Connect. Standaard is deze synchronisatie elke 30 minuten.
  >
  >
* **V: Als mijn on-premises account wordt beperkt door een on-premises Active Directory-wachtwoordbeleid, houdt SSPR zich aan dit beleid wanneer ik mijn wachtwoord wijzig?**

  > **A:** Ja, SSPR is afhankelijk van en houdt zich aan het on-premises Active Directory-wachtwoordbeleid. Dit beleid omvat het typische wachtwoordbeleid voor Active Directory-domeinen, evenals een gedefinieerd, fijnkorrelig wachtwoordbeleid dat is gericht op een gebruiker.
  >
  >
* **V: Voor welke typen accounts werkt terugschrijfwerk voor wachtwoorden?**

  > **A:** Wachtwoordterugschrijven werkt voor gebruikersaccounts die zijn gesynchroniseerd van on-premises Active Directory naar Azure AD, inclusief federatieve, wachtwoordhash gesynchroniseerd en Gebruikers van Pass-Through Autentication.
  >
  >
* **V: Dwingt wachtwoordterugschrijven het wachtwoordbeleid van mijn domein af?**

  > **A:** Ja. Wachtwoord terugschrijven dwingt wachtwoord leeftijd, geschiedenis, complexiteit, filters, en elke andere beperking die u zou kunnen zetten op wachtwoorden in uw lokale domein.
  >
  >
* **V: Is wachtwoord terugschrijven veilig?  Hoe weet ik zeker dat ik niet gehackt word?**

  > **A:** Ja, wachtwoord terugschrijven is veilig. Als u meer wilt weten over de meerdere beveiligingslagen die door de wachtwoordschrijfservice zijn geïmplementeerd, raadpleegt u de sectie [Password writeback security](concept-sspr-writeback.md#password-writeback-security) in het [overzichtsartikel Wachtwoord terugschrijven.](howto-sspr-writeback.md)
  >
  >

## <a name="next-steps"></a>Volgende stappen

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor het opnieuw instellen van een wachtwoord](../user-help/active-directory-passwords-reset-register.md)
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets kapot is. Hoe los ik SSPR op?](active-directory-passwords-troubleshoot.md)
