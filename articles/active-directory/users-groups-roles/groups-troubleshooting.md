---
title: Problemen met dynamische groepslidmaatschappen oplossen - Azure AD | Microsoft Documenten
description: Tips voor probleemoplossing voor dynamisch groepslidmaatschap in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026549"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Problemen met groepen oplossen en oplossen

## <a name="troubleshooting-group-creation-issues"></a>Problemen met het maken van groepen oplossen

**Ik heb het maken van beveiligingsgroepen uitgeschakeld in de Azure-portal, maar groepen kunnen nog steeds worden gemaakt via Powershell** De **gebruiker kan beveiligingsgroepen maken in Azure-portalen** die in de Azure-portal instellen, bepaalt of niet-beheerders beveiligingsgroepen kunnen maken in het Access-paneel of de Azure-portal. Het heeft geen controle over het maken van beveiligingsgroepen via Powershell.

Ga als het gaat om het maken van groepen voor niet-beheerders in Powershell uit te schakelen:
1. Controleer of niet-beheerders groepen mogen maken:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Als het `UsersPermissionToCreateGroupsEnabled : True`terugkeert, kunnen niet-beheerdersgroepen maken. Ga als volgt te werk om deze functie uit te schakelen:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Ik heb een fout met maximaal groepen ontvangen bij het maken van een dynamische groep in Powershell**<br/>
Als u een bericht ontvangt in Powershell met vermelding van _dynamisch groepsbeleid dat maximaal toegestane groepen hebt bereikt,_ betekent dit dat u de maximumlimiet voor dynamische groepen in uw tenant hebt bereikt. Het maximum aantal Dynamische groepen per tenant is 5.000.

Als u nieuwe dynamische groepen wilt maken, moet u eerst enkele bestaande dynamische groepen verwijderen. Er is geen manier om de limiet te verhogen.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Oplossen van problemen met dynamische lidmaatschappen voor groepen

**Ik heb een regel voor een groep geconfigureerd, maar er worden geen lidmaatschappen bijgewerkt in de groep**<br/>
1. Controleer de waarden voor gebruikers- of apparaatkenmerken in de regel. Zorg ervoor dat er gebruikers zijn die aan de regel voldoen. Controleer voor apparaten de eigenschappen van het apparaat om ervoor te zorgen dat gesynchroniseerde kenmerken de verwachte waarden bevatten.<br/>
2. Controleer de status van lidmaatschapsverwerking om te bevestigen of deze is voltooid. U de [status van lidmaatschapsverwerking](groups-create-rule.md#check-processing-status-for-a-rule) en de laatst bijgewerkte datum controleren op de pagina **Overzicht** voor de groep.

Als alles er goed uitziet, laat de groep dan even invullen. Afhankelijk van de grootte van uw tenant, kan het de eerste keer of na een regelwijziging tot 24 uur duren voordat de groep is ingevuld.

**Ik heb een regel geconfigureerd, maar nu worden de bestaande leden van de regel verwijderd**<br/>Dit is normaal. Bestaande leden van de groep worden verwijderd wanneer een regel is ingeschakeld of gewijzigd. De gebruikers die zijn teruggekeerd van de evaluatie van de regel worden als leden aan de groep toegevoegd.

**Ik zie niet direct wijzigingen in het lidmaatschap wanneer ik een regel toevoeg of wijzig, waarom niet?**<br/>Speciale lidmaatschapevaluatie gebeurt periodiek in een asynchrone achtergrondproces. Hoe lang het proces duurt, wordt bepaald door het aantal gebruikers in uw map en de grootte van de groep die is gemaakt als gevolg van de regel. Meestal zien mappen met kleine aantallen gebruikers de wijzigingen in het groepslidmaatschap in minder dan een paar minuten. Mappen met een groot aantal gebruikers kunnen 30 minuten of langer duren om te vullen.

**Hoe kan ik dwingen dat de groep nu wordt verwerkt?**<br/>
Momenteel is er geen manier om automatisch de groep te activeren die op aanvraag wordt verwerkt. U de opwerking echter handmatig activeren door de lidmaatschapsregel bij te werken om aan het einde een witruimte toe te voegen.  

**Ik heb een fout in de verwerking van regels ondervonden**<br/>In de volgende tabel worden veelvoorkomende fouten in de dynamische lidmaatschapsregel weergegeven en hoe u deze corrigeren.

| Regelparser-fout | Foutgebruik | Gecorrigeerd gebruik |
| --- | --- | --- |
| Fout: Kenmerk wordt niet ondersteund. |(user.invalidProperty -eq "Value") |(user.department -eq "waarde")<br/><br/>Controleer of het kenmerk zich in de [lijst met ondersteunde eigenschappen bevindt.](groups-dynamic-membership.md#supported-properties) |
| Fout: Operator wordt niet ondersteund op kenmerk. |(user.accountEnabled -bevat true) |(user.accountEnabled -eq true)<br/><br/>De gebruikte operator wordt niet ondersteund voor het eigenschapstype (in dit voorbeeld bevat -bevat niet op type booleaan). Gebruik de juiste operatoren voor het eigenschapstype. |
| Fout: querycompilatiefout. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match@domain.ext"* ") | 1. Ontbrekende operator. Gebruik -en of -of twee join predicaten<br>(user.department -eq "Sales") -of (user.department -eq "Marketing")<br>2. Fout in reguliere expressie die wordt gebruikt bij -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>of als alternatief: (user.userPrincipalName@domain.ext-match "$") |

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

* [Managing access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md) (Toegang tot resources beheren met Azure Active Directory-groepen)
* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md) (Toepassingsbeheer in Azure Active Directory)
* [Wat is Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md)