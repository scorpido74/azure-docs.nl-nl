---
title: Een Azure AD-adres lijst verwijderen-Azure Active Directory | Microsoft Docs
description: Hierin wordt uitgelegd hoe u een Azure AD-Directory voorbereidt voor verwijdering, inclusief self-service directory's
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73961818"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Een map in Azure Active Directory verwijderen

Wanneer een Azure AD-Directory wordt verwijderd, worden ook alle resources verwijderd die in de map zijn opgenomen. Bereid uw organisatie voor door de bijbehorende resources te minimaliseren voordat u verwijdert. Alleen een globale beheerder van een Azure Active Directory (Azure AD) kan een Azure AD-Directory verwijderen uit de portal.

## <a name="prepare-the-directory"></a>De Directory voorbereiden

U kunt een map in azure AD pas verwijderen als er verschillende controles worden door gegeven. Deze controles verminderen het risico dat het verwijderen van een Azure AD-Directory een negatieve invloed heeft op de gebruikers toegang, zoals de mogelijkheid om zich aan te melden bij Office 365 of om toegang te krijgen tot resources in Azure. Als de map die is gekoppeld aan een abonnement, bijvoorbeeld per ongeluk is verwijderd, hebben gebruikers geen toegang tot de Azure-resources voor dat abonnement. Er wordt gecontroleerd of aan de volgende voorwaarden is voldaan:

* Er kunnen zich geen gebruikers in de map bevinden, met uitzonde ring van één globale beheerder die de Directory verwijdert. Andere gebruikers moeten worden verwijderd voordat de directory kan worden verwijderd. Als gebruikers van on-premises worden gesynchroniseerd, moet de synchronisatie eerst worden uitgeschakeld en moeten de gebruikers worden verwijderd uit de Cloud Directory met behulp van de Azure Portal-of Azure PowerShell-cmdlets.
* Er mogen in de directory geen toepassingen aanwezig zijn. Alle toepassingen moeten worden verwijderd voordat de map kan worden verwijderd.
* Er kunnen geen multi-factor Authentication-providers aan de Directory zijn gekoppeld.
* Aan de directory mogen geen abonnementen op Microsoft Online Services zijn gekoppeld, zoals een abonnement op Microsoft Azure, Office 365 of Azure AD Premium. Als er bijvoorbeeld een standaarddirectory voor u is gemaakt in Azure, kunt u deze directory niet verwijderen als uw Azure-abonnement deze directory gebruikt voor verificatie. Het is evenmin mogelijk om een directory te verwijderen als een andere gebruiker er een abonnement aan heeft gekoppeld.

## <a name="delete-the-directory"></a>De map verwijderen

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat de globale beheerder is voor uw organisatie.

2. Selecteer **Azure Active Directory**.

3. Ga naar de map die u wilt verwijderen.
  
   ![Organisatie bevestigen vóór verwijderen](./media/directory-delete-howto/delete-directory-command.png)

4. Selecteer **map verwijderen**.
  
   ![Selecteer de opdracht voor het verwijderen van de organisatie](./media/directory-delete-howto/delete-directory-list.png)

5. Als uw Directory niet aan een of meer controles voldoet, krijgt u een koppeling naar meer informatie over hoe u kunt door geven. Nadat u alle controles hebt door lopen, selecteert u **verwijderen** om het proces te volt ooien.

## <a name="if-you-cant-delete-the-directory"></a>Als u de map niet kunt verwijderen

Wanneer u uw Azure AD-adres lijst hebt geconfigureerd, hebt u mogelijk ook op licenties gebaseerde abonnementen geactiveerd voor uw organisatie, zoals Azure AD Premium P2, Office 365 Business Premium of Enterprise Mobility + Security E5. Om onbedoeld gegevens verlies te voor komen, kunt u een map pas verwijderen als de abonnementen volledig zijn verwijderd. De abonnementen moeten een niet- **ingerichte** status hebben om het verwijderen van de map toe te staan. Een **verlopen** of **geannuleerde** abonnement wordt verplaatst naar de **Uitgeschakelde** status en de laatste fase is de status provisioned. **Deprovisioned**

Zie de volgende tabel voor wat u kunt verwachten wanneer een proef versie van Office 365 verloopt (exclusief betaalde partner/CSP, Enterprise Agreement of volume licentie). Zie [Wat gebeurt er met mijn gegevens en toegang wanneer mijn Office 365 voor bedrijven-abonnement wordt beëindigd?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)voor meer informatie over het bewaren van gegevens in Office 365 en de levens cyclus van abonnementen. 

Abonnements status | Gegevens | Toegang tot gegevens
----- | ----- | -----
Actief (30 dagen voor proef versie) | Gegevens die toegankelijk zijn voor alle | Gebruikers hebben normale toegang tot Office 365-bestanden of apps<br>Beheerders hebben normale toegang tot Microsoft 365 beheer centrum en bronnen 
Verlopen (30 dagen) | Gegevens die toegankelijk zijn voor alle| Gebruikers hebben normale toegang tot Office 365-bestanden of apps<br>Beheerders hebben normale toegang tot Microsoft 365 beheer centrum en bronnen
Uitgeschakeld (30 dagen) | Gegevens die alleen toegankelijk zijn voor beheerders | Gebruikers hebben geen toegang tot Office 365-bestanden of apps<br>Beheerders hebben toegang tot het Microsoft 365-beheer centrum, maar kunnen geen licenties toewijzen of gebruikers bijwerken
Inrichting ongedaan gemaakt (30 dagen na uitgeschakeld) | Gegevens verwijderd (automatisch verwijderd als er geen andere services worden gebruikt) | Gebruikers hebben geen toegang tot Office 365-bestanden of apps<br>Beheerders hebben toegang tot het Microsoft 365-beheer centrum om andere abonnementen te kopen en te beheren

## <a name="delete-a-subscription"></a>Een abonnement verwijderen

Met het Microsoft 365-beheer centrum kunt u een abonnement op de **onvoorziene** status laten verwijderen in drie dagen.

1. Meld u aan bij het [Microsoft 365-beheer centrum](https://admin.microsoft.com) met een account dat een globale beheerder is in uw organisatie. Als u probeert de Directory contoso te verwijderen met de oorspronkelijke standaard domein contoso.onmicrosoft.com, meldt u zich aan met een UPN, zoals admin@contoso.onmicrosoft.com.

2. Bekijk een voor beeld van het nieuwe Microsoft 365-beheer centrum door ervoor te zorgen dat de nieuwe schakel optie voor het **beheer centrum** is ingeschakeld.

   ![Bekijk een voor beeld van de nieuwe M365-beheer centrum-ervaring](./media/directory-delete-howto/preview-toggle.png)

3. Zodra het nieuwe beheer centrum is ingeschakeld, moet u een abonnement annuleren voordat u het kunt verwijderen. Selecteer **facturering** en selecteer **producten & Services**en selecteer vervolgens **abonnement annuleren** voor het abonnement dat u wilt annuleren. U wordt naar een feedback pagina geleid.

   ![Kies een abonnement dat u wilt annuleren](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Vul het feedback formulier in en selecteer **abonnement annuleren** om het abonnement te annuleren.

   ![Opdracht annuleren in de preview van het abonnement](./media/directory-delete-howto/cancel-command.png)

5. U kunt nu het abonnement verwijderen. Selecteer **verwijderen** voor het abonnement dat u wilt verwijderen. Als u het abonnement niet kunt vinden op de pagina **Products & Services** , controleert u of u de **abonnements status** hebt ingesteld op **alle**.

   ![Koppeling verwijderen voor het verwijderen van het abonnement](./media/directory-delete-howto/delete-command.png)

6. Selecteer **abonnement verwijderen** om het abonnement te verwijderen en de voor waarden te accepteren. Alle gegevens worden binnen drie dagen definitief verwijderd. Als u van gedachten verandert, kunt u [het abonnement opnieuw activeren](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) tijdens de periode van drie dagen.
  
   ![voor waarden zorgvuldig lezen](./media/directory-delete-howto/delete-terms.png)

7. Nu is de status van het abonnement gewijzigd en is het abonnement gemarkeerd voor verwijdering. Het abonnement treedt de **status van** 72 uur later op.

8. Zodra u een abonnement in uw map hebt verwijderd en 72 uur is verstreken, kunt u zich opnieuw aanmelden bij het Azure AD-beheer centrum en moeten er geen vereiste acties worden uitgevoerd en mogen er geen abonnementen worden verwijderd. U moet uw Azure AD-map kunnen verwijderen.
  
   ![controle van het abonnement door geven op het scherm van verwijderen](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Ik heb een proef abonnement dat verwijderen blokkeert

Er zijn [self-service-aanmeldingen](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , zoals micro soft Power BI, Rights Management Services, micro soft power apps of Dynamics 365, afzonderlijke gebruikers kunnen zich aanmelden via Office 365, waarmee ook een gast gebruiker voor verificatie wordt gemaakt in uw Azure AD-adres lijst. Met deze self-service producten worden verwijderde mappen geblokkeerd tot ze volledig zijn verwijderd uit de map, om gegevens verlies te voor komen. Ze kunnen alleen worden verwijderd door de Azure AD-beheerder, ongeacht of de gebruiker zich afzonderlijk heeft aangemeld of het product heeft toegewezen.

Er zijn twee soorten self-service registratie producten die worden toegewezen: 

* Toewijzing op organisatie niveau: een Azure AD-beheerder wijst het product toe aan de hele organisatie en een gebruiker kan actief gebruikmaken van de service met deze toewijzing op organisatie niveau, zelfs als ze niet afzonderlijk een licentie hebben.
* Toewijzing op gebruikers niveau: een afzonderlijke gebruiker tijdens een self-service-aanmelding wijst het product zonder beheerder toe aan zichzelf. Zodra de organisatie wordt beheerd door een beheerder (Zie [beheerders overname van een onbeheerde map](domains-admin-takeover.md), kan de beheerder het product rechtstreeks aan gebruikers toewijzen zonder selfservice registratie.  

Wanneer u het verwijderen van het self-service-aanmeldings product start, worden de gegevens door de actie permanent verwijderd en wordt alle gebruikers toegang tot de service verwijderd. Gebruikers aan wie de aanbieding afzonderlijk of op het niveau van de organisatie is toegewezen, kunnen zich niet aanmelden of toegang tot bestaande gegevens krijgen. Als u gegevens verlies wilt voor komen met het self-service registratie product zoals [micro soft power bi Dash boards](https://docs.microsoft.com/power-bi/service-export-to-pbix) of [Rights Management Services-beleids configuratie](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), moet u ervoor zorgen dat er een back-up van de gegevens wordt gemaakt en op een andere locatie worden opgeslagen.

Zie [beschik bare selfservice Program ma's](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)voor meer informatie over momenteel beschik bare self-service voor het aanmelden van services.

Zie de volgende tabel voor wat u kunt verwachten wanneer een proef versie van Office 365 verloopt (exclusief betaalde partner/CSP, Enterprise Agreement of volume licentie). Zie [Wat gebeurt er met mijn gegevens en toegang wanneer mijn Office 365 voor bedrijven-abonnement wordt beëindigd?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)voor meer informatie over het bewaren van gegevens in Office 365 en de levens cyclus van abonnementen.

Product status | Gegevens | Toegang tot gegevens
------------- | ---- | --------------
Actief (30 dagen voor proef versie) | Gegevens die toegankelijk zijn voor alle | Gebruikers hebben normale toegang tot een self-service voor het registreren van een product, bestanden of apps<br>Beheerders hebben normale toegang tot Microsoft 365 beheer centrum en bronnen
Verwijderen | Gegevens verwijderd | Gebruikers kunnen geen toegang krijgen tot een self-service voor het aanmelden, bestanden of apps<br>Beheerders hebben toegang tot het Microsoft 365-beheer centrum om andere abonnementen te kopen en te beheren

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hoe kan ik een self-service-aanmeld product verwijderen in de Azure Portal?

U kunt een self-service registratie product, zoals micro soft Power BI of Azure Rights Management Services, in een **verwijderings** status plaatsen zodat ze onmiddellijk worden verwijderd in de Azure AD-Portal.

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met een account dat een globale beheerder in de organisatie is. Als u probeert de Directory contoso te verwijderen met de oorspronkelijke standaard domein contoso.onmicrosoft.com, meldt u zich aan met een UPN, zoals admin@contoso.onmicrosoft.com.

2. Selecteer **licenties**en selecteer vervolgens **self-service registratie producten**. U kunt alle Self-service-aanmeldings producten afzonderlijk van de op seat gebaseerde abonnementen bekijken. Kies het product dat u definitief wilt verwijderen. Hier volgt een voor beeld van micro soft Power BI:

    ![de gebruikers naam is onjuist getypt of niet gevonden](./media/directory-delete-howto/licenses-page.png)

3. Selecteer **verwijderen** om het product te verwijderen en de voor waarden te accepteren waarmee gegevens onmiddellijk en onherroepelijk worden verwijderd. Met deze Verwijder actie worden alle gebruikers verwijderd en wordt de organisatie toegang tot het product verwijderd. Klik op Ja om door te gaan met de verwijdering.  

    ![de gebruikers naam is onjuist getypt of niet gevonden](./media/directory-delete-howto/delete-product.png)

4. Wanneer u **Ja**selecteert, wordt de verwijdering van het self-service product gestart. Er is een melding waarin wordt aangegeven dat de verwijdering wordt uitgevoerd.  

    ![de gebruikers naam is onjuist getypt of niet gevonden](./media/directory-delete-howto/progress-message.png)

5. Nu is de status van de self-service voor aanmelden gewijzigd in **verwijderd**. Wanneer u de pagina vernieuwt, moet het product worden verwijderd van de pagina met **self-service registratie producten** .  

    ![de gebruikers naam is onjuist getypt of niet gevonden](./media/directory-delete-howto/product-deleted.png)

6. Zodra u alle producten hebt verwijderd, kunt u zich opnieuw aanmelden bij het Azure AD-beheer centrum en hoeft u geen actie te ondernemen en kunnen er geen producten worden verwijderd. U moet uw Azure AD-map kunnen verwijderen.

    ![de gebruikers naam is onjuist getypt of niet gevonden](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Volgende stappen

[Documentatie voor Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
