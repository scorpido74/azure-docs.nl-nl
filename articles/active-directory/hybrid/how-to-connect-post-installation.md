---
title: 'Azure AD Connect: volgende stappen en het beheren van Azure AD Connect | Microsoft Documenten'
description: Meer informatie over het uitbreiden van de standaardconfiguratie- en operationele taken voor Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261292"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Volgende stappen en het beheren van Azure AD Connect
Gebruik de operationele procedures in dit artikel om Azure Active Directory (Azure AD) Connect aan te passen aan de behoeften en vereisten van uw organisatie.  

## <a name="add-additional-sync-admins"></a>Extra synchronisatiebeheerders toevoegen
Standaard kunnen alleen de gebruiker die de installatie heeft uitgevoerd en lokale beheerders de geïnstalleerde synchronisatie-engine beheren. Als u extra mensen toegang wilt geven tot en de synchronisatieengine wilt beheren, zoekt u de groep met de naam ADSyncAdmins op de lokale server en voegt u deze toe aan deze groep.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Licenties toewijzen aan Azure AD Premium- en Enterprise Mobility Suite-gebruikers
Nu uw gebruikers zijn gesynchroniseerd met de cloud, moet u ze een licentie toewijzen, zodat ze aan de slag kunnen met cloud-apps zoals Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Een Azure AD Premium- of Enterprise Mobility Suite-licentie toewijzen

1. Meld u aan bij de Azure-portal als beheerder.
2. Selecteer aan de linkerkant **Active Directory**.
3. Dubbelklik op de **pagina Active Directory** op de map met de gebruikers die u wilt instellen.
4. Selecteer boven aan de pagina met de adreslijst **Licenties**.
5. Selecteer **op** de pagina Licenties de optie **Active Directory Premium** of Enterprise Mobility **Suite**en klik vervolgens op **Toewijzen**.
6. Selecteer in het dialoogvenster de gebruikers aan wie u een licentie wilt toewijzen en klik op het vinkje om de wijzigingen op te slaan.

## <a name="verify-the-scheduled-synchronization-task"></a>De geplande synchronisatietaak controleren
Gebruik de Azure-portal om de status van een synchronisatie te controleren.

### <a name="to-verify-the-scheduled-synchronization-task"></a>De geplande synchronisatietaak verifiëren
1. Meld u aan bij de Azure-portal als beheerder.
2. Selecteer aan de linkerkant **Active Directory**.
3. Selecteer Azure **AD Connect** aan de linkerkant
4. Let boven aan de pagina op de laatste synchronisatie.

![Synchronisatietijd van directory](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Een geplande synchronisatietaak starten
Als u een synchronisatietaak moet uitvoeren, u dit doen door:

1. Dubbelklik op de snelkoppeling naar het Azure AD Connect-bureaublad om de wizard te starten.
2. Klik **op Configureren**.
3. Selecteer in het taakscherm de **synchronisatieopties aanpassen** en klik op **Volgende**
4. Voer uw Azure AD-referenties in
5. Klik op **Volgende**. Klik op **Volgende**.  Klik op **Volgende**.
5.  Controleer in het scherm **Klaar om te configureren** of het **synchronisatieproces starten wanneer de configuratie** is voltooid, is ingeschakeld.
6.  Klik **op Configureren**.

Zie [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md)voor meer informatie over de synchronisatieplanner van Azure AD Connect.

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Extra taken beschikbaar in Azure AD Connect
Na de eerste installatie van Azure AD Connect u de wizard altijd opnieuw starten vanaf de startpagina of bureaubladsnelkoppeling van Azure AD Connect.  U zult merken dat het doorlopen van de wizard weer een aantal nieuwe opties biedt in de vorm van extra taken.  

De volgende tabel bevat een overzicht van deze taken en een korte beschrijving van elke taak.

![Lijst met extra taken](./media/how-to-connect-post-installation/addtasks2.png)

| Extra taak | Beschrijving |
| --- | --- |
|**Privacyinstellingen**|Bekijk welke telemetriegegevens worden gedeeld met Microsoft.|
|**Huidige configuratie weergeven**|Bekijk uw huidige Azure AD Connect-oplossing.  Dit omvat algemene instellingen, gesynchroniseerde mappen en synchronisatie-instellingen. |
| **Synchronisatieopties aanpassen** |Wijzig de huidige configuratie, zoals het toevoegen van extra Active Directory-forests aan de configuratie of het inschakelen van synchronisatieopties zoals het terugschrijven van gebruikers, groepen, apparaten of wachtwoorden. |
|**Apparaatopties configureren**|Apparaatopties beschikbaar voor synchronisatie|
|**Mapschema vernieuwen**|Hiermee u nieuwe on-premises directoryobjecten toevoegen voor synchronisatie|
|**Faseringsmodus configureren** |Fasegegevens die niet onmiddellijk worden gesynchroniseerd en niet worden geëxporteerd naar Azure AD of on-premises Active Directory.  Met deze functie u een voorbeeld van de synchronisaties bekijken voordat ze plaatsvinden. |
|**Aanmelding van gebruikers wijzigen**|De verificatiemethode wijzigen die gebruikers gebruiken om zich aan te melden|
|**Federatie beheren**|Uw AD FS-infrastructuur beheren, certificaten vernieuwen en AD FS-servers toevoegen|
|**Oplossen**|Hulp bij het oplossen van Azure AD Connect-problemen|

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
