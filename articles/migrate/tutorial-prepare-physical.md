---
title: Fysieke servers voorbereiden op beoordeling/migratie met Azure Migrate
description: Meer informatie over hoe u zich voorbereiden op beoordeling/migratie van fysieke servers met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 539e25f8b6cc92674fef567de6e6de16d0a9394a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535278"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Voorbereiden op beoordeling en migratie van fysieke servers naar Azure

In dit artikel wordt beschreven hoe u zich voorbereiden op de beoordeling van on-premises fysieke servers met [Azure Migrate.](migrate-services-overview.md)

[Azure Migrate](migrate-overview.md) biedt een hub met hulpprogramma's waarmee u apps, infrastructuur en workloads ontdekken, beoordelen en migreren naar Microsoft Azure. De hub bevat Azure Migrate-hulpprogramma's en isv-aanbiedingen (independent software vendor) van derden. 

Deze zelfstudie is de eerste in een reeks die u laat zien hoe u fysieke servers met Azure Migrate beoordelen. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure voorbereiden. Machtigingen instellen voor uw Azure-account en resources om met Azure Migreren te werken.
> * Bereid on-premises fysieke servers voor op serverbeoordeling.


> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een proof-of-concept instellen. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. Bekijk voor gedetailleerde instructies de how-tos voor beoordeling van fysieke servers.


Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.


## <a name="prepare-azure-for-server-assessment"></a>Azure voorbereiden op serverbeoordeling

Azure instellen voor gebruik met Azure Migrate. 

**Taak** | **Details** 
--- | --- 
**Een Azure-migratieproject maken** | Uw Azure-account heeft inzender- of eigenaarmachtigingen nodig om een project te maken. 
**Resourceproviders registreren (alleen beoordeling)** | Azure Migrate maakt gebruik van een lichtgewicht Azure Migrate-toestel om machines te detecteren en te beoordelen met Azure Migrate:Server Assessment.<br/><br/> Tijdens de registratie van apparaten worden resourceproviders geregistreerd met het abonnement dat in het toestel is gekozen. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Als u de resourceproviders wilt registreren, hebt u een rol inzender of eigenaar bij het abonnement nodig.
**Azure AD-app maken (alleen beoordeling)** | Wanneer azure migrate het toestel registreert, maakt azure migrate een Azure Active Directory-app (Azure AD) die wordt gebruikt voor communicatie tussen de agents die op het toestel worden uitgevoerd met hun respectieve services die op Azure worden uitgevoerd. [Meer informatie](migrate-appliance-architecture.md#appliance-registration).<br/><br/> U hebt machtigingen nodig om Azure AD-apps te maken (beschikbaar in de rol Toepassingsontwikkelaar).


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen om project te maken 

Controleer of u machtigingen hebt om een Azure Migrate-project te maken.

1. Open het abonnement in de Azure-portal en selecteer **Toegangsbeheer (IAM)**.
2. Zoek **in Toegang controleren**het relevante account en klik erop om machtigingen weer te geven.
3. U moet **machtigingen voor inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, werkt u samen met de eigenaar om de rol toe te wijzen.


### <a name="assign-permissions-to-register-the-appliance"></a>Machtigingen toewijzen om het toestel te registreren 

U machtigingen toewijzen voor Azure Migrate om de Azure AD-app te maken tijdens de toestelregistratie, met een van de volgende methoden:

- Een tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de tenant om Azure AD-apps te maken en te registreren.
- Een tenant/globale beheerder kan de rol Toepassingsontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

> [!NOTE]
> - De app heeft geen andere toegangsmachtigingen voor het abonnement dan de hierboven beschreven.
> - U hebt deze machtigingen alleen nodig wanneer u een nieuw toestel registreert. U de machtigingen verwijderen nadat het apparaat is ingesteld.


#### <a name="grant-account-permissions"></a>Accountmachtigingen verlenen

De tenant/globale beheerder kan als volgt machtigingen verlenen:

1. In Azure AD moet de tenant/globale beheerder naar > **gebruikersinstellingen**van Azure Active > **Directory-gebruikers**navigeren. **Azure Active Directory**
2. De beheerder moet **app-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Dit is een standaardinstelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Rol Toepassingsontwikkelaar toewijzen

De tenant/globale beheerder kan de rol Toepassingsontwikkelaar toewijzen aan een account. [Meer informatie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-azure-for-physical-server-migration"></a>Azure voorbereiden op fysieke servermigratie

Bereid Azure voor om fysieke servers te migreren met servermigratie.

**Taak** | **Details**
--- | ---
**Een Azure-migratieproject maken** | Uw Azure-account heeft machtigingen voor bijdragen de bijdrage er of eigenaar nodig om een project te maken.
**Machtigingen voor uw Azure-account verifiëren** | Uw Azure-account heeft machtigingen nodig om een VM te maken en naar een door Azure beheerde schijf te schrijven.
**Een Azure-netwerk maken** | Een netwerk instellen in Azure.


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen om project te maken

1. Open het abonnement in de Azure-portal en selecteer **Toegangsbeheer (IAM)**.
2. Zoek **in Toegang controleren**het relevante account en klik erop om machtigingen weer te geven.
3. U moet **machtigingen voor inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, werkt u samen met de eigenaar om de rol toe te wijzen.


### <a name="assign-azure-account-permissions"></a>Azure-accountmachtigingen toewijzen

Wijs de rol Virtuele machineinzender toe aan het Azure-account. Dit biedt machtigingen voor:

    - Het maken van een VM in de geselecteerde resourcegroep.
    - Het maken van een VM in het geselecteerde virtuele netwerk.
    - Schrijf naar een door Azure beheerde schijf. 

### <a name="create-an-azure-network"></a>Een Azure-netwerk maken

Een Virtueel Azure-netwerk (VNet) [instellen.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Wanneer u deze nabootst naar Azure, worden Azure VM's gemaakt en samengevoegd met het Azure VNet dat u opgeeft wanneer u migratie instelt.


## <a name="prepare-for-physical-server-assessment"></a>Voorbereiden op fysieke serverbeoordeling

Als u zich wilt voorbereiden op de beoordeling van de fysieke server, moet u de fysieke serverinstellingen controleren en de instellingen voor de implementatie van het toestel verifiëren:

### <a name="verify-physical-server-settings"></a>Fysieke serverinstellingen verifiëren

1. Controleer [de fysieke serververeisten](migrate-support-matrix-physical.md#physical-server-requirements) voor serverbeoordeling.
2. Zorg ervoor dat de [vereiste poorten](migrate-support-matrix-physical.md#port-access) zijn geopend op fysieke servers.


### <a name="verify-appliance-settings"></a>Toestelinstellingen controleren

Bereid u voor voordat u het Azure Migrate-toestel en de beginbeoordeling in de volgende zelfstudie instelt, zich voorbereiden op de implementatie van apparaten.

1. [Controleer](migrate-appliance.md#appliance---physical) de vereisten van het toestel voor fysieke servers.
2. Bekijk de Azure URL's die het toestel nodig heeft om toegang te krijgen in de [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls)
3. [Controleer](migrate-appliance.md#collected-data---vmware) of het apparaat zal verzamelen tijdens de ontdekking en beoordeling.
4. [Note port](migrate-support-matrix-physical.md#port-access) access requirements physical server assessment.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Een account instellen voor het detecteren van fysieke servers

Azure Migrate heeft machtigingen nodig om on-premises servers te detecteren.

- **Windows:** Stel een lokaal gebruikersaccount in op alle Windows-servers die u in de detectie wilt opnemen. Het gebruikersaccount moet worden toegevoegd aan de volgende groepen: - Gebruikers van extern beheer - Gebruikers van prestatiemonitor - gebruikers van prestatielogboeken
- **Linux:** Je hebt een root-account nodig op de Linux-servers die je wilt ontdekken.

## <a name="prepare-for-physical-server-migration"></a>Voorbereiden op fysieke servermigratie

Bekijk de vereisten voor migratie van fysieke servers.

> [!NOTE]
> Bij het migreren van fysieke machines gebruikt Azure Migrate:Server Migration dezelfde replicatiearchitectuur als op agent gebaseerd disaster recovery in de Azure Site Recovery-service en delen sommige onderdelen dezelfde codebasis. Sommige inhoud kan worden gekoppeld aan documentatie voor siteherstel.

- [Bekijk](migrate-support-matrix-physical-migration.md#physical-server-requirements) fysieke serververeisten voor migratie.
- Azure Migreren:Servermigratie maakt gebruik van een replicatieserver voor fysieke servermigratie:
    - [Controleer](migrate-replication-appliance.md#appliance-requirements) de implementatievereisten voor het replicatietoestel en de [opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het toestel.
    - Controleer de [Azure-URL's die](migrate-appliance.md#url-access) nodig zijn voor het replicatietoestel om toegang te krijgen tot openbare en overheidsclouds.
    - Controleer de toegangsvereisten voor de toegangsvereisten voor het replicatietoestel (migrate-replication-appliance.md#port-access).




## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Azure-accountmachtigingen instellen.
> * Voorbereide fysieke servers voor beoordeling.

Ga verder naar de volgende zelfstudie om een Azure Migrate-project te maken en fysieke servers te beoordelen op migratie naar Azure

> [!div class="nextstepaction"]
> [Fysieke servers evalueren](./tutorial-assess-physical.md)
