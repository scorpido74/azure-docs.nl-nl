---
title: Azure Active Directory Connect Health-bewerkingen
description: In dit artikel worden aanvullende bewerkingen beschreven die kunnen worden uitgevoerd nadat u Azure AD Connect-status hebt geïmplementeerd.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef908429d359020282920d73480a472bfde0aa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261513"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health-bewerkingen
In dit onderwerp worden de verschillende bewerkingen beschreven die u uitvoeren met Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>E-mailmeldingen inschakelen
U de Azure AD Connect Health-service zo configureren dat u e-mailmeldingen verzendt wanneer meldingen aangeven dat uw identiteitsinfrastructuur niet in orde is. Dit gebeurt wanneer een waarschuwing wordt gegenereerd en wanneer deze is opgelost.

![Schermafbeelding van de e-mailmeldingsinstellingen van Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-mailmeldingen zijn standaard ingeschakeld.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>E-mailmeldingen voor Azure AD Connect Health inschakelen
1. Open het **zwaard Waarschuwingen** voor de service waarvoor u een e-mailmelding wilt ontvangen.
2. Klik op de actiebalk op **Meldingsinstellingen**.
3. Selecteer **AAN bij**de e-mailmeldingsschakelaar .
4. Schakel het selectievakje in als u wilt dat alle globale beheerders e-mailmeldingen ontvangen.
5. Als u e-mailmeldingen op andere e-mailadressen wilt ontvangen, geeft u deze op in het vak **Extra e-mailontvangers.** Als u een e-mailadres uit deze lijst wilt verwijderen, klikt u met de rechtermuisknop op het item en selecteert u **Verwijderen**.
6. Als u de wijzigingen wilt afronden, klikt u op **Opslaan**. Wijzigingen worden pas van kracht nadat u hebt opgeslagen.

>[!NOTE] 
> Wanneer er problemen zijn met het verwerken van synchronisatieverzoeken in onze backendservice, stuurt deze service een meldingsmail met de details van de fout naar het e-mailadres(s) van de beheerderscontactpersoon van uw tenant. We hoorden feedback van klanten dat in bepaalde gevallen het volume van deze berichten onbetaalbaar groot is, dus we veranderen de manier waarop we deze berichten verzenden. 
>
> In plaats van het verzenden van een bericht voor elke synchronisatie fout elke keer dat het zich voordoet sturen we een dagelijkse samenvatting van alle fouten de backend service is teruggekeerd. Dit stelt klanten in staat om deze fouten efficiënter te verwerken en vermindert het aantal dubbele foutmeldingen.
>
> We zijn van plan deze wijziging op 15 januari 2020 door te voeren.

## <a name="delete-a-server-or-service-instance"></a>Een server of service-instantie verwijderen

>[!NOTE] 
> Azure AD Premium-licentie is vereist voor de verwijderingsstappen.

In sommige gevallen wilt u mogelijk voorkomen dat een server wordt gecontroleerd. Dit is wat u moet weten om een server te verwijderen uit de Azure AD Connect Health-service.

Wanneer u een server verwijderd, moet u rekening houden met het volgende:

* Met deze actie worden geen verdere gegevens van die server meer verzameld. Deze server wordt uit de bewakingsservice verwijderd. Na deze actie u geen nieuwe gegevens over waarschuwingen, bewaking of gebruiksanalyse voor deze server weergeven.
* Met deze actie wordt de statusagent niet van uw server verwijderd. Als u de statusagent niet hebt verwijderd voordat u deze stap uitvoert, ziet u mogelijk fouten met betrekking tot de statusagent op de server.
* Met deze actie worden de gegevens die al van deze server zijn verzameld, niet verwijderd. Die gegevens worden verwijderd in overeenstemming met het Azure-beleid voor het bewaren van gegevens.
* Als u na het uitvoeren van deze actie dezelfde server opnieuw wilt controleren, moet u de statusagent op deze server verwijderen en opnieuw installeren.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Een server verwijderen uit de Azure AD Connect-statusservice

>[!NOTE] 
> Azure AD Premium-licentie is vereist voor de verwijderingsstappen.

Azure AD Connect-status voor AD FS (Active Directory Federation Services) en Azure AD Connect (Synchroniseren):

1. Open het **serverblad** in het **serverlijstblad** door de servernaam te selecteren die moet worden verwijderd.
2. Klik op het **serverblad** op de actiebalk op **Verwijderen**.
![Schermafbeelding van de server voor het verwijderen van Azure AD Connect-status](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Bevestig door de servernaam in het bevestigingsvak te typen.
4. Klik **op Verwijderen**.

Azure AD Connect-status voor Azure Active Directory Domain Services:

1. Open het dashboard **Domeincontrollers.**
2. Selecteer de domeincontroller die moet worden verwijderd.
3. Klik op de actiebalk op **Geselecteerd verwijderen**.
4. Bevestig de actie om de server te verwijderen.
5. Klik **op Verwijderen**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Een serviceinstantie verwijderen uit de Azure AD Connect-statusservice
In sommige gevallen u een service-instantie verwijderen. Dit is wat u moet weten om een service-exemplaar uit de Azure AD Connect-statusservice te verwijderen.

Wanneer u een service-exemplaar uitdeint, moet u rekening houden met het volgende:

* Met deze actie wordt de huidige service-instantie verwijderd uit de bewakingsservice.
* Met deze actie wordt de statusagent niet verwijderd of verwijderd van een van de servers die zijn gecontroleerd als onderdeel van deze serviceinstantie. Als u de statusagent niet hebt verwijderd voordat u deze stap uitvoert, ziet u mogelijk fouten met betrekking tot de statusagent op de servers.
* Alle gegevens uit deze serviceinstantie worden verwijderd in overeenstemming met het azure-beleid voor het bewaren van gegevens.
* Als u na het uitvoeren van deze actie de service wilt controleren, verwijdert u de statusagent op alle servers en installeert u deze opnieuw. Als u na het uitvoeren van deze actie dezelfde server opnieuw wilt controleren, verwijdert, installeert, installeert u de statusagent op die server opnieuw.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Een serviceinstantie verwijderen uit de Azure AD Connect-statusservice
1. Open het **serviceblad** in het blade van de **servicelijst** door de service-id (bedrijfsnaam) te selecteren die u wilt verwijderen. 
2. Klik op het **serviceblad** op de actiebalk op **Verwijderen**. 
![Schermafbeelding van de verwijderservice Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer.png)
3. Bevestig door de servicenaam in het bevestigingsvak te typen (bijvoorbeeld: sts.contoso.com).
4. Klik **op Verwijderen**.
   <br><br>

[//]: # (Begin van de sectie RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Toegang beheren met op rollen gebaseerd toegangsbeheer
[RBAC (Role-Based Access Control)](../../role-based-access-control/role-assignments-portal.md) voor Azure AD Connect Health biedt toegang tot andere gebruikers en groepen dan globale beheerders. RBAC wijst rollen toe aan de beoogde gebruikers en groepen en biedt een mechanisme om de globale beheerders in uw directory te beperken.

### <a name="roles"></a>Rollen
Azure AD Connect Health ondersteunt de volgende ingebouwde rollen:

| Rol | Machtigingen |
| --- | --- |
| Eigenaar |Eigenaren kunnen *toegang beheren* (bijvoorbeeld een rol toewijzen aan een gebruiker of groep), alle informatie (bijvoorbeeld waarschuwingen bekijken) vanuit de portal *en* *instellingen wijzigen* (bijvoorbeeld e-mailmeldingen) binnen Azure AD Connect Health. <br>Azure AD-globale beheerders krijgen deze rol standaard toegewezen en dit kan niet worden gewijzigd. |
| Inzender |Bijdragers kunnen *alle informatie* (bijvoorbeeld waarschuwingen bekijken) van de portal bekijken en *instellingen wijzigen* (bijvoorbeeld e-mailmeldingen) binnen Azure AD Connect Health. |
| Lezer |Lezers kunnen *alle informatie* (bijvoorbeeld waarschuwingen weergeven) bekijken vanuit de portal in Azure AD Connect Health. |

Alle andere rollen (zoals gebruikerstoegangsbeheerders of DevTest Labs-gebruikers) hebben geen invloed op de toegang binnen Azure AD Connect Health, zelfs niet als de rollen beschikbaar zijn in de portalervaring.

### <a name="access-scope"></a>Toegangsbereik
Azure AD Connect Health ondersteunt het beheren van toegang op twee niveaus:

* **Alle service-exemplaren**: Dit is in de meeste gevallen het aanbevolen pad. Het regelt de toegang voor alle service-exemplaren (bijvoorbeeld een AD FS-farm) voor alle roltypen die worden gecontroleerd door Azure AD Connect-status.
* **Service-instantie**: In sommige gevallen moet u toegang mogelijk scheiden op basis van roltypen of een serviceinstantie. In dit geval u de toegang op service-instantieniveau beheren.  

Er wordt toestemming verleend als een eindgebruiker toegang heeft tot directory- of serviceinstantieniveau.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Gebruikers of groepen toegang geven tot Azure AD Connect Health
In de volgende stappen wordt uitgelegd hoe u toegang toestaan.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Stap 1: Het juiste toegangsbereik selecteren
Als u een gebruiker toegang wilt verlenen op het niveau *van alle service-instanties* binnen Azure AD Connect-status, opent u het hoofdblad in Azure AD Connect-status.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Stap 2: Gebruikers en groepen toevoegen en rollen toewijzen
1. Klik **in** de sectie Configureren op **Gebruikers**.<br>
   ![Schermafbeelding van de sidebar Azure AD Connect Health-bron](./media/how-to-connect-health-operations/startRBAC.png)
2. Selecteer **Toevoegen**.
3. Selecteer **in het rolvenster Selecteren** een rol (bijvoorbeeld **Eigenaar**).<br>
   ![Schermafbeelding van het venster RBAC-gebruikers van Azure AD Connect Health](./media/how-to-connect-health-operations/RBAC_add.png)
4. Typ de naam of id van de beoogde gebruiker of groep. U een of meer gebruikers of groepen tegelijkertijd selecteren. Klik **op Selecteren**.
   ![Schermafbeelding van het venster RBAC-gebruikers van Azure AD Connect Health](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selecteer **OK**.<br>
6. Nadat de roltoewijzing is voltooid, worden de gebruikers en groepen in de lijst weergegeven.<br>
   ![Schermafbeelding van het venster RBAC-gebruikers van Azure AD Connect Health, met nieuwe gebruikers gemarkeerd](./media/how-to-connect-health-operations/RBAC_user_list.png)

Nu hebben de vermelde gebruikers en groepen toegang, afhankelijk van hun toegewezen rollen.

> [!NOTE]
> * Globale beheerders hebben altijd volledige toegang tot alle bewerkingen, maar globale beheerdersaccounts zijn niet aanwezig in de voorgaande lijst.
> * De functie Gebruikers uitnodigen wordt niet ondersteund in azure AD Connect-status.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Stap 3: Deel de bladelocatie met gebruikers of groepen
1. Nadat u machtigingen hebt toegewezen, heeft een gebruiker toegang tot Azure AD Connect Health door [hier](https://aka.ms/aadconnecthealth)naartoe te gaan.
2. Op het blad kan de gebruiker het mes, of verschillende delen ervan, aan het dashboard vastmaken. Klik op het pictogram **Vastmaken aan dashboard.**<br>
   ![Schermafbeelding van het RBAC-pinblad van Azure AD Connect Health, met het pictogram pin gemarkeerd](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Een gebruiker met de toegewezen Reader-rol kan de Azure AD Connect-statusextensie niet downloaden van de Azure Marketplace. De gebruiker kan niet de nodige "create" bewerking uitvoeren om dit te doen. De gebruiker kan nog steeds naar het blad door naar de voorgaande link. Voor volgend gebruik kan de gebruiker het mes vastmaken aan het dashboard.
>
>

### <a name="remove-users-or-groups"></a>Gebruikers of groepen verwijderen
U een gebruiker of een groep verwijderen die is toegevoegd aan Azure AD Connect Health RBAC. Klik met de rechtermuisknop op de gebruiker of groep en selecteer **Verwijderen**.<br>
![Schermafbeelding van het venster RBAC-gebruikers van Azure AD Connect Health, met Verwijderen gemarkeerd](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Einde van de sectie RBAC)

## <a name="next-steps"></a>Volgende stappen
* [Azure AD Connect Health (Engelstalig)](whatis-hybrid-identity-health.md)
* [Installatie van Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health gebruiken met AD FS](how-to-connect-health-adfs.md)
* [Azure AD Connect-status gebruiken voor synchronisatie](how-to-connect-health-sync.md)
* [Azure AD Connect Health gebruiken met AD DS](how-to-connect-health-adds.md)
* [Veelgestelde vragen over Azure AD Connect Health](reference-connect-health-faq.md)
* [Versiegeschiedenis van Azure AD Connect Health](reference-connect-health-version-history.md)
