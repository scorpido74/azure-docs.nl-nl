---
title: Een Azure AD-map verwijderen - Azure Active Directory | Microsoft Documenten
description: In de uitleg hoe u een Azure AD-map voorbereidt voor verwijdering, inclusief selfservice-mappen
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961818"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Een map verwijderen in Azure Active Directory

Wanneer een Azure AD-map wordt verwijderd, worden ook alle bronnen in de map verwijderd. Bereid uw organisatie voor door de bijbehorende resources te minimaliseren voordat u deze verwijdert. Alleen een globale Azure Active Directory(Azure AD)-beheerder kan een Azure AD-map uit de portal verwijderen.

## <a name="prepare-the-directory"></a>De map voorbereiden

U een map in Azure AD pas verwijderen als deze meerdere controles heeft doorstaan. Deze controles verminderen het risico dat het verwijderen van een Azure AD-map een negatieve invloed heeft op de toegang van gebruikers, zoals de mogelijkheid om u aan te melden bij Office 365 of toegang te krijgen tot bronnen in Azure. Als de map die aan een abonnement is gekoppeld bijvoorbeeld onbedoeld wordt verwijderd, hebben gebruikers geen toegang tot de Azure-bronnen voor dat abonnement. Er wordt gecontroleerd of aan de volgende voorwaarden is voldaan:

* Er kunnen geen gebruikers in de map zijn, behalve één globale beheerder die de map moet verwijderen. Andere gebruikers moeten worden verwijderd voordat de directory kan worden verwijderd. Als gebruikers worden gesynchroniseerd vanuit on-premises, moet de synchronisatie eerst worden uitgeschakeld en moeten de gebruikers worden verwijderd in de cloudmap met behulp van de Azure-portal of Azure PowerShell-cmdlets.
* Er mogen in de directory geen toepassingen aanwezig zijn. Alle toepassingen moeten worden verwijderd voordat de map kan worden verwijderd.
* Er kunnen geen multi-factor authenticatie providers gekoppeld aan de directory.
* Aan de directory mogen geen abonnementen op Microsoft Online Services zijn gekoppeld, zoals een abonnement op Microsoft Azure, Office 365 of Azure AD Premium. Als er bijvoorbeeld een standaarddirectory voor u is gemaakt in Azure, kunt u deze directory niet verwijderen als uw Azure-abonnement deze directory gebruikt voor verificatie. Het is evenmin mogelijk om een directory te verwijderen als een andere gebruiker er een abonnement aan heeft gekoppeld.

## <a name="delete-the-directory"></a>De map verwijderen

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een account dat de globale beheerder voor uw organisatie is.

2. Selecteer **Azure Active Directory**.

3. Schakel over naar de map die u wilt verwijderen.
  
   ![Organisatie bevestigen voordat u de organisatie verwijderde](./media/directory-delete-howto/delete-directory-command.png)

4. Selecteer **Map verwijderen**.
  
   ![de opdracht selecteren om de organisatie te verwijderen](./media/directory-delete-howto/delete-directory-list.png)

5. Als uw directory niet slaagt voor een of meer controles, krijgt u een link naar meer informatie over hoe u slagen. Nadat u alle controles hebt uitgevoerd, selecteert u **Verwijderen** om het proces te voltooien.

## <a name="if-you-cant-delete-the-directory"></a>Als u de map niet verwijderen

Wanneer u uw Azure AD-map hebt geconfigureerd, hebt u mogelijk ook licentiegebaseerde abonnementen voor uw organisatie geactiveerd, zoals Azure AD Premium P2, Office 365 Business Premium of Enterprise Mobility + Security E5. Om onbedoeld gegevensverlies te voorkomen, u een map niet verwijderen totdat de abonnementen volledig zijn verwijderd. De abonnementen moeten in een **deprovisioned-status** staan om het verwijderen van directorytoerekeningsgegevens mogelijk te maken. Een **verlopen** of **geannuleerd** abonnement wordt verplaatst naar de **status Uitgeschakeld** en de laatste fase is de status **Deprovisioned.**

Voor wat u verwachten wanneer een proefabonnement office 365-abonnement verloopt (exclusief betaalde partner/CSP- of ondernemingsovereenkomst of volumelicenties), raadpleegt u de volgende tabel. Zie [Wat gebeurt er met mijn gegevens en toegang wanneer mijn abonnement op Office 365 voor Bedrijven afloopt voor](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)meer informatie over de levenscyclus van Office 365-gegevens en -abonnementen? . 

Abonnementsstatus | Gegevens | Toegang tot gegevens
----- | ----- | -----
Actief (30 dagen voor proefperiode) | Gegevens die toegankelijk zijn voor iedereen | Gebruikers hebben normale toegang tot Office 365-bestanden of apps<br>Beheerders hebben normale toegang tot microsoft 365-beheercentrum en bronnen 
Verlopen (30 dagen) | Gegevens die toegankelijk zijn voor iedereen| Gebruikers hebben normale toegang tot Office 365-bestanden of apps<br>Beheerders hebben normale toegang tot microsoft 365-beheercentrum en bronnen
Uitgeschakeld (30 dagen) | Alleen gegevens toegankelijk voor beheerders | Gebruikers hebben geen toegang tot Office 365-bestanden of -apps<br>Beheerders hebben toegang tot het Microsoft 365-beheercentrum, maar kunnen geen licenties toewijzen aan of gebruikers bijwerken
Gedeprovisioneerd (30 dagen na uitgeschakeld) | Gegevens verwijderd (automatisch verwijderd als er geen andere services worden gebruikt) | Gebruikers hebben geen toegang tot Office 365-bestanden of -apps<br>Beheerders hebben toegang tot het Microsoft 365-beheercentrum om andere abonnementen te kopen en te beheren

## <a name="delete-a-subscription"></a>Een abonnement verwijderen

U een abonnement in de **status Deprovisioned** plaatsen om binnen drie dagen te worden verwijderd met behulp van het Microsoft 365-beheercentrum.

1. Meld u aan bij het [Microsoft 365-beheercentrum](https://admin.microsoft.com) met een account dat een globale beheerder in uw organisatie is. Als u de map 'Contoso' met het oorspronkelijke standaarddomein contoso.onmicrosoft.com, meldt u admin@contoso.onmicrosoft.comzich aan bij een UPN zoals .

2. Bekijk een voorbeeld van het nieuwe Microsoft 365-beheercentrum door ervoor te zorgen dat de schakelaar **Het nieuwe beheercentrum** uitproberen is ingeschakeld.

   ![Bekijk een voorbeeld van de nieuwe M365-beheercentrumervaring](./media/directory-delete-howto/preview-toggle.png)

3. Zodra het nieuwe beheercentrum is ingeschakeld, moet u een abonnement opzeggen voordat u het verwijderen. Selecteer **Facturering** en selecteer **Producten & services**en selecteer Vervolgens Abonnement **opzeggen** voor het abonnement dat u wilt opzeggen. U wordt naar een feedbackpagina gebracht.

   ![Een abonnement kiezen om op te zeggen](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Vul het feedbackformulier in en selecteer **Abonnement annuleren** om het abonnement op te zeggen.

   ![Opdracht annuleren in de voorvertoning van het abonnement](./media/directory-delete-howto/cancel-command.png)

5. U het abonnement nu verwijderen. Selecteer **Verwijderen** voor het abonnement dat u wilt verwijderen. Als u het abonnement niet vinden op de pagina **Producten & services,** controleert u of u **de status Van Abonnement** hebt ingesteld op **Alles**.

   ![Koppeling verwijderen voor het verwijderen van een abonnement](./media/directory-delete-howto/delete-command.png)

6. Selecteer **Abonnement verwijderen** om het abonnement te verwijderen en de algemene voorwaarden te accepteren. Alle gegevens worden permanent binnen drie dagen verwijderd. U [het abonnement opnieuw activeren](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) gedurende de periode van drie dagen als u van gedachten verandert.
  
   ![lees zorgvuldig de algemene voorwaarden](./media/directory-delete-howto/delete-terms.png)

7. Nu is de abonnementsstatus gewijzigd en is het abonnement gemarkeerd voor verwijdering. Het abonnement komt 72 uur later in de **deprovisioned** state.

8. Zodra u een abonnement in uw directory hebt verwijderd en 72 uur zijn verstreken, u zich opnieuw aanmelden bij het Azure AD-beheercentrum en mogen er geen vereiste actie zijn en mogen er geen abonnementen zijn die uw directory-verwijdering blokkeren. U moet uw Azure AD-map kunnen verwijderen.
  
   ![pas abonnement te controleren op verwijdering scherm](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Ik heb een proefabonnement dat verwijdering blokkeert

Er zijn [selfservice-aanmeldingsproducten](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) zoals Microsoft Power BI, Rights Management Services, Microsoft Power Apps of Dynamics 365, individuele gebruikers kunnen zich aanmelden via Office 365, waardoor er ook een gastgebruiker wordt gemaakt voor verificatie in uw Azure AD-directory. Deze selfserviceproducten blokkeren het verwijderen van directory's totdat ze volledig uit de map zijn verwijderd, om gegevensverlies te voorkomen. Ze kunnen alleen worden verwijderd door de Azure AD-beheerder, ongeacht of de gebruiker zich afzonderlijk heeft aangemeld of aan het product is toegewezen.

Er zijn twee soorten self-service aanmeldingsproducten in de manier waarop ze worden toegewezen: 

* Toewijzing op org-niveau: een Azure AD-beheerder wijst het product toe aan de hele organisatie en een gebruiker kan de service actief gebruiken met deze toewijzing op organisatieniveau, zelfs als deze niet afzonderlijk is gelicentieerd.
* Toewijzing op gebruikersniveau: Een individuele gebruiker tijdens selfservice-aanmelding wijst het product in wezen aan zichzelf toe zonder beheerder. Zodra de organisatie wordt beheerd door een beheerder (zie [Administrator overname van een niet-beheerde directory](domains-admin-takeover.md), dan kan de beheerder het product rechtstreeks toewijzen aan gebruikers zonder self-service aanmelding.  

Wanneer u begint met het verwijderen van het zelfserviceaanmeldingsproduct, verwijdert de actie de gegevens permanent en verwijdert u alle toegang van de gebruiker tot de service. Elke gebruiker die de aanbieding afzonderlijk of op organisatieniveau heeft toegewezen, wordt vervolgens geblokkeerd voor het aanmelden of toegang tot bestaande gegevens. Als u gegevensverlies wilt voorkomen met het selfservice-aanmeldingsproduct zoals [Microsoft Power BI-dashboards](https://docs.microsoft.com/power-bi/service-export-to-pbix) of [de beleidsconfiguratie van Rights Management Services,](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)moet u ervoor zorgen dat er een back-up van de gegevens wordt genomen en elders wordt opgeslagen.

Zie [Beschikbare selfserviceprogramma's](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)voor meer informatie over de momenteel beschikbare selfservice-aanmeldingsproducten en -services.

Voor wat u verwachten wanneer een proefabonnement office 365-abonnement verloopt (exclusief betaalde partner/CSP- of ondernemingsovereenkomst of volumelicenties), raadpleegt u de volgende tabel. Zie [Wat gebeurt er met mijn gegevens en toegang wanneer mijn abonnement op Office 365 voor Bedrijven afloopt voor](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)meer informatie over de levenscyclus van Office 365-gegevens en -abonnementen? .

Productstatus | Gegevens | Toegang tot gegevens
------------- | ---- | --------------
Actief (30 dagen voor proefperiode) | Gegevens die toegankelijk zijn voor iedereen | Gebruikers hebben normale toegang tot zelfservice-aanmeldingsproduct, bestanden of apps<br>Beheerders hebben normale toegang tot microsoft 365-beheercentrum en bronnen
Verwijderen | Gegevens verwijderd | Gebruikers hebben geen toegang tot het zelfservice-aanmeldingsproduct, bestanden of apps<br>Beheerders hebben toegang tot het Microsoft 365-beheercentrum om andere abonnementen te kopen en te beheren

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hoe kan ik een selfservice-aanmeldingsproduct verwijderen in de Azure-portal?

U een selfservice-aanmeldingsproduct zoals Microsoft Power BI of Azure Rights Management Services in een **delete-status** plaatsen die onmiddellijk in de Azure AD-portal moet worden verwijderd.

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met een account dat een globale beheerder in de organisatie is. Als u de map 'Contoso' met het oorspronkelijke standaarddomein contoso.onmicrosoft.com, meldt u admin@contoso.onmicrosoft.comzich aan bij een UPN zoals .

2. Selecteer **Licenties**en selecteer **vervolgens zelfserviceaanmeldingsproducten**. U alle self-service aanmeldingsproducten apart van de abonnementen op de stoel bekijken. Kies het product dat u permanent wilt verwijderen. Hier is een voorbeeld in Microsoft Power BI:

    ![de gebruikersnaam is verkeerd getypt of niet gevonden](./media/directory-delete-howto/licenses-page.png)

3. Selecteer **Verwijderen** om het product te verwijderen en accepteer de voorwaarden dat gegevens onmiddellijk en onherroepelijk worden verwijderd. Met deze verwijderactie worden alle gebruikers verwijderd en de toegang van organisaties tot het product verwijderd. Klik op Ja om verder te gaan met de verwijdering.  

    ![de gebruikersnaam is verkeerd getypt of niet gevonden](./media/directory-delete-howto/delete-product.png)

4. Wanneer u **Ja**selecteert, wordt het verwijderen van het zelfbedieningsproduct gestart. Er is een melding die u zal vertellen van de verwijdering in uitvoering.  

    ![de gebruikersnaam is verkeerd getypt of niet gevonden](./media/directory-delete-howto/progress-message.png)

5. Nu is de productstatus voor selfservice-aanmelding gewijzigd in **Verwijderd**. Wanneer u de pagina vernieuwt, moet het product worden verwijderd van de pagina **Zelfserviceaanmeldingsproducten.**  

    ![de gebruikersnaam is verkeerd getypt of niet gevonden](./media/directory-delete-howto/product-deleted.png)

6. Zodra u alle producten hebt verwijderd, u zich opnieuw aanmelden bij het Azure AD-beheercentrum en moet er geen vereiste actie zijn en geen producten die uw directoryverwijderen blokkeren. U moet uw Azure AD-map kunnen verwijderen.

    ![de gebruikersnaam is verkeerd getypt of niet gevonden](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Volgende stappen

[Azure Active Directory-documentatie](https://docs.microsoft.com/azure/active-directory/)
