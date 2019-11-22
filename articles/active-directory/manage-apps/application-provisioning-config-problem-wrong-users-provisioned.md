---
title: Er is een onjuiste set gebruikers ingericht voor een Azure AD Gallery-app
description: Meer informatie over hoe u kunt nagaan waarom een andere set gebruikers wordt ingericht voor een toepassing dan de toepassingen die u verwacht
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77e8f31bd0adeabea79cd11553fd77a3e5ab6404
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275780"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Er wordt een verkeerde set gebruikers ingericht voor een Azure AD-galerie toepassing

Welke gebruikers worden ingericht voor de app, worden voornamelijk bestuurd door de gebruikers en groepen die zijn **toegewezen** aan de toepassing.

Gebruik de volgende bronnen om te leren hoe u kunt controleren welke gebruikers en groepen zijn toegewezen aan een toepassing in Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Een gebruiker rechtstreeks toewijzen als beheerder

Voer de volgende stappen uit om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing:

1. Open de [ **Azure-portal** ](https://portal.azure.com/) en meld u aan als een **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Als u het deel venster **toewijzing toevoegen** wilt openen, klikt u op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** .

9. Klik op de **gebruikers en groepen** selector vanaf de **toevoegen toewijzing** deelvenster.

10. Typ in het **volledige naam** of **e-mailadres** van de gebruiker die u geïnteresseerd bent in toewijzen in de **zoeken op naam of e-mailadres** zoekvak.

11. Beweeg de muisaanwijzer over de **gebruiker** in de lijst om weer te geven een **selectievakje**. Klik op het selectievakje naast de profielfoto van de gebruiker of het logo om toe te voegen van de gebruiker naar de **geselecteerde** lijst.

12. **Optioneel:** als u wilt **toevoegen van meer dan één gebruiker**, type in een andere **volledige naam** of **e-mailadres** in de **zoeken op naam of e-mailadres** zoekvak en klik op het selectievakje voor deze gebruiker toevoegen aan de **geselecteerde** lijst.

13. Wanneer u klaar bent met gebruikers te selecteren, klikt u op de **Selecteer** knop aan de lijst met gebruikers en groepen die moeten worden toegewezen aan de toepassing wilt toevoegen.

14. **Optioneel:** klikt u op de **rol selecteren** selector in de **toevoegen toewijzing** deelvenster te selecteren van een rol toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de **toewijzen** knop om de toepassing aan de geselecteerde gebruikers te wijzen.

Als inrichten is geconfigureerd en al wordt uitgevoerd voor een app, moeten nieuwe gebruikers in ongeveer 10 minuten worden ingericht voor een toepassing. Raadpleeg de **controle logboeken** voor meer informatie.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Een groep rechtstreeks aan een toepassing toewijzen als beheerder

Voer de volgende stappen uit om een of meer groepen rechtstreeks toe te wijzen aan een toepassing:

1. Open de [ **Azure-portal** ](https://portal.azure.com/) en meld u aan als een **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Als u het deel venster **toewijzing toevoegen** wilt openen, klikt u op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** .

9. Klik op de **gebruikers en groepen** selector vanaf de **toevoegen toewijzing** deelvenster.

10. Typ in het **volledige groepsnaam** van de groep die u geïnteresseerd bent in toewijzen in de **zoeken op naam of e-mailadres** zoekvak.

11. Beweeg de muisaanwijzer over de **groep** in de lijst om weer te geven een **selectievakje**. Klik op het selectievakje naast de profielfoto van de groep of het logo om toe te voegen van de gebruiker naar de **geselecteerde** lijst.

12. **Optioneel:** als u wilt **toevoegen van meer dan één groep**, type in een andere **volledige groepsnaam** in de **zoeken op naam of e-mailadres** zoekvak typt, en Klik op het selectievakje in als u wilt toevoegen van deze groep de **geselecteerde** lijst.

13. Wanneer u klaar bent met groepen te selecteren, klikt u op de **Selecteer** knop aan de lijst met gebruikers en groepen die moeten worden toegewezen aan de toepassing wilt toevoegen.

14. **Optioneel:** klikt u op de **rol selecteren** selector in de **toevoegen toewijzing** deelvenster te selecteren van een rol toewijzen aan de groepen die u hebt geselecteerd.

15. Klik op de **toewijzen** om toe te wijzen van de toepassing aan de geselecteerde groepen.

Als inrichten is geconfigureerd en al wordt uitgevoerd voor een app, moeten nieuwe gebruikers in de groep ongeveer 10 minuten worden ingericht voor een toepassing. Raadpleeg de **controle logboeken** voor meer informatie.

>[!IMPORTANT]
>Het inrichten van de groeps naam en groeps gegevens, naast de leden, als deze worden ondersteund voor sommige toepassingen. U kunt deze functie in-of uitschakelen door de **toewijzing** voor groeps objecten die op het tabblad **inrichten** wordt weer gegeven in of uit te scha kelen. 
>
>

Als inrichtings groepen is ingeschakeld, moet u de kenmerk toewijzingen controleren om ervoor te zorgen dat er een geschikt veld wordt gebruikt voor de ' overeenkomende ID '. Deze overeenkomende ID kan de weergave naam of e-mail alias zijn. De groep en de bijbehorende leden worden niet ingericht als de overeenkomende eigenschap leeg is of niet is ingevuld voor een groep in azure AD.

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)
