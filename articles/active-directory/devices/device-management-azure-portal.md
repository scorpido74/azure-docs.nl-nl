---
title: Apparaten beheren met de Azure-portal | Microsoft Documenten
description: Meer informatie over het gebruik van de Azure-portal om apparaten te beheren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262241"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Apparaatidentiteiten beheren met behulp van de Azure-portal

Met apparaatidentiteitsbeheer in Azure Active Directory (Azure AD) u ervoor zorgen dat uw gebruikers toegang hebben tot uw bronnen vanaf apparaten die voldoen aan uw normen voor beveiliging en naleving.

Dit artikel:

- Gaat ervan uit dat u bekend bent met de [inleiding tot apparaatidentiteitsbeheer in Azure Active Directory](overview.md)
- Biedt u informatie over het beheren van uw apparaatidentiteit en met de Azure AD-portal

## <a name="manage-device-identities"></a>Apparaatidentiteiten beheren

De Azure AD-portal biedt u een centrale plek om uw apparaatidentiteiten te beheren. U naar deze plek door middel van een [directe link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) of:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar Azure Active > **Directory-apparaten**. **Azure Active Directory**

Op de pagina **Apparaten** u:

- Uw apparaatinstellingen configureren
- Apparaten zoeken
- Taken voor apparaatidentiteitsbeheer uitvoeren
- Controlevan apparaatgerelateerde controlelogboeken controleren  
  
## <a name="configure-device-settings"></a>Apparaatinstellingen configureren

Als u uw apparaatidentiteitwilt beheren met de Azure AD-portal, moeten uw apparaten zijn [geregistreerd of lid](overview.md) zijn geworden van Azure AD. Als beheerder u het proces van het registreren en samenvoegen van apparaten verfijnen door de apparaatinstellingen te configureren.

Op de pagina Apparaatinstellingen u instellingen configureren met betrekking tot apparaatidentiteiten:

![Een Intune-apparaat beheren](./media/device-management-azure-portal/21.png)

- **Gebruikers kunnen lid worden van apparaten met Azure AD** - Met deze instelling u de gebruikers selecteren die hun apparaten kunnen registreren als Azure AD-apparaten. De standaardwaarde is **alle**.

> [!NOTE]
> **Gebruikers kunnen lid worden van de** instelling voor Azure AD is alleen van toepassing op Het deelnemen aan Azure AD op Windows 10.

- **Extra lokale beheerders op Azure AD-apparaten :** u de gebruikers selecteren die lokale beheerdersrechten op een apparaat hebben gekregen. Gebruikers die hier zijn toegevoegd, worden toegevoegd aan de functie *Apparaatbeheerders* in Azure AD. Globale beheerders in Azure AD en apparaateigenaren krijgen standaard lokale beheerdersrechten. Deze optie is een premium-editiemogelijkheid die beschikbaar is via producten zoals Azure AD Premium of de Enterprise Mobility Suite (EMS).
- **Gebruikers kunnen hun apparaten registreren bij Azure AD** - U moet deze instelling configureren zodat apparaten voor persoonlijke, iOS-, Android- en MacOs-apparaten van Windows 10 kunnen worden geregistreerd bij Azure AD. Als u **Geen**selecteert, mogen apparaten zich niet registreren bij Azure AD. Voor inschrijving bij Microsoft Intune of Mobile Device Management (MDM) voor Office 365 is registratie vereist. Als u een van deze services hebt geconfigureerd, is **ALLES** geselecteerd en is **GEEN** niet beschikbaar.
- **Multi-Factor Auth vereisen om zich aan te sluiten bij apparaten** - U kiezen of gebruikers een extra verificatiefactor moeten opgeven om hun apparaat aan te sluiten bij Azure AD. De standaardinstelling is **Nee**. We raden u aan multi-factor authenticatie te vereisen bij het registreren van een apparaat. Voordat u meervoudige verificatie voor deze service inschakelt, moet u ervoor zorgen dat meervoudige verificatie is geconfigureerd voor de gebruikers die hun apparaten registreren. Zie Aan [de slag met Azure multi-factor authenticatie](../authentication/concept-mfa-whichversion.md)voor meer informatie over verschillende Azure-multifactorauthenticatieservices. 

> [!NOTE]
> **Multi-Factor Auth vereisen dat u zich aansluit bij de** instelling voor apparaten die zijn ingesteld op apparaten waarvoor Azure AD is samengevoegd of azure AD is geregistreerd. Deze instelling is niet van toepassing op hybride Azure AD-apparaten.

- **Maximaal aantal apparaten** Met deze instelling u het maximum aantal azure AD-geregistreerde apparaten selecteren dat een gebruiker kan hebben in Azure AD. Als een gebruiker dit quotum bereikt, kunnen ze geen extra apparaten toevoegen totdat een of meer van de bestaande apparaten zijn verwijderd. De standaardwaarde is **20**.

> [!NOTE]
> **De** instelling voor maximaal veel apparaten is van toepassing op apparaten waarvan Azure AD is toegetreden of Azure AD is geregistreerd. Deze instelling is niet van toepassing op hybride Azure AD-apparaten.

- **Gebruikers kunnen instellingen en app-gegevens op verschillende apparaten synchroniseren** - Standaard is deze instelling ingesteld op **GEEN**. Als u specifieke gebruikers of groepen selecteert of allen, kunnen de instellingen en app-gegevens van de gebruiker worden gesynchroniseerd op hun Windows 10-apparaten. Meer informatie over hoe synchronisatie werkt in Windows 10.
Deze optie is een premium mogelijkheid die beschikbaar is via producten zoals Azure AD Premium of de Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Apparaten zoeken

Je hebt twee opties om geregistreerde en samengevoegde apparaten te vinden:

- **Alle apparaten** in het gedeelte **Beheren** van de pagina **Apparaten**  
- **Apparaten** in de sectie **Beheren** van een **gebruikerspagina**

Met beide opties u een weergave krijgen die:

- Hiermee u zoeken naar apparaten met de weergavenaam of apparaat-id als filter.
- Biedt u een gedetailleerd overzicht van geregistreerde en samengevoegde apparaten
- Hiermee u veelvoorkomende apparaatbeheertaken uitvoeren

![Alle apparaten](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Als u een apparaat ziet dat is gekoppeld aan 'Hybride Azure AD' met een status 'In behandeling' onder de kolom GEREGISTREERD, geeft dit aan dat het apparaat is gesynchroniseerd vanuit Azure AD-verbinding en wacht op volledige registratie van de client. Lees meer over het [plannen van uw hybride Azure AD-implementatie.](hybrid-azuread-join-plan.md) Aanvullende informatie is te vinden in het artikel, [Apparaten veelgestelde vragen](faq.md).
>
>   ![Apparaten in behandeling](./media/device-management-azure-portal/75.png)
>
>* Voor sommige iOS-apparaten kunnen de apparaatnamen die apostrofs bevatten, mogelijk verschillende tekens gebruiken die op apostrofs lijken. Dus zoeken naar dergelijke apparaten is een beetje lastig - als je niet ziet zoekresultaten correct, ervoor te zorgen dat de zoekstring bevat bijpassende apostrof karakter.

## <a name="device-identity-management-tasks"></a>Taken voor apparaatidentiteitsbeheer

Als globale beheerder of beheerder van cloudapparaten u de geregistreerde of samengevoegde apparaten beheren. Beheerders van Intune-service kunnen:

- Apparaten bijwerken - Voorbeelden zijn dagelijkse bewerkingen, zoals het in- of uitschakelen van apparaten
- Apparaten verwijderen : wanneer een apparaat is uitgeschakeld en moet worden verwijderd in Azure AD

In deze sectie vindt u informatie over algemene apparaatidentiteitsbeheertaken.

### <a name="manage-an-intune-device"></a>Een Intune-apparaat beheren

Als u een Intune-beheerder bent, u apparaten beheren die zijn gemarkeerd als **Microsoft Intune.** Als het apparaat niet is ingeschreven bij Microsoft Intune, wordt de optie 'Beheren' grijs weergegeven.

![Een Intune-apparaat beheren](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Een Azure AD-apparaat in- of uitschakelen

Als u een apparaat wilt in- of uitschakelen, hebt u twee opties:

- Het takenmenu ("...") op de pagina **Alle apparaten**

   ![Een Intune-apparaat beheren](./media/device-management-azure-portal/71.png)

- De werkbalk op de pagina **Apparaten**

   ![Een Intune-apparaat beheren](./media/device-management-azure-portal/32.png)

**Opmerkingen:**

- U moet een globale beheerder of beheerder van een cloudapparaat in Azure AD zijn om een apparaat in te schakelen/ uit te schakelen. 
- Als u een apparaat uitschakelt, kan een apparaat niet met succes worden geauthenticeren met Azure AD, waardoor het apparaat geen toegang heeft tot uw Azure AD-bronnen die worden bewaakt door apparaat CA of uw WH4B-referenties gebruikt.
- Als u het apparaat uitschakelt, wordt zowel het Primaire refresh-token (PRT) als eventuele Vernieuwingstokens (RT) op het apparaat ingetrokken.

### <a name="delete-an-azure-ad-device"></a>Een Azure AD-apparaat verwijderen

Als u een apparaat wilt verwijderen, hebt u twee opties:

- Het takenmenu ("...") op de pagina **Alle apparaten**

   ![Een Intune-apparaat beheren](./media/device-management-azure-portal/72.png)

- De werkbalk op de pagina **Apparaten**

   ![Een apparaat verwijderen](./media/device-management-azure-portal/34.png)

**Opmerkingen:**

- U moet een globale beheerder of een Intune-beheerder in Azure AD zijn om een apparaat te verwijderen.
- Een apparaat verwijderen:
   - Hiermee voorkomt u dat een apparaat toegang krijgt tot uw Azure AD-bronnen.
   - Hiermee verwijdert u alle gegevens die aan het apparaat zijn gekoppeld, bijvoorbeeld BitLocker-sleutels voor Windows-apparaten.  
   - Vertegenwoordigt een niet-herstelbare activiteit en wordt niet aanbevolen, tenzij dit vereist is.

Als een apparaat wordt beheerd door een andere beheerautoriteit (bijvoorbeeld Microsoft Intune), controleert u of het apparaat is gewist / uitgeschakeld voordat het apparaat wordt verwijderd in Azure AD. Bekijk hoe u [verouderde apparaten beheert](device-management-azure-portal.md) voordat u apparaten verwijderd.

### <a name="view-or-copy-device-id"></a>Apparaat-id weergeven of kopiëren

U een apparaat-id gebruiken om de apparaat-id-gegevens op het apparaat te verifiëren of PowerShell te gebruiken tijdens het oplossen van problemen. Als u de kopieeroptie wilt openen, klikt u op het apparaat.

![Een apparaat-id weergeven](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-toetsen weergeven of kopiëren

U de BitLocker-sleutels bekijken en kopiëren om gebruikers te helpen hun versleutelde schijf te herstellen. Deze sleutels zijn alleen beschikbaar voor Windows-apparaten die zijn versleuteld en waarvan hun sleutels zijn opgeslagen in Azure AD. U deze sleutels kopiëren wanneer u toegang krijgt tot de gegevens van het apparaat.

![BitLocker-toetsen weergeven](./media/device-management-azure-portal/36.png)

Als u de BitLocker-sleutels wilt bekijken of kopiëren, moet u de eigenaar van het apparaat zijn of een gebruiker die ten minste één van de volgende rollen heeft toegewezen:

- Beheerder van cloudapparaten
- Globale beheerder
- Helpdeskbeheerder
- Intune-servicebeheerder
- Beveiligingsbeheer
- Beveiligingslezer

> [!NOTE]
> Hybride Azure AD Samengevoegde Windows 10-apparaten hebben geen eigenaar. Dus, als u op zoek bent naar een apparaat door de eigenaar en niet vinden, zoeken door het apparaat-ID.

## <a name="audit-logs"></a>Auditlogboeken

Apparaatactiviteiten zijn beschikbaar via de activiteitslogboeken. Deze logboeken omvatten activiteiten die worden geactiveerd door de apparaatregistratieservice en door gebruikers:

- Apparaat maken en toevoegen van eigenaren / gebruikers op het apparaat
- Wijzigingen in apparaatinstellingen
- Apparaatbewerkingen zoals het verwijderen of bijwerken van een apparaat

Uw toegangspunt tot de controlegegevens is **Controlelogboeken** in de sectie **Activiteit** van de pagina **Apparaten.**

Het controlelogboek heeft een standaardlijstweergave die wordt weergegeven:

- De datum en het tijdstip van het voorval
- De doelstellingen
- De initiatiefnemer / acteur (die) van een activiteit
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
- Geïnitieerd door (Acteur)

Naast de filters u ook zoeken naar specifieke items.

![Auditlogboeken](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Volgende stappen

[Verouderde apparaten beheren in Azure AD](manage-stale-devices.md)
