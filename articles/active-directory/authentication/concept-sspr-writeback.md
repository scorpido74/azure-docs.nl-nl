---
title: On-premises integratie van wachtwoordterugschrijfgegevens met Azure AD SSPR - Azure Active Directory
description: Cloudwachtwoorden terugschrijven naar on-premises AD-infrastructuur
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fe58ae95c8d9c6b93c7e92e093541af009781ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454428"
---
# <a name="what-is-password-writeback"></a>Wat is wachtwoord terugschrijven?

Het hebben van een cloud-based password reset utility is geweldig, maar de meeste bedrijven hebben nog steeds een on-premises directory waar hun gebruikers bestaan. Hoe ondersteunt Microsoft om traditionele on-premises Active Directory (AD) synchroon te houden met wachtwoordwijzigingen in de cloud? Wachtwoordterugschrijven is een functie die is ingeschakeld met [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) waarmee wachtwoordwijzigingen in de cloud in realtime kunnen worden teruggeschreven naar een bestaande on-premises directory.

Wachtwoord terugschrijven wordt ondersteund in omgevingen die gebruik maken van:

* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Synchronisatie van wachtwoord-hashes](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Doorgeefverificatie](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Wachtwoordterugschrijven werkt niet meer voor klanten die Azure AD Connect-versies 1.0.8641 en ouder gebruiken wanneer de [Azure Access Control-service (ACS) op 7 november 2018 wordt uitgeschakeld.](../azuread-dev/active-directory-acs-migration.md) Azure AD Connect-versies 1.0.8641.0 en ouder staan op dat moment geen wachtwoordterugschrijffunctie meer toe omdat ze voor die functionaliteit afhankelijk zijn van ACS.
>
> Zie het artikel [Azure AD Connect: Upgrade van een vorige versie naar de laatste versie](../hybrid/how-to-upgrade-previous-version.md) van een vorige versie van Azure AD Connect om een onderbreking in de service te voorkomen.
>

Wachtwoord terugschrijven biedt:

* **Handhaving van on-premises Active Directory-wachtwoordbeleidsregels:** Wanneer een gebruiker zijn wachtwoord opnieuw instelt, wordt gecontroleerd of het voldoet aan uw on-premises Active Directory-beleid voordat hij het aan die map verbindt. Deze beoordeling omvat het controleren van de geschiedenis, complexiteit, leeftijd, wachtwoordfilters en andere wachtwoordbeperkingen die u hebt gedefinieerd in de lokale Active Directory.
* **Feedback met nulvertraging**: Wachtwoord terugschrijven is een synchrone bewerking. Uw gebruikers worden onmiddellijk op de hoogte gesteld als hun wachtwoord niet aan het beleid voldeed of om welke reden dan ook niet kon worden gereset of gewijzigd.
* **Ondersteunt wachtwoordwijzigingen vanuit het toegangspaneel en Office 365:** Wanneer federatieve of wachtwoordhash gesynchroniseerde gebruikers hun verlopen of niet-verlopen wachtwoorden komen wijzigen, worden deze wachtwoorden teruggeschreven naar uw lokale Active Directory-omgeving.
* **Ondersteunt terugschrijven van wachtwoorden wanneer een beheerder deze opnieuw instelt vanuit de Azure-portal:** wanneer een beheerder het wachtwoord van een gebruiker opnieuw instelt in de [Azure-portal,](https://portal.azure.com)als die gebruiker wordt gefedereerd of wachtwoordhash wordt gesynchroniseerd, wordt het wachtwoord teruggeschreven naar on-premises. Deze functionaliteit wordt momenteel niet ondersteund in de Office-beheerportal.
* **Vereist geen binnenkomende firewallregels:** Wachtwoordwriteback gebruikt een Azure Service Bus-relay als onderliggend communicatiekanaal. Alle communicatie is uitgaand over poort 443.

> [!NOTE]
> Beheerdersaccounts die binnen beveiligde groepen in on-premises AD bestaan, kunnen worden gebruikt met terugschrijven met wachtwoorden. Beheerders kunnen hun wachtwoord in de cloud wijzigen, maar kunnen geen wachtwoordreset gebruiken om een vergeten wachtwoord opnieuw in te stellen. Zie Beveiligde accounts en [groepen in Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory)voor meer informatie over beveiligde groepen.

## <a name="licensing-requirements-for-password-writeback"></a>Licentievereisten voor terugschrijven van wachtwoorden

**Self-Service Password Reset/Change/Unlock met on-premises writeback is een premium functie van Azure AD.** Zie de [prijssite van Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over licenties.

Als u wachtwoordterugschrijfgegevens wilt gebruiken, moet u een van de volgende licenties op uw tenant hebben toegewezen:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Beveiliging E3 of A3
* Enterprise Mobility + Beveiliging E5 of A5
* Microsoft 365 E3 of A3
* Microsoft 365 E5 of A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Zelfstandige Office 365-licentieplannen *ondersteunen geen 'Self-Service Password Reset/Change/Unlock with on-premises writeback'* en vereisen dat u een van de voorgaande plannen hebt om deze functionaliteit te laten werken.

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
1. De service zoekt vervolgens naar de gebruiker met behulp van het kenmerk cloudanker. Om deze lookup te laten slagen:

   * Het gebruikersobject moet in de active directory-connectorruimte staan.
   * Het object moet worden gekoppeld aan het bijbehorende metaverse object (MV).
   * Het gebruikersobject moet worden gekoppeld aan het bijbehorende Azure Active Directory-connectorobject.
   * De koppeling van het Active Directory-connectorobject naar de `Microsoft.InfromADUserAccountEnabled.xxx` MV moet de synchronisatieregel op de koppeling hebben.
   
   Wanneer de aanroep vanuit de cloud binnenkomt, gebruikt de synchronisatie-engine het kenmerk **cloudAnchor** om het ruimteobject Azure Active Directory-connectorop te zoeken. Vervolgens volgt de koppeling terug naar het MV-object en volgt de koppeling terug naar het Active Directory-object. Omdat er meerdere Active Directory-objecten (multi-forest) voor dezelfde gebruiker kunnen `Microsoft.InfromADUserAccountEnabled.xxx` zijn, vertrouwt de synchronisatieengine op de koppeling om de juiste te kiezen.

1. Nadat het gebruikersaccount is gevonden, wordt geprobeerd het wachtwoord rechtstreeks in het juiste Active Directory-forest opnieuw in te stellen.
1. Als de bewerking met het wachtwoordset is geslaagd, krijgt de gebruiker te horen dat zijn wachtwoord is gewijzigd.
   > [!NOTE]
   > Als de wachtwoordhash van de gebruiker wordt gesynchroniseerd met Azure AD met behulp van wachtwoordhashsynchronisatie, bestaat de kans dat het on-premises wachtwoordbeleid zwakker is dan het beleid voor cloudwachtwoorden. In dit geval wordt het on-premises beleid gehandhaafd. Dit beleid zorgt ervoor dat uw on-premises beleid wordt afgedwongen in de cloud, ongeacht of u wachtwoordhashsynchronisatie of federatie gebruikt om één aanmelding te bieden.

1. Als de bewerking met het instellen van wachtwoorden mislukt, wordt de gebruiker door een fout gevraagd het opnieuw te proberen. De bewerking kan mislukken omdat:
    * De service was uitgevallen.
    * Het wachtwoord dat ze hebben geselecteerd, voldeed niet aan het beleid van de organisatie.
    * Kan de gebruiker niet vinden in de lokale Active Directory.

      De foutberichten bieden richtlijnen aan gebruikers, zodat ze kunnen proberen op te lossen zonder tussenkomst van de beheerder.

## <a name="password-writeback-security"></a>Beveiliging van het terugschrijven van wachtwoorden

Wachtwoord writeback is een zeer veilige service. Om ervoor te zorgen dat uw gegevens worden beschermd, is een beveiligingsmodel met vier lagen ingeschakeld zoals de volgende beschrijft:

* **Huurder-specifieke service-bus relais**
   * Wanneer u de service instelt, wordt een tenantspecifiek servicebusrelais ingesteld dat wordt beschermd door een willekeurig gegenereerd sterk wachtwoord waarMicrosoft nooit toegang toe heeft.
* **Vergrendeld, cryptografisch sterk, wachtwoord encryptie sleutel**
   * Nadat de servicebusrelais is gemaakt, wordt een sterke symmetrische sleutel gemaakt die wordt gebruikt om het wachtwoord te versleutelen wanneer het over de draad komt. Deze sleutel leeft alleen in de geheime winkel van uw bedrijf in de cloud, die zwaar is vergrendeld en gecontroleerd, net als elk ander wachtwoord in de directory.
* **Industriestandaard Transport Layer Security (TLS)**
   1. Wanneer een wachtwoordreset of wijzigingsbewerking plaatsvindt in de cloud, wordt het wachtwoord met plaintext versleuteld met uw openbare sleutel.
   1. Het versleutelde wachtwoord wordt in een HTTPS-bericht geplaatst dat via een versleuteld kanaal wordt verzonden met behulp van Microsoft TLS/SSL-certs naar uw servicebusrelay.
   1. Nadat het bericht in de servicebus is aangekomen, wordt uw on-premises agent wakker en verifieert u zich bij de servicebus met behulp van het sterke wachtwoord dat eerder is gegenereerd.
   1. De on-premises agent pikt het versleutelde bericht op en ontsleutelt het met behulp van de privésleutel.
   1. De on-premises agent probeert het wachtwoord in te stellen via de AD DS SetPassword API. Met deze stap u uw on-premises wachtwoordbeleid van Uw Active Directory (zoals de complexiteit, leeftijd, geschiedenis en filters) in de cloud handhaven.
* **Beleid voor het verlopen van berichten**
   * Als het bericht in de servicebus zit omdat uw on-premises service is uitgeschakeld, is het een uit-op-een-rij tijd en wordt het na enkele minuten verwijderd. De time-out en verwijdering van het bericht verhoogt de veiligheid nog verder.

### <a name="password-writeback-encryption-details"></a>Details van wachtwoordterugschrijfversleuteling

Nadat een gebruiker een wachtwoordreset heeft ingediend, wordt het resetverzoek door verschillende versleutelingsstappen doorlopen voordat deze in uw on-premises omgeving aankomt. Deze versleutelingsstappen zorgen voor maximale servicebetrouwbaarheid en -beveiliging. Zij worden als volgt omschreven:

* **Stap 1: Wachtwoordversleuteling met 2048-bits RSA-sleutel**: Nadat een gebruiker een wachtwoord heeft ingediend dat opnieuw naar on-premises moet worden geschreven, wordt het ingediende wachtwoord zelf versleuteld met een RSA-toets van 2048 bits.
* **Stap 2: Versleuteling op pakketniveau met AES-GCM**: Het hele pakket, het wachtwoord plus de vereiste metadata, wordt versleuteld met behulp van AES-GCM. Deze versleuteling voorkomt dat iedereen met directe toegang tot het onderliggende ServiceBus-kanaal de inhoud kan bekijken of knoeien.
* **Stap 3: Alle communicatie vindt plaats via TLS/SSL**: Alle communicatie met ServiceBus vindt plaats in een SSL/TLS-kanaal. Deze versleuteling beveiligt de inhoud van onbevoegde derden.
* **Automatische sleutel roll over om de zes maanden**: Alle sleutels rollen om de zes maanden, of elke keer wachtwoord writeback is uitgeschakeld en vervolgens opnieuw ingeschakeld op Azure AD Connect, om maximale service beveiliging en veiligheid te garanderen.

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
   * Elke self-user self-service vrijwillige wijziging wachtwoord operatie
   * Elke selfserviceservicekracht van eindgebruikers wijzigt wachtwoordbewerking, bijvoorbeeld het verlopen van wachtwoorden
   * Elke selfservicewachtwoordreset van eindgebruikers die afkomstig is van de [portal voor het opnieuw instellen](https://passwordreset.microsoftonline.com) van wachtwoorden
* **Ondersteunde beheerdersbewerkingen**
   * Elke beheerder self-service vrijwillige wijziging wachtwoord operatie
   * Elke beheerder self-service force change password operation, bijvoorbeeld, password expiratie
   * Elke selfservicewachtwoordreset van de beheerder die afkomstig is van de [portal voor het opnieuw instellen](https://passwordreset.microsoftonline.com) van wachtwoorden
   * Elke door de beheerder geïnitieerde wachtwoordreset van eindgebruikers van de [Azure-portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Niet-ondersteunde terugschrijfbewerkingen

Wachtwoorden worden *in* geen van de volgende situaties teruggeschreven:

* **Niet-ondersteunde bewerkingen van eindgebruikers**
   * Elke eindgebruiker die zijn eigen wachtwoord opnieuw instelt met PowerShell-versie 1, versie 2 of de Microsoft Graph API
* **Niet-ondersteunde beheerdersbewerkingen**
   * Elke door de beheerder geïnitieerde wachtwoordreset van PowerShell-versie 1, versie 2 of de Microsoft Graph API
   * Elke door de beheerder geïnitieerde wachtwoordreset van de eindgebruiker vanuit het [Microsoft 365-beheercentrum](https://admin.microsoft.com)

> [!WARNING]
> Het gebruik van het selectievakje 'Gebruiker moet wachtwoord bij volgende aanmelding wijzigen' in on-premises Active Directory-beheerhulpprogramma's zoals Active Directory- gebruikers en computers of het Active Directory-beheercentrum wordt ondersteund als voorbeeldfunctie van Azure AD Connect. Zie voor meer informatie het artikel [Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Volgende stappen

Terugschrijven van wachtwoorden inschakelen met de zelfstudie: [terugschrijven van wachtwoorden inschakelen](tutorial-enable-writeback.md)
