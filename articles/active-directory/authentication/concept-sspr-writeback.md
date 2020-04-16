---
title: On-premises terugschrijfherstel van wachtwoorden met selfservicewachtwoordreset - Azure Active Directory
description: Meer informatie over hoe wachtwoordwijziging of resetgebeurtenissen in Azure Active Directory kunnen worden teruggeschreven naar een on-premises directoryomgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787c15c11c995c7eb30662131302658175c7f877
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393026"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Hoe werkt selfservice wachtwoordreset writeback in Azure Active Directory?

Met Azure Active Directory (Azure AD) selfservice password reset (SSPR) kunnen gebruikers hun wachtwoorden in de cloud opnieuw instellen, maar de meeste bedrijven hebben ook een on-premises AD DS-omgeving (Active Directory Domain Services) waar hun gebruikers bestaan. Wachtwoordterugschrijven is een functie die is ingeschakeld met [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) waarmee wachtwoordwijzigingen in de cloud in realtime kunnen worden teruggeschreven naar een bestaande on-premises directory. In deze configuratie, wanneer gebruikers hun wachtwoorden wijzigen of resetten met SSPR in de cloud, worden de bijgewerkte wachtwoorden ook teruggeschreven naar de on-premises AD DS-omgeving

Wachtwoordterugschrijven wordt ondersteund in omgevingen die de volgende hybride identiteitsmodellen gebruiken:

* [Synchronisatie van wachtwoord-hashes](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Pass-through-verificatie](../hybrid/how-to-connect-pta.md)
* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Wachtwoord terugschrijven biedt de volgende functies:

* **Handhaving van het wachtwoordbeleid van Active Directory Domain Services (AD DS) op**locatie: wanneer een gebruiker zijn wachtwoord opnieuw instelt, wordt gecontroleerd of het voldoet aan uw on-premises AD DS-beleid voordat hij het aan die directory verbindt. Deze beoordeling omvat het controleren van de geschiedenis, complexiteit, leeftijd, wachtwoordfilters en andere wachtwoordbeperkingen die u definieert in AD DS.
* **Feedback met nulvertraging**: Wachtwoord terugschrijven is een synchrone bewerking. Gebruikers worden onmiddellijk op de hoogte gesteld als hun wachtwoord niet aan het beleid voldoet of om welke reden dan ook niet kan worden gereset of gewijzigd.
* **Ondersteunt wachtwoordwijzigingen vanuit het toegangspaneel en Office 365:** Wanneer federatieve of wachtwoordhash gesynchroniseerde gebruikers hun verlopen of niet-verlopen wachtwoorden komen wijzigen, worden deze wachtwoorden teruggeschreven naar AD DS.
* **Ondersteunt terugschrijven van wachtwoorden wanneer een beheerder deze opnieuw instelt vanuit de Azure-portal:** Wanneer een beheerder het wachtwoord van een gebruiker opnieuw instelt in de [Azure-portal,](https://portal.azure.com)wordt het wachtwoord teruggeschreven naar on-premises als die gebruiker wordt gefedereerd of als wachtwoordhash is gesynchroniseerd. Deze functionaliteit wordt momenteel niet ondersteund in de Office-beheerportal.
* **Vereist geen binnenkomende firewallregels:** Wachtwoordwriteback gebruikt een Azure Service Bus-relay als onderliggend communicatiekanaal. Alle communicatie is uitgaand over poort 443.

> [!NOTE]
> Beheerdersaccounts die binnen beveiligde groepen in on-premises AD bestaan, kunnen worden gebruikt met terugschrijven met wachtwoorden. Beheerders kunnen hun wachtwoord in de cloud wijzigen, maar kunnen geen wachtwoordreset gebruiken om een vergeten wachtwoord opnieuw in te stellen. Zie Beveiligde accounts en [groepen in Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory)voor meer informatie over beveiligde groepen.

## <a name="how-password-writeback-works"></a>Hoe wachtwoord terugschrijven werkt

Wanneer een gefedereerde of wachtwoordhashgesynchroniseerde gebruiker probeert zijn wachtwoord opnieuw in de cloud te resetten of te wijzigen, vinden de volgende acties plaats:

1. Er wordt een controle uitgevoerd om te zien welk type wachtwoord de gebruiker heeft. Als het wachtwoord on-premises wordt beheerd:
   * Er wordt een controle uitgevoerd om te zien of de terugschrijfservice actief is. Als dit het is, kan de gebruiker doorgaan.
   * Als de terugschrijfservice is uitgeschakeld, wordt de gebruiker geïnformeerd dat zijn wachtwoord nu niet kan worden gereset.
1. Vervolgens passeert de gebruiker de juiste verificatiepoorten en bereikt hij de **pagina Wachtwoord opnieuw instellen.**
1. De gebruiker selecteert een nieuw wachtwoord en bevestigt het.
1. Wanneer de gebruiker **Verzenden**selecteert, wordt het plaintext-wachtwoord versleuteld met een symmetrische sleutel die is gemaakt tijdens het installatieproces voor terugschrijven.
1. Het versleutelde wachtwoord is opgenomen in een payload die via een HTTPS-kanaal naar uw tenant-specifieke servicebusrelay wordt verzonden (dat voor u is ingesteld tijdens het installatieproces voor writeback). Dit relais wordt beschermd door een willekeurig gegenereerd wachtwoord dat alleen uw on-premises installatie weet.
1. Nadat het bericht de servicebus heeft bereikt, wordt het eindpunt voor het opnieuw instellen van wachtwoorden automatisch wakker en ziet het dat er een resetverzoek in behandeling is.
1. De service zoekt vervolgens naar de gebruiker met behulp van het kenmerk cloudanker. Om deze lookup te laten slagen, moet aan de volgende voorwaarden worden voldaan:

   * Het gebruikersobject moet in de active directory-connectorruimte staan.
   * Het object moet worden gekoppeld aan het bijbehorende metaverse object (MV).
   * Het gebruikersobject moet worden gekoppeld aan het bijbehorende Azure Active Directory-connectorobject.
   * De koppeling van het Active Directory-connectorobject naar de `Microsoft.InfromADUserAccountEnabled.xxx` MV moet de synchronisatieregel op de koppeling hebben.

   Wanneer de aanroep vanuit de cloud binnenkomt, gebruikt de synchronisatie-engine het kenmerk **cloudAnchor** om het ruimteobject Azure Active Directory-connectorop te zoeken. Vervolgens volgt de koppeling terug naar het MV-object en volgt de koppeling terug naar het Active Directory-object. Omdat er meerdere Active Directory-objecten (multi-forest) voor dezelfde gebruiker kunnen `Microsoft.InfromADUserAccountEnabled.xxx` zijn, vertrouwt de synchronisatieengine op de koppeling om de juiste te kiezen.

1. Nadat het gebruikersaccount is gevonden, wordt geprobeerd het wachtwoord rechtstreeks in het juiste Active Directory-forest opnieuw in te stellen.
1. Als de bewerking met het wachtwoordset is geslaagd, krijgt de gebruiker te horen dat zijn wachtwoord is gewijzigd.

   > [!NOTE]
   > Als de wachtwoordhash van de gebruiker wordt gesynchroniseerd met Azure AD met behulp van wachtwoordhashsynchronisatie, bestaat de kans dat het on-premises wachtwoordbeleid zwakker is dan het beleid voor cloudwachtwoorden. In dit geval wordt het on-premises beleid gehandhaafd. Dit beleid zorgt ervoor dat uw on-premises beleid wordt afgedwongen in de cloud, ongeacht of u wachtwoordhashsynchronisatie of federatie gebruikt om één aanmelding te bieden.

1. Als de bewerking met het instellen van wachtwoorden mislukt, wordt de gebruiker door een fout gevraagd het opnieuw te proberen. De bewerking kan mislukken om de volgende redenen:
    * De service was uitgevallen.
    * Het wachtwoord dat ze hebben geselecteerd, voldoet niet aan het beleid van de organisatie.
    * Kan de gebruiker niet vinden in de lokale Active Directory.

   De foutberichten bieden richtlijnen aan gebruikers, zodat ze kunnen proberen op te lossen zonder tussenkomst van de beheerder.

## <a name="password-writeback-security"></a>Beveiliging van het terugschrijven van wachtwoorden

Wachtwoord writeback is een zeer veilige service. Om ervoor te zorgen dat uw gegevens worden beschermd, is een beveiligingsmodel met vier lagen als volgt ingeschakeld:

* **Huurder-specifieke service-bus relais**
   * Wanneer u de service instelt, wordt een tenantspecifiek servicebusrelais ingesteld dat wordt beschermd door een willekeurig gegenereerd sterk wachtwoord waarMicrosoft nooit toegang toe heeft.
* **Vergrendeld, cryptografisch sterk, wachtwoord encryptie sleutel**
   * Nadat de servicebusrelais is gemaakt, wordt een sterke symmetrische sleutel gemaakt die wordt gebruikt om het wachtwoord te versleutelen wanneer het over de draad komt. Deze sleutel leeft alleen in de geheime winkel van uw bedrijf in de cloud, die zwaar is vergrendeld en gecontroleerd, net als elk ander wachtwoord in de directory.
* **Industriestandaard Transport Layer Security (TLS)**
   1. Wanneer een wachtwoordreset of wijzigingsbewerking plaatsvindt in de cloud, wordt het wachtwoord met plaintext versleuteld met uw openbare sleutel.
   1. Het versleutelde wachtwoord wordt geplaatst in een HTTPS-bericht dat via een versleuteld kanaal wordt verzonden met behulp van Microsoft TLS/SSL-certs naar uw servicebusrelay.
   1. Nadat het bericht in de servicebus is aangekomen, wordt uw on-premises agent wakker en verifieert u zich bij de servicebus met behulp van het sterke wachtwoord dat eerder is gegenereerd.
   1. De on-premises agent pikt het versleutelde bericht op en ontsleutelt het met behulp van de privésleutel.
   1. De on-premises agent probeert het wachtwoord in te stellen via de AD DS SetPassword API. Met deze stap u uw on-premises wachtwoordbeleid van Uw Active Directory (zoals de complexiteit, leeftijd, geschiedenis en filters) in de cloud handhaven.
* **Beleid voor het verlopen van berichten**
   * Als het bericht in de servicebus zit omdat uw on-premises service is uitgeschakeld, is het een uit-op-een-rij tijd en wordt het na enkele minuten verwijderd. De time-out en verwijdering van het bericht verhoogt de veiligheid nog verder.

### <a name="password-writeback-encryption-details"></a>Details van wachtwoordterugschrijfversleuteling

Nadat een gebruiker een wachtwoordreset heeft ingediend, wordt het resetverzoek door verschillende versleutelingsstappen doorlopen voordat deze in uw on-premises omgeving aankomt. Deze versleutelingsstappen zorgen voor maximale servicebetrouwbaarheid en -beveiliging. Zij worden als volgt omschreven:

1. **Wachtwoordversleuteling met 2048-bits RSA-sleutel**: Nadat een gebruiker een wachtwoord heeft ingediend dat moet worden teruggeschreven naar on-premises, wordt het ingediende wachtwoord zelf versleuteld met een RSA-sleutel van 2048 bits.
1. **Versleuteling op pakketniveau met AES-GCM**: Het hele pakket, het wachtwoord plus de vereiste metagegevens, wordt versleuteld met behulp van AES-GCM. Deze versleuteling voorkomt dat iedereen met directe toegang tot het onderliggende ServiceBus-kanaal de inhoud kan bekijken of knoeien.
1. **Alle communicatie vindt plaats via TLS/SSL**: Alle communicatie met ServiceBus vindt plaats in een SSL/TLS-kanaal. Deze versleuteling beveiligt de inhoud van onbevoegde derden.
1. **Automatische sleutel roll over om de zes maanden**: Alle sleutels rollen om de zes maanden, of elke keer wachtwoord writeback is uitgeschakeld en vervolgens opnieuw ingeschakeld op Azure AD Connect, om maximale service beveiliging en veiligheid te garanderen.

### <a name="password-writeback-bandwidth-usage"></a>Gebruik van wachtwoordterugschrijfbandbreedte

Wachtwoordwriteback is een service met een lage bandbreedte die alleen aanvragen terugstuurt naar de on-premises agent onder de volgende omstandigheden:

* Er worden twee berichten verzonden wanneer de functie is ingeschakeld of uitgeschakeld via Azure AD Connect.
* Eén bericht wordt eenmaal per vijf minuten verzonden als serviceheartbeat zolang de service wordt uitgevoerd.
* Er worden twee berichten verzonden elke keer dat een nieuw wachtwoord wordt verzonden:
   * Het eerste bericht is een verzoek om de bewerking uit te voeren.
   * Het tweede bericht bevat het resultaat van de bewerking en wordt in de volgende omstandigheden verzonden:
      * Elke keer dat een nieuw wachtwoord wordt ingediend tijdens een selfservicewachtwoord voor gebruikers.
      * Elke keer dat een nieuw wachtwoord wordt ingediend tijdens een bewerking voor het wijzigen van het wachtwoord van de gebruiker.
      * Elke keer dat een nieuw wachtwoord wordt ingediend tijdens een door de beheerder gestarte wachtwoordreset van de gebruiker (alleen vanuit de Azure-beheerportalen).

#### <a name="message-size-and-bandwidth-considerations"></a>Overwegingen voor berichtgrootte en bandbreedte

De grootte van elk van het eerder beschreven bericht is meestal onder 1 KB. Zelfs onder extreme belastingen verbruikt de wachtwoordwriteback-service zelf een paar kilobits per seconde bandbreedte. Omdat elk bericht in realtime wordt verzonden, alleen wanneer dit vereist is door een wachtwoordupdatebewerking en omdat de grootte van het bericht zo klein is, is het bandbreedtegebruik van de terugschrijfmogelijkheid te klein om een meetbare impact te hebben.

## <a name="supported-writeback-operations"></a>Ondersteunde terugschrijfbewerkingen

Wachtwoorden worden in alle volgende situaties teruggeschreven:

* **Ondersteunde bewerkingen voor eindgebruikers**
   * Elke eindgebruiker self-service vrijwillige verandering wachtwoord bewerking.
   * Elke selfserviceservicekracht van eindgebruikers wijzigt de wachtwoordbewerking, bijvoorbeeld het verlopen van het wachtwoord.
   * Elke selfservicewachtwoordreset van de eindgebruiker die afkomstig is van de portal voor het opnieuw instellen van [wachtwoorden.](https://passwordreset.microsoftonline.com)

* **Ondersteunde beheerdersbewerkingen**
   * Elke beheerder self-service vrijwillige verandering wachtwoord bewerking.
   * Elke beheerder self-service force change password operation, bijvoorbeeld, wachtwoord expiratie.
   * Elke selfservicewachtwoordreset van de beheerder die afkomstig is van de portal voor het opnieuw instellen van [wachtwoorden.](https://passwordreset.microsoftonline.com)
   * Elke door de beheerder geïnitieerde wachtwoordreset van eindgebruikers van de [Azure-portal.](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Niet-ondersteunde terugschrijfbewerkingen

Wachtwoorden worden in geen van de volgende situaties teruggeschreven:

* **Niet-ondersteunde bewerkingen van eindgebruikers**
   * Elke eindgebruiker die zijn eigen wachtwoord opnieuw instelt met PowerShell-versie 1, versie 2 of de Microsoft Graph API.
* **Niet-ondersteunde beheerdersbewerkingen**
   * Elke door de beheerder geïnitieerde wachtwoordreset van PowerShell-versie 1, versie 2 of de Microsoft Graph-API.
   * Elke door de beheerder geïnitieerde wachtwoordreset van de eindgebruiker vanuit het [Microsoft 365-beheercentrum](https://admin.microsoft.com).

> [!WARNING]
> Het gebruik van het selectievakje 'Gebruiker moet wachtwoord bij volgende aanmelding wijzigen' in on-premises AD DS-beheertools zoals Active Directory- gebruikers en computers of het Active Directory-beheercentrum wordt ondersteund als voorbeeldfunctie van Azure AD Connect. Zie [Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](../hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voer de volgende zelfstudie in om aan de slag te gaan met SSPR-terugschrijftekst:

> [!div class="nextstepaction"]
> [Zelfstudie: Self-service wachtwoordreset (SSPR) terugschrijven inschakelen](tutorial-enable-writeback.md)
