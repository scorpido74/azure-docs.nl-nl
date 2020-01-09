---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564782"
---
## <a name="create-a-container-registry"></a>Een containerregister maken

In deze zelf studie gebruikt u de extensie Azure IoT-Hulpprogram Ma's voor het bouwen van een module en het maken van een **container installatie kopie** van de bestanden. Vervolgens pusht u deze installatiekopie naar een **register** waarin uw installatiekopieën worden opgeslagen en beheerd. Tot slot implementeert u de installatiekopie uit het register voor uitvoering op uw IoT Edge-apparaat.

U kunt een Docker-register gebruiken om de containerinstallatiekopieën op te slaan. Twee populaire Docker-registerservices zijn [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) en [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). In deze zelfstudie wordt Azure Container Registry gebruikt.

Als u nog geen containerregister hebt, volgt u deze stappen om een nieuw containerregister te maken in Azure:

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Een resource maken** > **Containers** > **Container Registry**.

2. Geef de volgende waarden op om uw containerregister te maken:

   | Veld | Waarde |
   | ----- | ----- |
   | Registernaam | Geef hier een unieke naam op. |
   | Abonnement | Selecteer een abonnement in de vervolgkeuzelijst. |
   | Resourcegroep | Het wordt aangeraden om dezelfde resourcegroep te gebruiken voor alle test-resources die u maakt met de snelstartgidsen en zelfstudies voor IoT Edge, zoals **IoTEdgeResources**. |
   | Locatie | Kies een locatie dicht bij u in de buurt. |
   | Beheerder | Stel deze optie in op **Inschakelen**. |
   | SKU | Selecteer **Basic**. |

3. Selecteer **Maken**.

4. Nadat u het container register hebt gemaakt, bladert u naar het en selecteert u in het linkerdeel venster de optie **toegangs sleutels** in het menu dat zich onder **instellingen**bevindt.

5. Kopieer de waarden voor de **aanmeldings server**, de **gebruikers naam**en het **wacht woord** en sla ze op een gemakkelijke plek op. U gebruikt deze waarden in deze zelf studie om toegang te bieden tot het container register.

   ![Aanmeldings server, gebruikers naam en wacht woord voor container register kopiëren](./media/iot-edge-create-container-registry/registry-access-key.png)