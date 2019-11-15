---
title: Fysieke servers voorbereiden voor evaluatie met Azure Migrate server-evaluatie
description: Hierin wordt beschreven hoe u de evaluatie en migratie van fysieke servers naar Azure voorbereidt met behulp van Azure Migrate server-evaluatie.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 176dfdabeee6299bba0eb17085be25ced3e97993
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091904"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Voor bereiding voor de evaluatie en migratie van fysieke servers naar Azure

In dit artikel wordt beschreven hoe u de evaluatie van on-premises fysieke servers kunt voorbereiden met [Azure migrate](migrate-services-overview.md).


> [!NOTE]
> Als u nog enkele van deze functies in de Azure Migrate Portal ziet, loopt u vast. Deze worden in de volgende week weer gegeven.

[Azure migrate](migrate-overview.md) biedt een hub aan hulpprogram ma's waarmee u apps, infra structuur en werk belastingen op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor) van derden. 

Deze zelf studie is de eerste in een serie die laat zien hoe u fysieke servers kunt beoordelen met Azure Migrate. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Stel machtigingen in voor uw Azure-account en-resources om met Azure Migrate te werken.
> * On-premises fysieke servers voorbereiden voor Server evaluatie.


> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Bekijk voor gedetailleerde instructies de evaluatie van de fysieke servers met uitleg.


Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prepare-azure"></a>Azure voorbereiden

### <a name="azure-permissions"></a>Azure-machtigingen

U moet machtigingen instellen voor Azure Migrate-implementatie.

- Machtigingen voor uw Azure-account om een Azure Migrate project te maken. 
- Machtigingen voor uw account om het Azure Migrate apparaat te registreren. Het apparaat wordt gebruikt voor Hyper-V-detectie en-migratie. Tijdens de registratie van het apparaat maakt Azure Migrate twee Azure Active Directory (Azure AD)-apps waarmee het apparaat op unieke wijze wordt geïdentificeerd:
    - De eerste app communiceert met Azure Migrate service-eind punten.
    - De tweede app heeft toegang tot een Azure Key Vault die tijdens de registratie is gemaakt voor het opslaan van Azure AD-app-informatie en toestel configuratie-instellingen.



### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

Controleer of u gemachtigd bent om een Azure Migrate project te maken.

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.


### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het apparaat te registreren

Met een van de volgende methoden kunt u machtigingen voor Azure Migrate toewijzen om de Azure AD-apps te maken tijdens de registratie van het apparaat:

- Een Tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de Tenant om Azure AD-apps te maken en registreren.
- Een Tenant/globale beheerder kan de rol van toepassings ontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

Het is een goed idee dat:

- De apps hebben geen andere toegangs machtigingen voor het abonnement dan de hierboven beschreven.
- U hebt deze machtigingen alleen nodig wanneer u een nieuw apparaat registreert. U kunt de machtigingen verwijderen nadat het apparaat is ingesteld. 


#### <a name="grant-account-permissions"></a>Account machtigingen verlenen

De Tenant/globale beheerder kan machtigingen als volgt verlenen:

1. In azure AD moet de Tenant/globale beheerder naar **Azure Active Directory** > **gebruikers** > **gebruikers instellingen**navigeren.
2. De beheerder moet **app-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Dit is een standaard instelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Rol toepassings ontwikkelaar toewijzen 

De Tenant/globale beheerder kan de rol van toepassings ontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Evaluatie van fysieke server voorbereiden

Voor de voor bereiding op de beoordeling van de fysieke server moet u de instellingen voor de fysieke server controleren en instellingen controleren voor de implementatie van het apparaat:

### <a name="verify-physical-server-settings"></a>Controleer de instellingen van de fysieke server

1. Controleer de vereisten voor de [fysieke server](migrate-support-matrix-physical.md#assessment-physical-server-requirements) voor Server evaluatie.
2. Zorg ervoor dat de [vereiste poorten](migrate-support-matrix-physical.md#assessment-port-requirements) op fysieke servers zijn geopend.


### <a name="verify-appliance-settings"></a>Instellingen voor toestellen controleren

Voordat u het Azure Migrate apparaat instelt en de evaluatie begint in de volgende zelf studie, moet u de implementatie van het apparaat voorbereiden.

1. [Controleer](migrate-support-matrix-physical.md#assessment-appliance-requirements) de vereisten van het apparaat.
2. [Bekijk](migrate-support-matrix-physical.md#assessment-appliance-url-access) de Azure-url's die het apparaat nodig heeft om toegang te krijgen.
3. Bekijk de gegevens die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Noteer](migrate-support-matrix-physical.md#assessment-port-requirements) de toegangs vereisten voor poorten voor het apparaat.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Een account instellen voor detectie van fysieke servers

Azure Migrate heeft machtigingen nodig om on-premises servers te detecteren.

- **Windows:** Stel een lokale gebruikers account in op alle Windows-servers die u wilt toevoegen in de detectie. Het gebruikers account moet worden toegevoegd aan de volgende groepen:-extern beheer gebruikers-prestatie meter gebruikers-prestaties logboek gebruikers
- **Linux:** U hebt een hoofd account nodig op de Linux-servers die u wilt detecteren.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:
 
> [!div class="checklist"] 
> * Stel de machtigingen voor het Azure-account in.
> * Voor bereide fysieke servers voor evaluatie.

Ga door naar de volgende zelf studie om een Azure Migrate project te maken en fysieke servers te beoordelen voor migratie naar Azure

> [!div class="nextstepaction"] 
> [Fysieke servers beoordelen](./tutorial-assess-physical.md) 
