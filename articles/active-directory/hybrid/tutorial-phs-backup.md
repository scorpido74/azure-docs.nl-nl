---
title: 'Zelf studie: PHS instellen als back-up voor AD FS in Azure AD Connect | Microsoft Docs'
description: Laat u zien hoe u wachtwoord-hashsynchronisatie als een back-up en voor AD FS inschakelt.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "64918995"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Zelf studie: PHS instellen als back-up voor AD FS in Azure AD Connect

De volgende zelfstudie begeleidt u bij het instellen van wachtwoord-hashsynchronisatie als een back-up en failover voor AD FS.  In dit document wordt ook gedemonstreerd hoe u wachtwoord-hashsynchronisatie als primaire verificatiemethode inschakelt, als AD FS is mislukt of niet beschikbaar is.

>[!NOTE] 
>Hoewel deze stappen doorgaans worden uitgevoerd tijdens nood situaties of onderbrekingen, is het raadzaam om deze stappen te testen en de procedures te controleren voordat er een storing optreedt.

>[!NOTE]
>In het geval dat u geen toegang hebt tot Azure AD Connect server of de server geen toegang heeft tot internet, kunt u contact opnemen met [Microsoft ondersteuning](https://support.microsoft.com/en-us/contactus/) om de wijzigingen aan de Azure AD-kant te helpen.

## <a name="prerequisites"></a>Vereisten
Deze zelf studie bouwt voort op de [zelf studie: Eén AD-Forest-omgeving naar de Cloud](tutorial-federation.md) verzenden en is een vereiste voor het uitvoeren van deze zelf studie.  Als u deze zelfstudie niet hebt voltooid, moet u dit doen voordat u de stappen in dit document gaat uitvoeren.

>[!IMPORTANT]
>Voordat u overschakelt naar PHS, moet u een back-up maken van uw AD FS omgeving.  U kunt dit doen met behulp van de [AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>WHS inschakelen in Azure AD Connect
Nu we een Azure AD Connect-omgeving die gebruikmaakt van federatie hebben, bestaat de eerste stap uit het inschakelen van wachtwoord-hashsynchronisatie en Azure AD Connect in staat tellen om de hashes te synchroniseren.

Ga als volgt te werk:

1.  Dubbelklik op het pictogram voor Azure AD Connect dat op het bureaublad is gemaakt
2.  Klik op **configureren**.
3.  Selecteer op de pagina Extra taken de optie **Synchronisatieopties aanpassen** en klik op **Volgende**.
4.  Voer de gebruikersnaam en het wachtwoord voor uw globale beheerder in.  Dit account is [hier](tutorial-federation.md#create-a-global-administrator-in-azure-ad) gemaakt in de vorige zelfstudie.
5.  Klik in het scherm **Verbinding maken met uw directory’s** op **Volgende**.
6.  Klik op het scherm **filteren op domein en OE** op **volgende**.
7.  Schakel in het scherm **Optionele functies** het selectievakje **Wachtwoord-hashsynchronisatie** in en klik op **Volgende**.
![Selecteren](media/tutorial-phs-backup/backup1.png)</br>
8.  Klik in het scherm **gereed voor configuratie** op **configureren**.
9.  Klik zodra de configuratie is voltooid op **Afsluiten**.
10. Dat is alles.  U bent klaar.  Wachtwoord-hashsynchronisatie wordt nu uitgevoerd en kan worden gebruikt als een back-up als AD FS niet beschikbaar is.

## <a name="switch-to-password-hash-synchronization"></a>Overschakelen naar wachtwoord-hashsynchronisatie
We laten u nu zien hoe u overschakelt naar wachtwoord-hashsynchronisatie. Voordat u begint, moet u overwegen onder welke omstandigheden u gaat overschakelen. Schakel niet over voor tijdelijke redenen, zoals netwerkstoringen, een klein probleem met AD FS of een probleem dat betrekking heeft op een subset van uw gebruikers. Als u besluit te maken over te schakelen omdat het oplossen van het probleem te lang duurt, doet u het volgende:

> [!IMPORTANT]
> Houd er rekening mee dat het enige tijd kan duren voordat de wacht woord-hashes worden gesynchroniseerd met Azure AD.  Dit betekent dat het 3 uur kan duren voordat de synchronisatie is voltooid en voordat u kunt verifiëren met behulp van de wacht woord-hashes.

1. Dubbelklik op het pictogram voor Azure AD Connect dat op het bureaublad is gemaakt
2.  Klik op **configureren**.
3.  Selecteer **Gebruikersaanmelding wijzigen** en klik op **Volgende**.
![Wijzigen](media/tutorial-phs-backup/backup2.png)</br>
4.  Voer de gebruikersnaam en het wachtwoord voor uw globale beheerder in.  Dit account is [hier](tutorial-federation.md#create-a-global-administrator-in-azure-ad) gemaakt in de vorige zelfstudie.
5.  Selecteer in het scherm **Aanmelding door gebruiker** de optie **Wachtwoord-hashsynchronisatie** en plaats een vinkje in het vak **Gebruikersaccounts niet converteren**.  
6.  Laat de standaardwaarde **Eenmalige aanmelding inschakelen** geselecteerd en klik op **Volgende**.
7.  Klik in het scherm **Eenmalige aanmelding** inschakelen op **Volgende**.
8.  Klik in het scherm **Gereed om te configureren** op **Configureren**.
9.  Zodra de configuratie is voltooid, klikt u op **Afsluiten**.
10. Gebruikers kunnen nu hun wachtwoord gebruiken om zich aan te melden bij Azure en Azure-services.

## <a name="test-signing-in-with-one-of-our-users"></a>Aanmelden testen met een van onze gebruikers

1. Bladeren naar[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Meld u aan met een gebruikersaccount dat is gemaakt in onze nieuwe tenant.  U moet zich aanmelden met de volgende indeling: (user@domain.onmicrosoft.com). Gebruik hetzelfde wachtwoord dat de gebruiker gebruikt om zich on-premises aan te melden.</br>
   ![Verifiëren](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Ga terug naar Federatie
Nu wordt uitgelegd hoe u terugschakelt naar Federatie.  Ga als volgt te werk om dit te doen:

1.  Dubbelklik op het pictogram voor Azure AD Connect dat op het bureaublad is gemaakt
2.  Klik op **configureren**.
3.  Selecteer **Gebruikersaanmelding wijzigen** en klik op **Volgende**.
4.  Voer de gebruikersnaam en het wachtwoord voor uw globale beheerder in.  Dit is het account dat [u in de](tutorial-federation.md#create-a-global-administrator-in-azure-ad) vorige zelf studie hebt gemaakt.
5.  Selecteer op het aanmeldings scherm van de **gebruiker** de optie **Federatie met AD FS** en klik op **volgende**.  
6. Voer op de pagina Referenties van domeinadministrator de gebruikersnaam en het wachtwoord voor contoso\Administrator in en klik op **Volgende.**
7. Klik in het scherm AD FS Farm op **volgende**.
8. Selecteer in het venster **Azure AD-domein** het domein in de vervolg keuzelijst en klik op **volgende**.
9. Klik in het scherm **Gereed om te configureren** op **Configureren**.
10. Zodra de configuratie is voltooid, klikt u op **volgende**.
![Configureren](media/tutorial-phs-backup/backup4.png)</br>
11. Klik in het scherm **Federatie connectiviteit controleren** op **controleren**.  Mogelijk moet u DNS-records (add A-en AAAA-records) configureren om deze te volt ooien.
![Verifiëren](media/tutorial-phs-backup/backup5.png)</br>
12. Klik op **Afsluiten**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>De AD FS-en Azure-vertrouwens relatie opnieuw instellen
Nu moeten we de vertrouwens relatie tussen AD FS en Azure opnieuw instellen.

1.  Dubbelklik op het pictogram voor Azure AD Connect dat op het bureaublad is gemaakt
2.  Klik op **configureren**.
3.  Selecteer **Federatie beheren** en klik op **volgende**.
4.  Selecteer **Azure AD-vertrouwens relatie opnieuw instellen** en klik op **volgende**.
![Opnieuw instellen](media/tutorial-phs-backup/backup6.png)</br>
5.  Voer in het scherm **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in voor uw globale beheerder.
6.  Voer in het scherm **verbinding maken met AD FS** de gebruikers naam en het wacht woord contoso\Administrator in en klik op **Volgende.**
7.  Klik in het scherm **certificaten** op **volgende**.

## <a name="test-signing-in-with-one-of-our-users"></a>Aanmelden testen met een van onze gebruikers

1.  Bladeren naar[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Meld u aan met een gebruikersaccount dat is gemaakt in onze nieuwe tenant.  U moet zich aanmelden met de volgende indeling: (user@domain.onmicrosoft.com). Gebruik het wachtwoord waarmee de gebruiker zich on-premises aanmeldt.
![Verifiëren](media/tutorial-password-hash-sync/verify1.png)

U hebt nu een omgeving met een hybride identiteit ingesteld die u kunt gebruiken voor testdoeleinden en om bekend te raken met wat Azure te bieden heeft.

## <a name="next-steps"></a>Volgende stappen


- [Hardware en vereisten](how-to-connect-install-prerequisites.md) 
- [Snelle instellingen](how-to-connect-install-express.md)
- [Synchronisatie van wachtwoord-hashes](how-to-connect-password-hash-synchronization.md)
