---
title: Aangepaste domein namen toevoegen en verifiëren-Azure Active Directory | Microsoft Docs
description: Management-concepten en-procedures voor het beheren van een domein naam in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c58086a163bf558ffdc71e51e55d296e8d4d25e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728585"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Aangepaste domein namen in uw Azure Active Directory beheren

Een domein naam is een belang rijk onderdeel van de id voor veel Directory bronnen: het maakt deel uit van een gebruikers naam of e-mail adres voor een gebruiker, een deel van het adres voor een groep en maakt soms deel uit van de App-ID-URI voor een toepassing. Een resource in Azure Active Directory (Azure AD) kan een domein naam bevatten die het eigendom is van de map die de bron bevat. Alleen een globale beheerder kan domeinen in azure AD beheren.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>De primaire domein naam voor uw Azure AD-Directory instellen

Als uw directory is gemaakt, is de initiële domein naam, zoals ' contoso.onmicrosoft.com ', ook de primaire domein naam. Het primaire domein is de standaard domein naam voor een nieuwe gebruiker wanneer u een nieuwe gebruiker maakt. Het instellen van een primaire domein naam stroomlijnt het proces voor een beheerder om nieuwe gebruikers te maken in de portal. De primaire domein naam wijzigen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een account dat een globale beheerder voor de Directory is.
2. Selecteer **Azure Active Directory**.
3. Selecteer **Namen van aangepaste domeinen**.
  
   ![De pagina gebruikers beheer openen](./media/domains-manage/add-custom-domain.png)
4. Selecteer de naam van het domein dat u als primair domein wilt maken.
5. Selecteer de opdracht **Primair maken** . Bevestig uw keuze wanneer u hierom wordt gevraagd.
  
   ![Een domein naam instellen als primair](./media/domains-manage/make-primary-domain.png)

U kunt de primaire domein naam voor uw Directory wijzigen naar een geverifieerd aangepast domein dat niet federatief is. Als u het primaire domein voor uw map wijzigt, wordt de gebruikers naam niet gewijzigd voor bestaande gebruikers.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Aangepaste domein namen toevoegen aan uw Azure AD-organisatie

U kunt Maxi maal 900 beheerde domein namen toevoegen. Als u al uw domeinen configureert voor Federatie met on-premises Active Directory, kunt u Maxi maal 450 domein namen toevoegen aan elke directory.

## <a name="add-subdomains-of-a-custom-domain"></a>Subdomeinen van een aangepast domein toevoegen

Als u een domein naam van een derde niveau, zoals ' europe.contoso.com ', aan uw Directory wilt toevoegen, moet u eerst het domein van het tweede niveau toevoegen en controleren, zoals contoso.com. Het subdomein wordt automatisch gecontroleerd door Azure AD. Vernieuw de lijst met domeinen in de browser om te zien dat het subdomein dat u hebt toegevoegd, is geverifieerd.

Notitie

Als u al een contoso.com-domein aan een Azure AD-Tenant hebt toegevoegd, kunt u ook het subdomein europe.contoso.com toevoegen aan een tweede Azure AD-Tenant. Wanneer u het subdomein toevoegt, wordt u gevraagd een TXT-record toe te voegen aan de DNS-hosting provider.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Wat u moet doen als u de DNS-registratie functie voor uw aangepaste domein naam wijzigt

Als u de DNS-registratie service wijzigt, zijn er geen aanvullende configuratie taken in azure AD. U kunt de domein naam zonder onderbreking blijven gebruiken met Azure AD. Als u uw aangepaste domein naam gebruikt met Office 365, intune of andere services die afhankelijk zijn van aangepaste domein namen in azure AD, raadpleegt u de documentatie voor deze services.

## <a name="delete-a-custom-domain-name"></a>Een aangepaste domein naam verwijderen

U kunt een aangepaste domein naam uit uw Azure AD verwijderen als uw organisatie niet langer gebruikmaakt van die domein naam of als u deze domein naam moet gebruiken met een andere Azure AD.

Als u een aangepaste domein naam wilt verwijderen, moet u er eerst voor zorgen dat er geen resources in uw Directory afhankelijk zijn van de domein naam. U kunt geen domein naam uit uw Directory verwijderen als:

* Elke gebruiker heeft een gebruikers naam, e-mail adres of proxy adres dat de domein naam bevat.
* Elke groep heeft een e-mail adres of proxy adres dat de domein naam bevat.
* Elke toepassing in uw Azure AD heeft een app-ID-URI die de domein naam bevat.

U moet deze resource in uw Azure AD-adres lijst wijzigen of verwijderen voordat u de aangepaste domein naam kunt verwijderen.

### <a name="forcedelete-option"></a>ForceDelete optie

U kunt een domein naam in het [Azure AD-beheer centrum](https://aad.portal.azure.com) **ForceDelete** of gebruikmaken van [Microsoft Graph-API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Deze opties gebruiken een asynchrone bewerking en werken alle verwijzingen van de aangepaste domein naam zoals " user@contoso.com " bij naar de oorspronkelijke standaard domein naam, zoals " user@contoso.onmicrosoft.com ." 

Als u **ForceDelete** wilt aanroepen in de Azure Portal, moet u ervoor zorgen dat er minder dan 1000 verwijzingen naar de domein naam zijn en alle verwijzingen waarbij Exchange de inrichtings service is, moeten worden bijgewerkt of verwijderd in het [Exchange-beheer centrum](https://outlook.office365.com/ecp/). Dit geldt ook voor Exchange mail-beveiligings groepen en gedistribueerde lijsten. Zie [beveiligings groepen met e-mail beveiliging verwijderen](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)voor meer informatie. De **ForceDelete** -bewerking kan ook niet worden uitgevoerd als aan een van de volgende voor waarden wordt voldaan:

* U hebt een domein aangeschaft via Office 365 Domain Subscription Services
* U bent een partner die u beheert namens een andere klant organisatie

De volgende acties worden uitgevoerd als onderdeel van de **ForceDelete** -bewerking:

* De naam van de UPN, Emailnaam en proxyAddress attribuut van gebruikers met verwijzingen naar de aangepaste domein naam naar de oorspronkelijke standaard domein naam.
* De naam van het EmailAddress van groepen met verwijzingen naar de aangepaste domein naam wordt gewijzigd naar de naam van het oorspronkelijke standaard domein.
* De naam van de identifierUris van toepassingen met verwijzingen naar de aangepaste domein naam wordt gewijzigd in de naam van het oorspronkelijke standaard domein.

Er wordt een fout geretourneerd wanneer:

* Het aantal objecten waarvan de naam moet worden gewijzigd, is groter dan 1000
* Een van de toepassingen waarvan u de naam wilt wijzigen, is een app met meerdere tenants

### <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: waarom mislukt het verwijderen van het domein met een fout die aangeeft dat ik Gemastered groups heb op deze domein naam?** <br>
**A:** Momenteel worden bepaalde groepen, zoals beveiligings groepen met e-mail functionaliteit en gedistribueerde lijsten, door Exchange ingericht en moeten ze hand matig worden opgeschoond in [Exchange-beheer centrum (SBV)](https://outlook.office365.com/ecp/). Er is mogelijk een achtergebleven ProxyAddresses die afhankelijk is van de aangepaste domein naam en moet hand matig worden bijgewerkt naar een andere domein naam. 

**V: Ik ben aangemeld als beheerder \@ contoso.com, maar ik kan de domein naam ' contoso.com ' niet verwijderen?**<br>
**A:** U kunt niet verwijzen naar de aangepaste domein naam die u probeert te verwijderen in de naam van uw gebruikers account. Zorg ervoor dat het account van de globale beheerder de oorspronkelijke standaard domein naam (. onmicrosoft.com) gebruikt, zoals admin@contoso.onmicrosoft.com . Meld u aan met een ander account voor globale beheerders, zoals een admin@contoso.onmicrosoft.com andere aangepaste domein naam, zoals ' fabrikam.com ', waarbij het account is admin@fabrikam.com .

**V: Ik heb op de knop domein verwijderen geklikt en Zie `In Progress` status voor de Verwijder bewerking. Hoe lang duurt het? Wat gebeurt er als dit mislukt?**<br>
**A:** De bewerking domein verwijderen is een asynchrone achtergrond taak waarmee alle verwijzingen naar de domein naam worden hernoemd. Deze moet binnen een minuut of twee worden voltooid. Als het verwijderen van het domein mislukt, zorg er dan voor dat u niet beschikt over:

* Apps die zijn geconfigureerd op de domein naam met de appIdentifierURI
* Elke e-mail groep die verwijst naar de aangepaste domein naam
* Meer dan 1000 verwijzingen naar de domein naam

Als u merkt dat aan een van de voor waarden niet is voldaan, moet u de verwijzingen hand matig opschonen en proberen het domein opnieuw te verwijderen.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Power shell of de Microsoft Graph-API gebruiken voor het beheren van domein namen

De meeste beheer taken voor domein namen in Azure Active Directory kunnen ook worden voltooid met behulp van micro soft power shell of via een programma met behulp van de Microsoft Graph-API.

* [Power shell gebruiken voor het beheren van domein namen in azure AD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Bron type van het domein](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste domeinnamen toevoegen](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Beveiligings groepen met Exchange-e-mail verwijderen in Exchange-beheer centrum op een aangepaste domein naam in azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [Een aangepaste domein naam ForceDelete met Microsoft Graph-API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
