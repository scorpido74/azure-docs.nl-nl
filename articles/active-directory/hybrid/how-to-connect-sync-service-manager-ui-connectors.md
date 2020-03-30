---
title: Connectors in de gebruikersinterface van Azure AD Synchronization Service Manager | Microsoft Docs'
description: Het tabblad Connectors in het synchronisatieservicebeheer voor Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae932191c7b76590ea217386dfd729add5566f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261045"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Connectors gebruiken met Azure AD Connect Sync Service Manager

![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Het tabblad Connectors wordt gebruikt om alle systemen te beheren waarmee de synchronisatieengine is verbonden.

## <a name="connector-actions"></a>Connectoracties
| Actie | Opmerking |
| --- | --- |
| Maken |Niet gebruiken. Gebruik de wizard Installatie om verbinding te maken met extra AD-forests. |
| Eigenschappen |Wordt gebruikt voor domein- en ORGANISATIE-filtering. |
| [Verwijderen](#delete) |Wordt gebruikt om de gegevens in de connectorruimte te verwijderen of om de verbinding met een forest te verwijderen. |
| [Run-profielen configureren](#configure-run-profiles) |Behalve voor domeinfiltering, niets om hier te configureren. U deze actie gebruiken om reeds geconfigureerde runprofielen weer te geven. |
| Voer |Gebruikt om een eenmalige run van een profiel te starten. |
| Stoppen |Hiermee wordt een connector gestopt waarop momenteel een profiel wordt uitgevoerd. |
| Exportconnector |Niet gebruiken. |
| Connector importeren |Niet gebruiken. |
| Connector bijwerken |Niet gebruiken. |
| Schema vernieuwen |Hiermee wordt het schema in de cache vernieuwd. Het heeft de voorkeur om de optie in de installatiewizard te gebruiken, omdat dat ook synchronisatieregels bijwerkt. |
| [Zoekconnectorruimte](#search-connector-space) |Wordt gebruikt om objecten te vinden en een object en de bijbehorende gegevens te volgen via het systeem. |

### <a name="delete"></a>Verwijderen
De verwijderactie wordt gebruikt voor twee verschillende dingen.  
![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

De optie **Connectorruimte verwijderen** verwijdert alleen alle gegevens, maar behoudt de configuratie.

Met de optie **Connector verwijderen en de verbindingsruimte** worden de gegevens en de configuratie verwijderd. Deze optie wordt gebruikt wanneer u geen verbinding meer wilt maken met een forest.

Beide opties synchroniseren alle objecten en werken de metaverse objecten bij. Deze actie is een langdurige bewerking.

### <a name="configure-run-profiles"></a>Run-profielen configureren
Met deze optie u de runprofielen zien die zijn geconfigureerd voor een connector.

![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Zoekconnectorruimte
De ruimteactie voor zoekconnectoren is handig om objecten te vinden en gegevensproblemen op te lossen.

![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Begin met het selecteren van een **bereik**. U zoeken op basis van gegevens (RDN, DN, Anker, Substructuur) of status van het object (alle andere opties).  
![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Als u bijvoorbeeld een substructuurzoekopdracht doet, krijgt u alle objecten in één organisatie.  
![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
In dit raster u een object selecteren, **eigenschappen**selecteren en [volgen](tshoot-connect-object-not-syncing.md) vanuit de bronconnectorruimte, via de metaverse en naar de doelconnectorruimte.

### <a name="changing-the-ad-ds-account-password"></a>Het wachtwoord voor het AD DS-account wijzigen
Als u het accountwachtwoord wijzigt, kan de synchronisatieservice geen wijzigingen meer importeren/exporteren naar on-premises AD.   U kunt het volgende te zien krijgen:

- De import/exportstap voor de AD-connector mislukt met fout 'no-start-credentials'.
- Onder Windows Event Viewer bevat het logboek van de toepassingsgebeurtenis een fout met gebeurtenis-id 6000 en het bericht 'De beheeragent 'contoso.com' kan niet worden uitgevoerd omdat de referenties ongeldig waren.'

Update het AD DS-gebruikersaccount als volgt bij om het probleem op te lossen:


1. Start de Synchronisatieservicebeheer (START → Synchronisatieservice).
</br>![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Ga naar het tabblad **Connectors.**
3. Selecteer de AD-connector die is geconfigureerd om het AD DS-account te gebruiken.
4. Selecteer **Eigenschappen**onder Acties .
5. Selecteer Verbinding maken met Active Directory Forest in het pop-updialoogvenster:
6. De naam Forest geeft de overeenkomstige ad-locatie aan.
7. De gebruikersnaam geeft het AD DS-account aan dat wordt gebruikt voor synchronisatie.
8. Voer het nieuwe wachtwoord van het AD DS-account in het tekstvak ![Azure AD Connect-synchronisatiesleutel van Azure in](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Klik op OK om het nieuwe wachtwoord op te slaan en start de synchronisatieservice opnieuw om het oude wachtwoord uit de geheugencache te verwijderen.



## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [synchronisatieconfiguratie van Azure AD Connect.](how-to-connect-sync-whatis.md)

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
