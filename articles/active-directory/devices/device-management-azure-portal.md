---
title: Apparaten beheren met de Azure Portal | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Portal voor het beheren van apparaten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 241e5baf7f00bc9897e84fb0c10ac7db605fb712
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84628313"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Apparaat-id's beheren met de Azure Portal

Met apparaat-id-beheer in Azure Active Directory (Azure AD) kunt u ervoor zorgen dat uw gebruikers toegang krijgen tot uw resources vanaf apparaten die voldoen aan uw normen voor beveiliging en naleving.

Dit artikel:

- Er wordt van uitgegaan dat u bekend bent met de [Inleiding tot apparaat-id-beheer in azure Active Directory](overview.md)
- Biedt informatie over het beheren van uw apparaat-id's met behulp van de Azure AD-Portal

![Weer gave alle apparaten in de Azure Portal](./media/device-management-azure-portal/all-devices-azure-portal.png)

## <a name="manage-device-identities"></a>Apparaatidentiteiten beheren

De Azure AD-Portal biedt een centrale plek voor het beheren van uw apparaat-id's. U kunt deze locatie bereiken met behulp van een [directe koppeling](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) of:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **apparaten**.

Op de pagina **apparaten** kunt u het volgende doen:

- Apparaatinstellingen configureren
- Apparaten zoeken
- Identiteits beheer taken voor apparaten uitvoeren
- Audit logboeken met betrekking tot het apparaat controleren  
  
## <a name="configure-device-settings"></a>Apparaatinstellingen configureren

Als u uw apparaat-id's wilt beheren met de Azure AD-Portal, moeten uw apparaten zijn [geregistreerd of zijn gekoppeld](overview.md) aan Azure AD. Als beheerder kunt u het proces voor het registreren en toevoegen van apparaten nauw keurig afstemmen door de apparaatinstellingen te configureren.

Op de pagina Apparaatinstellingen kunt u instellingen configureren met betrekking tot apparaat-id's:

![Apparaatinstellingen met betrekking tot Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Gebruikers kunnen apparaten toevoegen aan Azure AD** : met deze instelling kunt u de gebruikers selecteren die hun apparaten als aan Azure AD gekoppelde apparaten kunnen registreren. De standaardwaarde is **alle**.

> [!NOTE]
> **Gebruikers mogen apparaten toevoegen aan de Azure AD** -instelling is alleen van toepassing op Azure AD-deelname op Windows 10.

- **Aanvullende lokale beheerders op aan Azure AD gekoppelde apparaten** : u kunt de gebruikers selecteren die lokale beheerders rechten op een apparaat krijgen. Gebruikers die hier worden toegevoegd, worden toegevoegd aan de rol van het *apparaat Administrators* in azure AD. Globale beheerders in azure AD en eigen aren van apparaten krijgen standaard lokale beheerders rechten. Deze optie is een Premium Edition-mogelijkheid die beschikbaar is via producten als Azure AD Premium of de Enter prise Mobility Suite (EMS).
- **Gebruikers kunnen hun apparaten registreren bij Azure AD** . u moet deze instelling configureren zodat Windows 10 Personal-, Ios-, Android-en macOS-apparaten kunnen worden geregistreerd bij Azure AD. Als u **geen**selecteert, mogen apparaten zich niet registreren bij Azure AD. Inschrijving met Microsoft Intune of Mobile Device Management (MDM) voor Office 365 vereist registratie. Als u een van deze services hebt geconfigureerd, is **Alles** **geselecteerd en is er geen beschikbaar** .
- **Multi-factor Authentication vereisen voor het toevoegen van apparaten** : u kunt kiezen of gebruikers een extra verificatie factor moeten opgeven om hun apparaat toe te voegen aan Azure AD. De standaard waarde is **Nee**. U wordt aangeraden multi-factor Authentication te vereisen bij het registreren van een apparaat. Voordat u multi-factor Authentication inschakelt voor deze service, moet u ervoor zorgen dat multi-factor Authentication is geconfigureerd voor de gebruikers die hun apparaten registreren. Zie aan de slag [met Azure multi-factor Authentication](../authentication/concept-mfa-whichversion.md)voor meer informatie over de verschillende Azure multi-factor Authentication-Services. 

> [!NOTE]
> **Vereisen dat multi-factor Authentication wordt toegevoegd** aan de instelling apparaten is van toepassing op apparaten die zijn opgenomen in azure AD of Azure AD. Deze instelling is niet van toepassing op hybride apparaten die deel uitmaken van Azure AD.

- **Maximum aantal apparaten** : met deze instelling kunt u het maximum aantal aan Azure AD gekoppelde of Azure AD geregistreerde apparaten selecteren dat een gebruiker in azure AD kan hebben. Als een gebruiker dit quotum bereikt, kunnen ze geen extra apparaten toevoegen totdat een of meer van de bestaande apparaten worden verwijderd. De standaard waarde is **20**.

> [!NOTE]
> De instelling **maximum aantal apparaten** is van toepassing op apparaten die zijn geregistreerd bij Azure AD of Azure AD. Deze instelling is niet van toepassing op hybride apparaten die deel uitmaken van Azure AD.

- **Gebruikers kunnen instellingen en app-gegevens synchroniseren op alle apparaten** . deze instelling is standaard ingesteld op **geen**. Als u specifieke gebruikers of groepen selecteert, kunnen de instellingen en app-gegevens van de gebruiker worden gesynchroniseerd op hun Windows 10-apparaten. Meer informatie over hoe synchronisatie werkt in Windows 10.
Deze optie is een Premium-functie die beschikbaar is via producten als Azure AD Premium of de Enter prise Mobility Suite (EMS).

## <a name="locate-devices"></a>Apparaten zoeken

U hebt twee opties voor het zoeken van geregistreerde en gekoppelde apparaten:

- **Alle apparaten** in het gedeelte **beheren** van de pagina **apparaten**  
- **Apparaten** in het gedeelte **beheren** van een **gebruikers** pagina

Met beide opties kunt u een weer gave openen die:

- Met kunt u zoeken naar apparaten met behulp van de weergave naam of apparaat-ID als filter.
- Biedt een gedetailleerd overzicht van geregistreerde en gekoppelde apparaten
- Hiermee kunt u algemene beheer taken voor apparaten uitvoeren

>[!TIP]
>
>* Als u een apparaat ziet dat ' hybride Azure AD join ' met de status ' in behandeling ' heeft in de kolom geregistreerd, geeft dit aan dat het apparaat is gesynchroniseerd vanuit Azure AD Connect en wacht op het volt ooien van de registratie van de client. Meer informatie over het [plannen van uw hybride Azure AD-koppelings implementatie](hybrid-azuread-join-plan.md). Meer informatie vindt u in het artikel, [Veelgestelde vragen over apparaten](faq.md).
>
>* Voor sommige iOS-apparaten kunnen de apparaatnamen die apostrofs bevatten mogelijk andere tekens gebruiken die eruitzien als apostrofs. Het is dus lastig om dergelijke apparaten te doorzoeken: als u de zoek resultaten niet op de juiste manier ziet, moet u ervoor zorgen dat de zoek reeks overeenkomt met het apostrof-teken.

## <a name="device-identity-management-tasks"></a>Taken voor het beheer van apparaat-id's

Als globale beheerder of beheerder van een Cloud apparaat kunt u de geregistreerde of gekoppelde apparaten beheren. InTune-service beheerders kunnen:

- Apparaten bijwerken: voor beelden zijn dagelijkse bewerkingen, zoals het inschakelen/uitschakelen van apparaten
- Apparaten verwijderen: wanneer een apparaat buiten gebruik wordt gesteld en in azure AD moet worden verwijderd

In deze sectie vindt u informatie over algemene beheer taken voor identiteiten van apparaten.

### <a name="manage-an-intune-device"></a>Een intune-apparaat beheren

Als u intune-beheerder bent, kunt u apparaten beheren die zijn gemarkeerd als **Microsoft intune**. Als het apparaat niet is inge schreven bij Microsoft Intune, wordt de optie beheren grijs weer gegeven.

![Een intune-apparaat beheren](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Een Azure AD-apparaat in-of uitschakelen

Als u een apparaat wilt in-of uitschakelen, hebt u twee opties:

- Het menu taken (...) op de pagina **alle apparaten**

   ![Een intune-apparaat beheren](./media/device-management-azure-portal/71.png)

- De werk balk op de pagina **apparaten**

   ![Een intune-apparaat beheren](./media/device-management-azure-portal/32.png)

**Opmerkingen**

- U moet een globale beheerder of een beheerder van een Cloud apparaat in azure AD zijn om een apparaat in-of uit te scha kelen. 
- Als u een apparaat uitschakelt, kan een apparaat niet worden geverifieerd met Azure AD, waardoor het apparaat geen toegang heeft tot uw Azure AD-resources die worden beschermd door de CA van het apparaat of het gebruik van uw WH4B-referenties.
- Als u het apparaat uitschakelt, worden zowel het primaire vernieuwings token (PRT) als een vernieuwings token (RT) op het apparaat ingetrokken.

### <a name="delete-an-azure-ad-device"></a>Een Azure AD-apparaat verwijderen

Als u een apparaat wilt verwijderen, hebt u twee opties:

- Het menu taken (...) op de pagina **alle apparaten**

   ![Een intune-apparaat beheren](./media/device-management-azure-portal/72.png)

- De werk balk op de pagina **apparaten**

   ![Een apparaat verwijderen](./media/device-management-azure-portal/34.png)

**Opmerkingen**

- Als u een apparaat wilt verwijderen, moet u een globale beheerder of intune-beheerder zijn in azure AD.
- Een apparaat verwijderen:
   - Hiermee voor komt u dat een apparaat toegang heeft tot uw Azure AD-resources.
   - Hiermee verwijdert u alle details die zijn gekoppeld aan het apparaat, bijvoorbeeld BitLocker-sleutels voor Windows-apparaten.  
   - Vertegenwoordigt een niet-herstel bare activiteit en wordt niet aanbevolen, tenzij dit vereist is.

Als een apparaat wordt beheerd door een andere beheer instantie (bijvoorbeeld Microsoft Intune), moet u ervoor zorgen dat het apparaat is gewist/buiten gebruik is gesteld voordat het apparaat in azure AD wordt verwijderd. Lees hoe u [verouderde apparaten beheert](manage-stale-devices.md) voordat u apparaten verwijdert.

### <a name="view-or-copy-device-id"></a>Apparaat-ID weer geven of kopiëren

U kunt een apparaat-ID gebruiken om de details van de apparaat-ID op het apparaat te controleren of door Power shell te gebruiken tijdens het oplossen van problemen. Als u de Kopieer optie wilt openen, klikt u op het apparaat.

![Een apparaat-ID weer geven](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-sleutels weer geven of kopiëren

U kunt de BitLocker-sleutels weer geven en kopiëren om gebruikers te helpen hun versleutelde station te herstellen. Deze sleutels zijn alleen beschikbaar voor Windows-apparaten die zijn versleuteld en waarvan de sleutels zijn opgeslagen in azure AD. U kunt deze sleutels kopiëren bij het openen van Details van het apparaat.

![BitLocker-sleutels weer geven](./media/device-management-azure-portal/36.png)

Als u de BitLocker-sleutels wilt bekijken of kopiëren, moet u eigenaar zijn van het apparaat of een gebruiker zijn die ten minste een van de volgende rollen heeft:

- Beheerder van Cloud apparaat
- Globale beheerder
- Helpdesk beheerder
- Intune-servicebeheerder
- Beveiligingsbeheer
- Beveiligingslezer

> [!NOTE]
> Hybride Azure AD-gekoppelde Windows 10-apparaten hebben geen eigenaar. Als u dus op zoek bent naar een apparaat op basis van de eigenaar en niet hebt gevonden, zoekt u op de apparaat-ID.

### <a name="device-list-filtering-preview"></a>Filteren van apparaten lijst (preview-versie)

Voorheen kon u de lijst met apparaten alleen filteren op activiteit en status ingeschakeld. Met deze preview-versie kunt u nu de lijst met apparaten filteren op de volgende kenmerken op een apparaat:

- Ingeschakelde status
- Status conform
- Koppelings type (Azure AD toegevoegd, hybride Azure AD toegevoegd, Azure AD geregistreerd)
- Tijdstempel van activiteit
- Besturingssysteem
- Apparaattype (printers, beveiligde Vm's, gedeelde apparaten, geregistreerde apparaten)

De functie voor beeld filtering inschakelen in de weer gave **alle apparaten** :

![Preview-functionaliteit voor filteren inschakelen](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **apparaten**.
1. Selecteer de banner waarin wordt **vermeld, probeer de nieuwe verbeteringen voor het filteren van apparaten uit. Klik om de preview-versie in te scha kelen.**

U kunt nu **filters toevoegen** aan uw weer gave **alle apparaten** .

## <a name="audit-logs"></a>Auditlogboeken

Apparaat-activiteiten zijn beschikbaar via de activiteiten Logboeken. Deze logboeken bevatten activiteiten die worden geactiveerd door de Device Registration service en door gebruikers:

- Apparaten maken en toevoegen van eigen aren/gebruikers op het apparaat
- Wijzigingen in Apparaatinstellingen
- Apparaat-bewerkingen, zoals het verwijderen of bijwerken van een apparaat

Uw ingangs punt voor de controle gegevens is **controle logboeken** in het gedeelte **activiteit** van de pagina **apparaten** .

Het controle logboek heeft een standaard lijst weergave waarin het volgende wordt weer gegeven:

- De datum en tijd waarop de gebeurtenis is opgetreden
- De doelen
- De initiator/Actor (wie) van een activiteit
- De activiteit (wat)

![Auditlogboeken](./media/device-management-azure-portal/63.png)

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Auditlogboeken](./media/device-management-azure-portal/64.png)

Als u de gerapporteerde gegevens wilt beperken tot een niveau dat geschikt is voor u, kunt u de controlegegevens filteren met de volgende velden:

- Categorie
- Resourcetype van activiteit
- Activiteit
- Datumbereik
- Doel
- Gestart door (actor)

Naast de filters kunt u zoeken naar specifieke vermeldingen.

![Auditlogboeken](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Volgende stappen

[Verouderde apparaten beheren in azure AD](manage-stale-devices.md)
