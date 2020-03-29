---
title: De azure IoT-oplossingensite gebruiken - Azure | Microsoft Documenten
description: Beschrijft hoe u de AzureIoTSolutions.com website gebruiken om uw oplossingsversneller te implementeren.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447427"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Gebruik de azureiotsolutions.com site om uw oplossingsversneller te implementeren

U Azure IoT-oplossingsversnellers vanaf [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators)implementeren in uw Azure-abonnement. AzureIoTSolutions.com host zowel de microsoft open source en partner oplossing accelerators. Deze oplossingsversnellers sluiten aan bij de [Azure IoT Reference Architecture.](https://aka.ms/iotrefarchitecture) U de site gebruiken om snel een oplossingsversneller te implementeren als demo- of productieomgeving.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Als u meer controle over het implementatieproces nodig hebt, u de CLI gebruiken [om een oplossingsversneller te implementeren.](iot-accelerators-remote-monitoring-deploy-cli.md)

U de oplossingsversnellers implementeren in de volgende configuraties:

* **Standaard**: Een uitgebreide implementatie van de infrastructuur voor het ontwikkelen van een productieomgeving. De Azure Container Service implementeert de microservices in diverse virtuele Azure-machines. Kubernetes deelt de Docker-containers in die de afzonderlijke microservices hosten.
* **Basis:** een versie met lagere kosten voor een demonstratie of om een implementatie te testen. Alle microservices worden geïmplementeerd op een enkele virtuele Azure-machine.
* **Lokaal**: Een lokale machine-implementatie voor testen en ontwikkeling. Deze aanpak implementeert de microservices op een lokale Docker-container en maakt verbinding met de IoT Hub, Azure Cosmos DB en Azure-opslagservices in de cloud.

Elk van de oplossingsversnellers maakt gebruik van een andere combinatie van Azure-services, zoals IoT Hub, Azure Stream Analytics en Cosmos DB. Ga voor meer informatie naar [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) en selecteer een oplossingsversneller.

## <a name="sign-in-at-azureiotsolutionscom"></a>Log azureiotsolutions.com hier in

Voordat u een oplossingsversneller implementeren, moet u zich aanmelden bij AzureIoTSolutions.com met referenties die zijn gekoppeld aan een Azure-abonnement. Als uw account is gekoppeld aan meer dan één AD-tenant (Microsoft Azure Active Directory), u de **vervolgkeuzelijst Accountselectie** gebruiken om de te gebruiken map te kiezen.

Uw machtigingen voor het implementeren van oplossingsversnellers, het beheren van gebruikers en het beheren van Azure-services zijn afhankelijk van uw rol in de geselecteerde map. Veelvoorkomende Azure AD-rollen die zijn gekoppeld aan de oplossingsversnellers zijn:

* **Globale beheerder:** er kunnen veel [globale beheerders](../active-directory/users-groups-roles/directory-assign-admin-roles.md) zijn per Azure AD-tenant:

  * Wanneer u een Azure AD-tenant maakt, bent u standaard de globale beheerder van die tenant.
  * De globale beheerder kan basis- en standaardoplossingsversnellers implementeren.

* **Domeingebruiker**: Er kunnen veel domeingebruikers zijn per Azure AD-tenant. Een domeingebruiker kan een basisoplossingsversneller implementeren.

* **Gastgebruiker**: Er kunnen veel gastgebruikers zijn per Azure AD-tenant. Gastgebruikers kunnen geen oplossingsversneller implementeren in de Azure AD-tenant.

Zie de volgende bronnen voor meer informatie over gebruikers en rollen in Azure AD:

* [Gebruikers maken in Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Gebruikers toewijzen aan apps](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Kies uw apparaat

De AzureIoTSolutions.com sitekoppelingen naar de [azure certified for IoT-apparaatcatalogus.](https://catalog.azureiotsolutions.com/)

De catalogus bevat honderden gecertificeerde IoT-hardwareapparaten die u aansluiten op uw oplossingsversnellers om te beginnen met het bouwen van uw IoT-oplossing.

![Apparaatcatalogus](media/iot-accelerators-permissions/devicecatalog.png)

Als u een hardwarefabrikant bent, klikt u op **Partner worden** om meer te weten te komen over samenwerking met Microsoft in het Certified for IoT-programma.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de quickstarts om een van de oplossingsverbeteringen uit te proberen:

* [Een externe bewakingsoplossing uitproberen](quickstart-remote-monitoring-deploy.md)
* [Een oplossing voor verbonden factory uitproberen](quickstart-connected-factory-deploy.md)
* [Een oplossing voor predictief onderhoud uitproberen](quickstart-predictive-maintenance-deploy.md)
* [Een oplossing voor apparaatsimulatie uitproberen](quickstart-device-simulation-deploy.md)
