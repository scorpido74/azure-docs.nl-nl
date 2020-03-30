---
title: Aangepaste domeinnamen toevoegen en verifiëren - Azure Active Directory | Microsoft Documenten
description: Beheerconcepten en how-tos voor het beheren van een domeinnaam in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2395aa5984de2a9fe41e4778d16aba69bfef5192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559230"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Aangepaste domeinnamen beheren in uw Azure Active Directory

Een domeinnaam is een belangrijk onderdeel van de id voor veel directorybronnen: het maakt deel uit van een gebruikersnaam of e-mailadres voor een gebruiker, een deel van het adres voor een groep, en maakt soms deel uit van de app-ID URI voor een toepassing. Een bron in Azure Active Directory (Azure AD) kan een domeinnaam bevatten die eigendom is van de map die de bron bevat. Alleen een globale beheerder kan domeinen beheren in Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>De primaire domeinnaam instellen voor uw Azure AD-map

Wanneer uw map wordt gemaakt, is de oorspronkelijke domeinnaam, zoals 'contoso.onmicrosoft.com', ook de primaire domeinnaam. Het primaire domein is de standaarddomeinnaam voor een nieuwe gebruiker wanneer u een nieuwe gebruiker maakt. Als u een primaire domeinnaam instelt, wordt het proces gestroomlijnd voor een beheerder om nieuwe gebruikers in de portal te maken. Ga als een wijziging in op de primaire domeinnaam:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat een globale beheerder voor de directory is.
2. Selecteer **Azure Active Directory**.
3. Selecteer **Namen van aangepaste domeinen**.
  
   ![De pagina gebruikersbeheer openen](./media/domains-manage/add-custom-domain.png)
4. Selecteer de naam van het domein dat u als primaire domein wilt zijn.
5. Selecteer de **opdracht Primaire maken.** Bevestig uw keuze wanneer u daarom wordt gevraagd.
  
   ![Een domeinnaam primair maken](./media/domains-manage/make-primary-domain.png)

U de primaire domeinnaam voor uw map wijzigen als een geverifieerd aangepast domein dat niet wordt gefedereerd. Als u het primaire domein voor uw map wijzigt, wordt de gebruikersnaam voor bestaande gebruikers niet gewijzigd.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Aangepaste domeinnamen toevoegen aan uw Azure AD-tenant

U maximaal 900 beheerde domeinnamen toevoegen. Als u al uw domeinen configureert voor federatie met on-premises Active Directory, u in elke map maximaal 450 domeinnamen toevoegen.

## <a name="add-subdomains-of-a-custom-domain"></a>Subdomeinen van een aangepast domein toevoegen

Als u een domeinnaam op het derde niveau, zoals 'europe.contoso.com', aan uw map wilt toevoegen, moet u eerst het domein op het tweede niveau toevoegen en verifiëren, zoals contoso.com. Het subdomein wordt automatisch geverifieerd door Azure AD. Als u wilt zien dat het subdomein dat u hebt toegevoegd, is geverifieerd, vernieuwt u de domeinlijst in de browser.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Wat te doen als u de DNS-registrar voor uw aangepaste domeinnaam wijzigt

Als u de DNS-registrars wijzigt, zijn er geen extra configuratietaken in Azure AD. U de domeinnaam zonder onderbreking blijven gebruiken met Azure AD. Als u uw aangepaste domeinnaam gebruikt met Office 365, Intune of andere services die afhankelijk zijn van aangepaste domeinnamen in Azure AD, raadpleegt u de documentatie voor die services.

## <a name="delete-a-custom-domain-name"></a>Een aangepaste domeinnaam verwijderen

U een aangepaste domeinnaam uit uw Azure AD verwijderen als uw organisatie die domeinnaam niet meer gebruikt of als u die domeinnaam moet gebruiken met een andere Azure AD.

Als u een aangepaste domeinnaam wilt verwijderen, moet u er eerst voor zorgen dat er geen bronnen in uw map op de domeinnaam vertrouwen. U een domeinnaam niet uit uw map verwijderen als:

* Elke gebruiker heeft een gebruikersnaam, e-mailadres of proxyadres dat de domeinnaam bevat.
* Elke groep heeft een e-mailadres of proxyadres dat de domeinnaam bevat.
* Elke toepassing in uw Azure AD heeft een uri voor app-id's die de domeinnaam bevat.

U moet een dergelijke bron in uw Azure AD-map wijzigen of verwijderen voordat u de aangepaste domeinnaam verwijderen.

### <a name="forcedelete-option"></a>ForceDelete, optie

U een domeinnaam **forceren** in het [Azure AD-beheercentrum](https://aad.portal.azure.com) of microsoft [graph-api gebruiken.](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta) Deze opties maken gebruik van een asynchrone bewerking enuser@contoso.comwerken alle verwijzingen bij vanuser@contoso.onmicrosoft.comde aangepaste domeinnaam zoals " " naar de oorspronkelijke standaarddomeinnaam zoals " . 

Als u **ForceDelete** wilt aanroepen in de Azure-portal, moet u ervoor zorgen dat er minder dan 1000 verwijzingen naar de domeinnaam zijn en dat verwijzingen waarbij Exchange de inrichtingsservice is, moeten worden bijgewerkt of verwijderd in het [Exchange-beheercentrum.](https://outlook.office365.com/ecp/) Dit omvat Exchange Mail-enabled beveiligingsgroepen en gedistribueerde lijsten; Zie [Beveiligingsgroepen verwijderen voor](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)meer informatie . De **ForceDelete-bewerking** wordt ook niet uitgevoerd als een van de volgende handelingen waar is:

* U hebt een domein gekocht via Office 365-abonnementsservices voor domeinen
* U bent een partner die namens een andere klanthuurder

De volgende acties worden uitgevoerd als onderdeel van de **ForceDelete-bewerking:**

* Hiermee wijzigt u de naam van upn, emailadres en proxyadres van gebruikers met verwijzingen naar de aangepaste domeinnaam naar de oorspronkelijke standaarddomeinnaam.
* Hiermee wijzigt u de naam van het e-mailadres van groepen met verwijzingen naar de aangepaste domeinnaam naar de oorspronkelijke standaarddomeinnaam.
* Hiermee wijzigt u de naam van de idUris van toepassingen met verwijzingen naar de aangepaste domeinnaam naar de oorspronkelijke standaarddomeinnaam.

Er wordt een fout geretourneerd wanneer:

* Het aantal objecten dat opnieuw wordt genoemd is groter dan 1000 objecten
* Een van de toepassingen die anders worden genoemd is een multi-tenant app

### <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Waarom is het verwijderen van het domein mislukt met een fout die verklaart dat ik Exchange-beheerde groepen op deze domeinnaam heb?** <br>
**A:** Tegenwoordig worden bepaalde groepen zoals beveiligde groepen met e-mail en gedistribueerde lijsten door Exchange ingericht en moeten ze handmatig worden opgeschoond in [het Exchange Admin Center (EAC).](https://outlook.office365.com/ecp/) Er kunnen blijvende ProxyAddresses die afhankelijk zijn van de aangepaste domeinnaam en moet handmatig worden bijgewerkt naar een andere domeinnaam. 

**V: Ik ben ingelogd\@als admin contoso.com maar ik kan de domeinnaam "contoso.com" niet verwijderen?**<br>
**A:** U niet verwijzen naar de aangepaste domeinnaam die u probeert te verwijderen in uw gebruikersnaamnaam. Controleer of het globale beheerdersaccount de oorspronkelijke standaarddomeinnaam (.onmicrosoft.com) gebruikt, zoals admin@contoso.onmicrosoft.com. Meld u aan met een ander admin@contoso.onmicrosoft.com Global Administrator-account dat, zoals of admin@fabrikam.comeen andere aangepaste domeinnaam zoals "fabrikam.com" waar het account zich bevindt.

**V: Ik heb op de `In Progress` knop Domein verwijderen geklikt en de status voor de bewerking Verwijderen weergegeven. Hoe lang duurt het? Wat gebeurt er als het mislukt?**<br>
**A:** De domeinbewerking verwijderen is een asynchrone achtergrondtaak die de namen van alle verwijzingen naar de domeinnaam wijzigt. Het moet binnen een minuut of twee voltooien. Als het verwijderen van domeinen mislukt, moet u ervoor zorgen dat u niet beschikt over:

* Apps die op de domeinnaam zijn geconfigureerd met de appIdentifierURI
* Elke groep met e-mail die verwijst naar de aangepaste domeinnaam
* Meer dan 1000 verwijzingen naar de domeinnaam

Als u merkt dat niet aan een van de voorwaarden is voldaan, ruimt u handmatig de verwijzingen op en probeert u het domein opnieuw te verwijderen.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>PowerShell of de Microsoft Graph API gebruiken om domeinnamen te beheren

De meeste beheertaken voor domeinnamen in Azure Active Directory kunnen ook worden voltooid met Microsoft PowerShell of programmatisch met behulp van de Microsoft Graph API.

* [PowerShell gebruiken om domeinnamen in Azure AD te beheren](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Domeinbrontype](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste domeinnamen toevoegen](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Exchange-beveiligingsgroepen met e-mail verwijderen in Exchange-beheercentrum op een aangepaste domeinnaam in Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete een aangepaste domeinnaam met Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
