---
title: Azure AD Connect - LargeObject-fouten veroorzaakt door kenmerk UserCertificate | Microsoft Documenten
description: In dit onderwerp worden de herstelstappen weergegeven voor LargeObject-fouten die zijn veroorzaakt door het kenmerk UserCertificate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c851b5ef024e6584e6f8c93995208b08a91fbb60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62095486"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Synchronisatie van Azure AD Connect: verwerking van LargeObject-fouten die worden veroorzaakt door het kenmerk UserCertificate

Azure AD handhaaft een maximumlimiet van **15** certificaatwaarden op het kenmerk **userCertificate.** Als Azure AD Connect een object met meer dan 15 waarden exporteert naar Azure AD, retourneert Azure AD een **LargeObject-fout** met bericht:

>*"Het ingerichte object is te groot. Het aantal kenmerkwaarden op dit object bijsnijden. De bewerking wordt opnieuw geprobeerd in de volgende synchronisatiecyclus..."*

De fout LargeObject kan worden veroorzaakt door andere AD-kenmerken. Als u wilt bevestigen dat het inderdaad wordt veroorzaakt door het kenmerk userCertificate, moet u controleren tegen het object in on-premises AD of in de [Metaverse Search voor synchronisatieservicebeheer.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch)

Als u de lijst met objecten in uw tenant met LargeObject-fouten wilt verkrijgen, gebruikt u een van de volgende methoden:

 * Als uw tenant is ingeschakeld voor Azure AD Connect-status voor synchronisatie, u verwijzen naar het [opgegeven melding van synchronisatiefouten.](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync)
 
 * De meldingse-mail voor adreslijstsynchronisatiefouten die aan het einde van elke synchronisatiecyclus wordt verzonden, bevat de lijst met objecten met LargeObject-fouten. 
 * Op [het tabblad Operations voor synchronisatieservicebeheer](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) worden de lijst met objecten met largeobjectfouten weergegeven als u op de nieuwste BEWERKING Exporteren naar Azure AD klikt.
 
## <a name="mitigation-options"></a>Mitigatieopties
Totdat de fout LargeObject is opgelost, kunnen andere kenmerkwijzigingen in hetzelfde object niet worden geëxporteerd naar Azure AD. Als u de fout wilt oplossen, u de volgende opties overwegen:

 * Upgrade Azure AD Connect naar build 1.1.524.0 of daarna. In Azure AD Connect build 1.1.524.0 zijn de out-of-box synchronisatieregels bijgewerkt om geen gebruikerscertificaat en userSMIMECertificate van gebruikers te exporteren als de kenmerken meer dan 15 waarden hebben. Raadpleeg het artikel [Azure AD Connect: Upgrade van een vorige versie naar de laatste versie voor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)meer informatie over het upgraden van Azure AD Connect.

 * Implementeer een **uitgaande synchronisatieregel** in Azure AD Connect die een null-waarde exporteert **in plaats van de werkelijke waarden voor objecten met meer dan 15 certificaatwaarden.** Deze optie is geschikt als u niet vereist dat een van de certificaatwaarden wordt geëxporteerd naar Azure AD voor objecten met meer dan 15 waarden. Raadpleeg de volgende sectie [Synchronisatieregel implementeren om de export van het kenmerk userCertificate te beperken](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute)voor meer informatie over het implementeren van deze synchronisatieregel.

 * Verminder het aantal certificaatwaarden op het on-premises AD-object (15 of minder) door waarden te verwijderen die niet meer door uw organisatie worden gebruikt. Dit is geschikt als de bloat van het kenmerk wordt veroorzaakt door verlopen of ongebruikte certificaten. U het [PowerShell-script dat hier beschikbaar is,](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) gebruiken om verlopen certificaten te vinden, te back-ups maken en verwijderen in uw on-premises AD. Voordat u de certificaten verwijderde, wordt aanbevolen om te verifiëren met de beheerders van de openbare sleutel-infrastructuur in uw organisatie.

 * Configureer Azure AD Connect om uit te sluiten dat het kenmerk userCertificate wordt geëxporteerd naar Azure AD. In het algemeen raden we deze optie niet aan, omdat het kenmerk door Microsoft Online Services kan worden gebruikt om specifieke scenario's in te schakelen. Met name:
    * Het kenmerk userCertificate op het object Gebruiker wordt gebruikt door Exchange Online- en Outlook-clients voor het ondertekenen en versleutelen van berichten. Voor meer informatie over deze functie verwijzen we je naar artikel [S/MIME voor het ondertekenen en versleutelen van berichten.](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx)

    * Het kenmerk userCertificate op het computerobject wordt door Azure AD gebruikt om apparaten die met een on-premises domein zijn verbonden met Windows 10 verbinding te maken met Azure AD. Raadpleeg artikel [Connect-apparaten met domeinverbinding verbinden met Azure AD voor Windows 10-ervaringen](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy)voor meer informatie over deze functie.

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Synchronisatieregel implementeren om het exporteren van het kenmerk UserCertificate te beperken
Als u de largeobjectfout als gevolg van het kenmerk userCertificate wilt oplossen, u een uitgaande synchronisatieregel implementeren in Azure AD Connect die een null-waarde exporteert **in plaats van de werkelijke waarden voor objecten met meer dan 15 certificaatwaarden.** In deze sectie worden de stappen beschreven die nodig zijn om de synchronisatieregel voor **gebruikersobjecten** te implementeren. De stappen kunnen worden aangepast voor **contact-** en **computerobjecten.**

> [!IMPORTANT]
> Als u null-waarde exporteert, worden certificaatwaarden verwijderd die eerder naar Azure AD zijn geëxporteerd.

De stappen kunnen worden samengevat als:

1. Schakel de synchronisatieplanner uit en controleer of er geen synchronisatie wordt uitgevoerd.
3. Zoek de bestaande uitgaande synchronisatieregel voor het kenmerk UserCertificate.
4. Maak de vereiste uitgaande synchronisatieregel.
5. Controleer de nieuwe synchronisatieregel op een bestaand object met de fout LargeObject.
6. Pas de nieuwe synchronisatieregel toe op resterende objecten met de fout LargeObject.
7. Controleer of er geen onverwachte wijzigingen wachten om te worden geëxporteerd naar Azure AD.
8. Exporteer de wijzigingen naar Azure AD.
9. Synchronisatieplanner opnieuw inschakelen.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Step 1. Synchronisatieplanner uitschakelen en controleren of er geen synchronisatie wordt uitgevoerd
Zorg ervoor dat er geen synchronisatie plaatsvindt terwijl u bezig bent met het implementeren van een nieuwe synchronisatieregel om te voorkomen dat onbedoelde wijzigingen worden geëxporteerd naar Azure AD. Ga als bedoeld als u de ingebouwde synchronisatieplanner uitschakelt:
1. PowerShell-sessie starten op de Azure AD Connect-server.

2. Geplande synchronisatie uitschakelen door cmdlet uit te voeren:`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> De voorgaande stappen zijn alleen van toepassing op nieuwere versies (1.1.xxx.x) van Azure AD Connect met de ingebouwde planner. Als u oudere versies (1.0.xxx.x) van Azure AD Connect gebruikt die Windows Taakplanner gebruiken, of als u uw eigen aangepaste planner (niet gebruikelijk) gebruikt om periodieke synchronisatie te activeren, moet u deze dienovereenkomstig uitschakelen.

1. Start de **Synchronisatieservicebeheer** door naar STARTEN → Synchronisatieservice te gaan.

1. Ga naar het tabblad **Bewerkingen** en bevestig dat er geen bewerking is waarvan de status *'aan de gang' is.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Stap 2. De bestaande regel voor uitgaande synchronisatie voor het kenmerk UserCertificate zoeken
Er moet een bestaande synchronisatieregel zijn die is ingeschakeld en geconfigureerd om het kenmerk userCertificate voor gebruikersobjecten te exporteren naar Azure AD. Zoek deze synchronisatieregel om de **voorrangs-** en **scopingfilterconfiguratie** te achterhalen:

1. Start de **editor synchronisatieregels** door naar TE GAAN → Synchronisatieregels editor.

2. Configureer de zoekfilters met de volgende waarden:

    | Kenmerk | Waarde |
    | --- | --- |
    | Richting |**Uitgaand** |
    | MV-objecttype |**Person** |
    | Connector |*naam van uw Azure AD-connector* |
    | Objecttype connector |**Gebruiker** |
    | MV-kenmerk MV |**userCertificate** |

3. Als u OOB-synchronisatieregels (out-of-box) gebruikt naar Azure AD-connector om het kenmerk UserCertficiate voor gebruikersobjecten te exporteren, moet u de regel *'Uit naar AAD – ExchangeOnline voor gebruikers'* terugkrijgen.
4. Noteer de **prioriteitswaarde** van deze synchronisatieregel.
5. Selecteer de synchronisatieregel en klik op **Bewerken**.
6. Klik in het pop-updialoogvenster *Bevestiging van gereserveerde regels bewerken* op **Nee**. (Maak je geen zorgen, we gaan geen wijzigingen aanbrengen in deze synchronisatieregel).
7. Selecteer in het bewerkingsscherm het **filtertabblad Scoping.**
8. Noteer de configuratie van het scopingfilter. Als u de OOB-synchronisatieregel gebruikt, moet er precies **één scopingfiltergroep zijn die twee clausules bevat,** waaronder:

    | Kenmerk | Operator | Waarde |
    | --- | --- | --- |
    | sourceObjectType | Gelijke | Gebruiker |
    | cloudMastered | NOTEQUAL NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Stap 3. De vereiste uitgaande synchronisatieregel maken
De nieuwe synchronisatieregel moet dezelfde **scopingfilter** en **hogere prioriteit** hebben dan de bestaande synchronisatieregel. Dit zorgt ervoor dat de nieuwe synchronisatieregel van toepassing is op dezelfde set objecten als de bestaande synchronisatieregel en overschrijft de bestaande synchronisatieregel voor het kenmerk userCertificate. Ga als u de synchronisatieregel maken:
1. Klik in de editor voor synchronisatieregels op de knop **Nieuwe regels toevoegen.**
2. Geef **onder het tabblad Beschrijving**de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | --- | --- | --- |
    | Name | *Een naam opgeven* | *Bijvoorbeeld:: "Out to AAD – Custom override for userCertificate"* |
    | Beschrijving | *Een beschrijving geven* | *Bijvoorbeeld:"Als het kenmerk UserCertificate meer dan 15 waarden heeft, exporteert u NULL."* |
    | Verbonden systeem | *De Azure AD-connector selecteren* |
    | Verbonden systeemobjecttype | **Gebruiker** | |
    | Metaverse objecttype | **Persoon** | |
    | Koppelingstype | **Toevoegen** | |
    | Prioriteit | *Kies een getal tussen 1 - 99* | Het gekozen getal mag niet worden gebruikt door een bestaande synchronisatieregel en heeft een lagere waarde (en dus een hogere prioriteit) dan de bestaande synchronisatieregel. |

3. Ga naar het **filtertabblad Scoping** en implementeer hetzelfde scopingfilter dat de bestaande synchronisatieregel gebruikt.
4. Sla het tabblad **Joinregels** over.
5. Ga naar het tabblad **Transformaties** om een nieuwe transformatie toe te voegen met de volgende configuratie:

    | Kenmerk | Waarde |
    | --- | --- |
    | Stroomtype |**Expressie** |
    | Kenmerk Target |**userCertificate** |
    | Bronkenmerk |*Gebruik de volgende expressie:*`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klik **op** de knop Toevoegen om de synchronisatieregel te maken.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Stap 4. De nieuwe synchronisatieregel voor een bestaand object met largeobjectfout verifiëren
Dit is om te controleren of de synchronisatieregel die is gemaakt correct werkt op een bestaand AD-object met largeobjectfout voordat u het toepast op andere objecten:
1. Ga naar het tabblad **Bewerkingen** in de synchronisatieservicemanager.
2. Selecteer de meest recente bewerking Exporteren naar Azure AD en klik op een van de objecten met LargeObject-fouten.
3.  Klik in het pop-upscherm Eigenschappen van verbindingsruimteobject op de knop **Voorbeeld.**
4. Selecteer in het pop-upscherm Voorbeeld de optie **Volledige synchronisatie** en klik op **Voorbeeld vastleggen**.
5. Sluit het scherm Voorbeeld en het scherm Eigenschappen van objecteigenschappen voor verbindingsruimte.
6. Ga naar het tabblad **Connectors** in de synchronisatieservicebeheer.
7. Klik met de rechtermuisknop op de **Azure AD** Connector en selecteer **Uitvoeren...**
8. Selecteer in de pop-up Connector uitvoeren de optie **Stap exporteren** en klik op **OK**.
9. Wacht tot exporteren naar Azure AD is voltooid en bevestig dat er geen LargeObject-fout meer is op dit specifieke object.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Stap 5. De nieuwe synchronisatieregel toepassen op resterende objecten met de fout LargeObject
Zodra de synchronisatieregel is toegevoegd, moet u een volledige synchronisatiestap uitvoeren op de AD-connector:
1. Ga naar het tabblad **Connectors** in de synchronisatieservicebeheer.
2. Klik met de rechtermuisknop op de **AD-connector** en selecteer **Uitvoeren...**
3. Selecteer in de pop-up Connector uitvoeren de optie **Volledige synchronisatiestap** en klik op **OK**.
4. Wacht tot de stap Volledige synchronisatie is voltooid.
5. Herhaal de bovenstaande stappen voor de resterende AD-connectors als u meer dan één AD-connectors hebt. Meestal zijn meerdere connectors vereist als u meerdere on-premises mappen hebt.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Stap 6. Controleren of er geen onverwachte wijzigingen moeten worden uitgevoerd naar Azure AD
1. Ga naar het tabblad **Connectors** in de synchronisatieservicebeheer.
2. Klik met de rechtermuisknop op de **Azure AD** Connector en selecteer **Zoekconnectorruimte**.
3. Ga als bedoeld in de pop-up Zoekconnectorruimte:
    1. Bereik instellen **op In behandeling zijnde export**.
    2. Schakel alle drie de selectievakjes in, inclusief **Toevoegen,** **Wijzigen** en **Verwijderen**.
    3. Klik op de knop **Zoeken** om alle objecten met wijzigingen die wachten om te worden geëxporteerd naar Azure AD, terug te sturen.
    4. Controleer of er geen onverwachte wijzigingen zijn. Als u de wijzigingen voor een bepaald object wilt onderzoeken, dubbelklikt u op het object.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Stap 7. De wijzigingen exporteren naar Azure AD
Ga als een andere toepassing op de wijzigingen naar Azure AD:
1. Ga naar het tabblad **Connectors** in de synchronisatieservicebeheer.
2. Klik met de rechtermuisknop op de **Azure AD** Connector en selecteer **Uitvoeren...**
4. Selecteer in de pop-up Connector uitvoeren de optie **Stap exporteren** en klik op **OK**.
5. Wacht tot exporteren naar Azure AD is voltooid en bevestig dat er geen LargeObject-fouten meer zijn.

### <a name="step-8-re-enable-sync-scheduler"></a>Stap 8. Synchronisatieplanner opnieuw inschakelen
Nu het probleem is opgelost, schakelt u de ingebouwde synchronisatieplanner opnieuw in:
1. PowerShell-sessie starten.
2. Geplande synchronisatie opnieuw inschakelen door cmdlet uit te voeren:`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> De voorgaande stappen zijn alleen van toepassing op nieuwere versies (1.1.xxx.x) van Azure AD Connect met de ingebouwde planner. Als u oudere versies (1.0.xxx.x) van Azure AD Connect gebruikt die Windows Taakplanner gebruiken, of als u uw eigen aangepaste planner (niet gebruikelijk) gebruikt om periodieke synchronisatie te activeren, moet u deze dienovereenkomstig uitschakelen.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

