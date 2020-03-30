---
title: 'Azure AD Connect: gewenste gegevenslocatie configureren voor Office 365-resources'
description: Beschrijft hoe u uw Office 365-gebruikersbronnen dicht bij de gebruiker plaatsen met Azure Active Directory Connect-synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a71c5328c6fa85f85db4bd7e6103f6470b86d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258325"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Synchronisatie van Azure Active Directory Connect: gewenste gegevenslocatie configureren voor Office 365-bronnen
Het doel van dit onderwerp is om u te laten doorlopen hoe u het kenmerk configureert voor de gewenste gegevenslocatie in Azure Active Directory (Azure AD) Connect-synchronisatie. Wanneer iemand multigeo-mogelijkheden gebruikt in Office 365, gebruikt u dit kenmerk om de geolocatie van de Office 365-gegevens van de gebruiker aan te wijzen. (De termen *regio* en *geo* worden door elkaar gebruikt.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Synchronisatie van voorkeursgegevenslocatie inschakelen
Standaard bevinden Office 365-resources voor uw gebruikers zich in dezelfde geo als uw Azure AD-tenant. Als uw tenant zich bijvoorbeeld in Noord-Amerika bevindt, bevinden de Exchange-postvakken van de gebruikers zich ook in Noord-Amerika. Voor een multinationale organisatie is dit misschien niet optimaal.

Door het kenmerk **preferredDataLocation**in te stellen, u de geo van een gebruiker definiëren. U de Office 365-bronnen van de gebruiker, zoals het postvak en OneDrive, in dezelfde geo als de gebruiker hebben en nog steeds één tenant voor uw hele organisatie hebben.

> [!IMPORTANT]
> Multi-Geo is momenteel beschikbaar voor klanten met een actieve Enterprise Agreement en minimaal 500 Office 365 Services-abonnementen. Neem contact op met uw Microsoft-vertegenwoordiger voor meer informatie.
>
>

Een lijst met alle geo's voor Office 365 is te vinden in [Waar bevinden uw gegevens zich?](https://aka.ms/datamaps)

De geo's in Office 365 die beschikbaar zijn voor Multi-Geo zijn:

| Geo | preferredDataLocatiewaarde |
| --- | --- |
| Azië en Stille Oceaan | Apc |
| Australië | Aus |
| Canada | CAN |
| Europese Unie | EUR |
| Frankrijk | FRA |
| India | IND |
| Japan | JPN |
| Korea | KOR |
| Zuid-Afrika | ZAF ZAF |
| Verenigde Arabische Emiraten | Zijn |
| Verenigd Koninkrijk | Gbr |
| Verenigde Staten | NAM |

* Als een geo niet in deze tabel (bijvoorbeeld Zuid-Amerika) wordt vermeld, kan deze niet worden gebruikt voor Multi-Geo.

* Niet alle Office 365-workloads ondersteunen het gebruik van het instellen van de geo van een gebruiker.

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect-ondersteuning voor synchronisatie

Azure AD Connect ondersteunt synchronisatie van het **kenmerk preferredDataLocation** voor **gebruikersobjecten** in versie 1.1.524.0 en hoger. Met name:

* Het schema van het objecttype **Gebruiker** in de Azure AD Connector wordt uitgebreid met het kenmerk **preferredDataLocation.** Het kenmerk is van de tekenreeks met één waarde.
* Het schema van het objecttype **Persoon** in de metaverse wordt uitgebreid met het kenmerk **preferredDataLocation.** Het kenmerk is van de tekenreeks met één waarde.

Standaard is **preferredDataLocation** niet ingeschakeld voor synchronisatie. Deze functie is bedoeld voor grotere organisaties. Het Active Directory-schema in Windows Server 2019 heeft een kenmerk **msDS-preferredDataLocation** dat u hiervoor moet gebruiken. Als u het Active Directory-schema niet hebt bijgewerkt en dit niet doen, moet u een kenmerk identificeren dat de Office 365-geo voor uw gebruikers moet behouden. Dit zal voor elke organisatie anders zijn.

> [!IMPORTANT]
> Azure AD staat toe dat het **kenmerk preferredDataLocation** op **cloud Gebruikersobjecten** rechtstreeks worden geconfigureerd met Azure AD PowerShell. Als u dit kenmerk wilt configureren voor **gesynchroniseerde gebruikersobjecten,** moet u Azure AD Connect gebruiken.

Voordat u synchronisatie inschakelt:

* Als u het Active Directory-schema niet hebt geüpgraded naar 2019, bepaalt u vervolgens welk on-premises Active Directory-kenmerk als bronkenmerk moet worden gebruikt. Het moet van het type, **single-valued string**.
* Als u eerder het kenmerk **preferredDataLocation** hebt geconfigureerd op bestaande **gesynchroniseerde gebruikersobjecten** in Azure AD met Azure AD PowerShell, moet u de kenmerkwaarden backporten naar de bijbehorende **gebruikersobjecten** in on-premises Active Directory.

    > [!IMPORTANT]
    > Als u deze waarden niet backportt, verwijdert Azure AD Connect de bestaande kenmerkwaarden in Azure AD wanneer synchronisatie voor het kenmerk **preferredDataLocation** is ingeschakeld.

* Configureer nu het bronkenmerk op ten minste een aantal on-premises Active Directory-gebruikers. U dit later gebruiken voor verificatie.

In de volgende secties worden de stappen gegeven om synchronisatie van het kenmerk **preferredDataLocation** mogelijk te maken.

> [!NOTE]
> De stappen worden beschreven in de context van een Azure AD-implementatie met topologie met één forest en zonder aangepaste synchronisatieregels. Als u een topologie met meerdere foresten, aangepaste synchronisatieregels hebt geconfigureerd of een staging-server hebt, moet u de stappen dienovereenkomstig aanpassen.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Stap 1: Synchronisatieplanner uitschakelen en controleren of er geen synchronisatie aan de gang is
Om te voorkomen dat onbedoelde wijzigingen worden geëxporteerd naar Azure AD, moet u ervoor zorgen dat er geen synchronisatie plaatsvindt terwijl u bezig bent met het bijwerken van synchronisatieregels. Ga als bedoeld als u de ingebouwde synchronisatieplanner uitschakelt:

1. Start een PowerShell-sessie op de Azure AD Connect-server.
2. Schakel geplande synchronisatie uit door deze `Set-ADSyncScheduler -SyncCycleEnabled $false`cmdlet uit te voeren: .
3. Start de **synchronisatieservicebeheer** door naar **DE** > **Synchronisatieservice**te gaan.
4. Selecteer het tabblad **Bewerkingen** en controleer of er geen bewerking is uitgevoerd met de *status in uitvoering*.

![Schermafbeelding van Synchronisatieservicebeheer](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Stap 2: Het schema voor Active Directory vernieuwen
Als u het Active Directory-schema hebt bijgewerkt naar 2019 en Connect is geïnstalleerd vóór de schema-extensie, heeft de schemacache van Connect niet het bijgewerkte schema. U moet het schema vervolgens uit de wizard vernieuwen om het in de gebruikersinterface weer te geven.

1. Start de wizard Azure AD Connect vanaf het bureaublad.
2. Selecteer het optie **Overzichtsschema vernieuwen** en klik op **Volgende**.
3. Voer uw Azure AD-referenties in en klik op **Volgende**.
4. Controleer op de pagina **Directoryschema vernieuwen** of alle forests zijn geselecteerd en klik op **Volgende**.
5. Sluit de wizard wanneer u klaar bent.

![Schermafbeelding van Directory-schema vernieuwen in de wizard Verbinding maken](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Stap 3: Het bronkenmerk toevoegen aan het on-premises Active Directory Connector-schema
**Deze stap is alleen nodig als u Connect-versie 1.3.21 of ouder uitvoert. Als u op 1.4.18 of nieuwer bent, ga dan naar stap 5.**  
Niet alle Azure AD-kenmerken worden geïmporteerd in de on-premises Active Directory-connectorruimte. Als u hebt geselecteerd om een kenmerk te gebruiken dat niet standaard is gesynchroniseerd, moet u het importeren. Ga als u het bronkenmerk toevoegt aan de lijst met geïmporteerde kenmerken:

1. Selecteer het tabblad **Connectors** in de synchronisatieservicebeheer.
2. Klik met de rechtermuisknop op de on-premises Active Directory Connector en selecteer **Eigenschappen**.
3. Ga in het pop-updialoogvenster naar het tabblad **Kenmerken selecteren.**
4. Controleer of het bronkenmerk dat u hebt geselecteerd om te gebruiken, is ingeschakeld in de lijst met kenmerken. Als u uw kenmerk niet ziet, schakelt u het selectievakje **Alles weergeven** in.
5. Als u wilt opslaan, selecteert u **OK**.

![Schermafbeelding van het dialoogvenster Beheer van synchronisatieservicebeheer en eigenschappen](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Stap 4: **PreferredDataLocation** toevoegen aan het Azure AD Connector-schema
**Deze stap is alleen nodig als u Connect-versie 1.3.21 of ouder uitvoert. Als u op 1.4.18 of nieuwer bent, ga dan naar stap 5.**  
Standaard wordt het kenmerk **preferredDataLocation** niet geïmporteerd in de Azure AD Connector-ruimte. Ga als u deze wilt toevoegen aan de lijst met geïmporteerde kenmerken:

1. Selecteer het tabblad **Connectors** in de synchronisatieservicebeheer.
2. Klik met de rechtermuisknop op de Azure AD-connector en selecteer **Eigenschappen**.
3. Ga in het pop-updialoogvenster naar het tabblad **Kenmerken selecteren.**
4. Selecteer het kenmerk **preferredDataLocation** in de lijst.
5. Als u wilt opslaan, selecteert u **OK**.

![Schermafbeelding van het dialoogvenster Beheer van synchronisatieservicebeheer en eigenschappen](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Stap 5: Een binnenkomende synchronisatieregel maken
Met de binnenkomende synchronisatieregel kan de kenmerkwaarde van het bronkenmerk in on-premises Active Directory naar de metaverse stromen.

1. Start de **editor synchronisatieregels** door naar DE Editor**synchronisatieregels** **te starten** > .
2. Stel het zoekfilter **Richting** in als **binnenkomend**.
3. Als u een nieuwe inkomende regel wilt maken, selecteert u **Nieuwe regel toevoegen**.
4. Geef onder het tabblad **Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | --- | --- | --- |
    | Name | *Een naam opgeven* | Bijvoorbeeld'In van AD – Gebruiker preferredDataLocation' |
    | Beschrijving | *Een aangepaste beschrijving geven* |  |
    | Verbonden systeem | *De on-premises Active Directory-connector kiezen* |  |
    | Verbonden systeemobjecttype | **Gebruiker** |  |
    | Metaverse objecttype | **Person** |  |
    | Koppelingstype | **Toevoegen** |  |
    | Prioriteit | *Kies een getal tussen 1-99* | 1–99 is gereserveerd voor aangepaste synchronisatieregels. Kies geen waarde die wordt gebruikt door een andere synchronisatieregel. |

5. Houd het **Scoping-filter** leeg, om alle objecten op te nemen. Mogelijk moet u het scopingfilter aanpassen op basis van uw Azure AD Connect-implementatie.
6. Ga naar het **tabblad Transformatie**en implementeer de volgende transformatieregel:

    | Stroomtype | Kenmerk Target | Bron | Eén keer solliciteren | Tekst samenvoegen |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocatie | Het bronkenmerk kiezen | Niet ingeschakeld | Update |

7. Als u de inkomende regel wilt maken, selecteert u **Toevoegen**.

![Schermafbeelding van binnenkomende synchronisatieregel maken](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Stap 6: Een uitgaande synchronisatieregel maken
Met de uitgaande synchronisatieregel kan de kenmerkwaarde van het metaverse naar het kenmerk **preferredDataLocation** in Azure AD stromen:

1. Ga naar de **editor synchronisatieregels**.
2. Stel het zoekfilter **Richting** in als **Uitgaand**.
3. Selecteer **Nieuwe regel toevoegen**.
4. Geef onder het tabblad **Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | ----- | ------ | --- |
    | Name | *Een naam opgeven* | Bijvoorbeeld 'Uit naar Azure AD – User preferredDataLocation' |
    | Beschrijving | *Een beschrijving geven* ||
    | Verbonden systeem | *De Azure AD-connector selecteren* ||
    | Verbonden systeemobjecttype | **Gebruiker** ||
    | Metaverse objecttype | **Person** ||
    | Koppelingstype | **Toevoegen** ||
    | Prioriteit | *Kies een getal tussen 1-99* | 1–99 is gereserveerd voor aangepaste synchronisatieregels. Kies geen waarde die wordt gebruikt door een andere synchronisatieregel. |

5. Ga naar het **filtertabblad Scoping** en voeg één scopingfiltergroep toe met twee clausules:

    | Kenmerk | Operator | Waarde |
    | --- | --- | --- |
    | sourceObjectType | Gelijke | Gebruiker |
    | cloudMastered | NOTEQUAL NOTEQUAL | True |

    Scoping-filter bepaalt op welke Azure AD-objecten deze uitgaande synchronisatieregel wordt toegepast. In dit voorbeeld gebruiken we dezelfde synchronisatieregel voor scoping van OOB (Out to Azure AD – User Identity" (out-of-box). Hiermee wordt voorkomen dat de synchronisatieregel wordt toegepast op **gebruikersobjecten** die niet zijn gesynchroniseerd vanuit een on-premises Active Directory. Mogelijk moet u het scopingfilter aanpassen op basis van uw Azure AD Connect-implementatie.

6. Ga naar het tabblad **Transformatie** en implementeer de volgende transformatieregel:

    | Stroomtype | Kenmerk Target | Bron | Eén keer solliciteren | Tekst samenvoegen |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocatie | preferredDataLocatie | Niet ingeschakeld | Update |

7. Sluit **Toevoegen** om de uitgaande regel te maken.

![Schermafbeelding van uitgaande synchronisatieregel maken](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Stap 7: Volledige synchronisatiecyclus uitvoeren
In het algemeen is volledige synchronisatiecyclus vereist. Dit komt omdat u nieuwe kenmerken hebt toegevoegd aan zowel het Active Directory- als het Azure AD Connector-schema en aangepaste synchronisatieregels hebt geïntroduceerd. Controleer de wijzigingen voordat u ze exporteert naar Azure AD. U de volgende stappen gebruiken om de wijzigingen te verifiëren, terwijl u handmatig de stappen uitvoert die deel uitmaken van een volledige synchronisatiecyclus.

1. **Volledige import uitvoeren** op de on-premises Active Directory Connector:

   1. Ga naar het tabblad **Bewerkingen** in de synchronisatieservicemanager.
   2. Klik met de rechtermuisknop op de **on-premises Active Directory Connector**en selecteer **Uitvoeren**.
   3. Selecteer In het dialoogvenster **Volledige invoer**en selecteer **OK**.
   4. Wacht tot de bewerking is voltooid.

      > [!NOTE]
      > U de volledige import op de on-premises Active Directory Connector overslaan als het bronkenmerk al is opgenomen in de lijst met geïmporteerde kenmerken. Met andere woorden, u hoefde geen enkele wijziging aan te brengen tijdens stap 2 eerder in dit artikel.

2. **Volledige import uitvoeren** op de Azure AD Connector:

   1. Klik met de rechtermuisknop op de **Azure AD Connector**en selecteer **Uitvoeren**.
   2. Selecteer In het dialoogvenster **Volledige invoer**en selecteer **OK**.
   3. Wacht tot de bewerking is voltooid.

3. Controleer de wijzigingen van de synchronisatieregel op een bestaand **object gebruiker.**

   Het bronkenmerk van on-premises Active Directory en **preferredDataLocation** van Azure AD zijn geïmporteerd in elke respectievelijke connectorruimte. Voordat u verdergaat met de volledige synchronisatiestap, moet u een voorbeeld doen voor een bestaand **object van gebruiker** in de on-premises Active Directory Connector-ruimte. Het object dat u hebt gekozen, moet het bronkenmerk hebben ingevuld. Een geslaagdvoorbeeld met **preferredDataLocation** in de metaverse is een goede indicator dat u de synchronisatieregels correct hebt geconfigureerd. Zie [De wijziging verifiëren](how-to-connect-sync-change-the-configuration.md#verify-the-change)voor informatie over het doen van een voorbeeld.

4. **Volledige synchronisatie** uitvoeren op de on-premises Active Directory Connector:

   1. Klik met de rechtermuisknop op de **on-premises Active Directory Connector**en selecteer **Uitvoeren**.
   2. Selecteer in het dialoogvenster **Volledige synchronisatie**en selecteer **OK**.
   3. Wacht tot de bewerking is voltooid.

5. Controleer **in behandeling zijnde uitvoer** naar Azure AD:

   1. Klik met de rechtermuisknop op de **Azure AD Connector**en selecteer **Zoekconnectorruimte**.
   2. Ga als een in het dialoogvenster **Zoekconnectorruimte:**

        a. **Bereik** instellen **op In behandeling zijnde export**.<br>
        b. Schakel alle drie de selectievakjes in, waaronder **Toevoegen, Wijzigen en Verwijderen**.<br>
        c. Als u de lijst met objecten wilt weergeven met wijzigingen die moeten worden geëxporteerd, selecteert u **Zoeken**. Dubbelklik op het object om de wijzigingen voor een bepaald object te onderzoeken.<br>
        d. Controleer of de wijzigingen worden verwacht.

6. **Exporteren uitvoeren** op de **Azure AD-connector**

   1. Klik met de rechtermuisknop op de **Azure AD Connector**en selecteer **Uitvoeren**.
   2. Selecteer in het dialoogvenster **Connector uitvoeren** de optie **Exporteren**en selecteer **OK**.
   3. Wacht tot de bewerking is voltooid.

> [!NOTE]
> Mogelijk merkt u dat de stappen niet de volledige synchronisatiestap op de Azure AD Connector of de exportstap op de Active Directory Connector bevatten. De stappen zijn niet vereist, omdat de kenmerkwaarden alleen van on-premises Active Directory naar Azure AD stromen.

## <a name="step-8-re-enable-sync-scheduler"></a>Stap 8: Synchronisatieplanner opnieuw inschakelen
Schakel de ingebouwde synchronisatieplanner opnieuw in:

1. Start een PowerShell-sessie.
2. Geplande synchronisatie opnieuw inschakelen door deze cmdlet uit te voeren:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Stap 9: Het resultaat verifiëren
Het is nu tijd om de configuratie te verifiëren en in te schakelen voor uw gebruikers.

1. Voeg de geo toe aan het geselecteerde kenmerk van een gebruiker. De lijst met beschikbare geo's is te vinden in deze tabel.  
![Schermafbeelding van ad-kenmerk dat aan een gebruiker is toegevoegd](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Wacht tot het kenmerk is gesynchroniseerd met Azure AD.
3. Controleer met Exchange Online PowerShell of het postvakgebied correct is ingesteld.  
![Schermafbeelding van Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Ervan uitgaande dat uw tenant is gemarkeerd om deze functie te kunnen gebruiken, wordt het postvak verplaatst naar de juiste geo. Dit kan worden geverifieerd door te kijken naar de servernaam waar het postvak zich bevindt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Multi-Geo in Office 365:

* [Multi-Geo sessions bij Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo in OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi-Geo in SharePoint Online](https://aka.ms/SharePointMultiGeo)

Meer informatie over het configuratiemodel in de synchronisatieengine:

* Lees meer over het configuratiemodel in [Declaratieve provisioning begrijpen.](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Lees meer over de expressietaal in [Het begrijpen van declaratieve inrichtingsuitdrukkingen](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Overzichtsonderwerpen:

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
