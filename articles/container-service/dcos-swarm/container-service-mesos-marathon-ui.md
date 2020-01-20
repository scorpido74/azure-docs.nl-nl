---
title: KEUR Een Azure DC/OS-cluster beheren met de marathon-gebruikers interface
description: Implementeer containers naar een Azure Container Service-cluster met behulp van de webgebruikersinterface van Marathon.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b251096915506c3c7a4eebf45b6a03e24779a3d8
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277805"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>KEUR Een Azure Container Service DC/OS-cluster beheren via de marathon-webgebruikersinterface

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS biedt een omgeving voor het implementeren en schalen van geclusterde werkbelastingen terwijl de onderliggende hardware wordt onttrokken. Op de DC/OS ligt een framework dat de planning en uitvoering van rekenworkloads regelt.

In dit document wordt beschreven hoe u aan de slag gaat met het implementeren van containers met marathon. 


## <a name="prerequisites"></a>Vereisten
Voer het uitvoeren van deze voorbeelden hebt u een DC/OS-cluster nodig dat is geconfigureerd in Azure Container Service. U hebt ook een externe verbinding met dit cluster nodig. Zie de volgende artikelen voor meer informatie over deze items:

* [Een Azure Container Service-cluster implementeren](container-service-deployment.md)
* [Verbinding maken met een Azure Container Service-cluster](../container-service-connect.md)

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u tunnelt naar het DC/OS-cluster via uw lokale poort 80.
>

## <a name="explore-the-dcos-ui"></a>De DC/OS-gebruikersinterface verkennen
Als er een SSH-tunnel (Secure Shell) is [ingesteld](../container-service-connect.md), gaat u naar http:\//localhost/. Hierdoor wordt de DC/OS-webgebruikersinterface geladen met informatie over het cluster, zoals gebruikte resources, actieve agents en actieve services.

![DC/OS-webgebruikersinterface](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>De Marathon-gebruikersinterface verkennen
Als u de marathon-gebruikers interface wilt weer geven, gaat u naar http:\//localhost/marathon. In dit scherm kunt u een nieuwe container of een andere toepassing starten op het DC/OS-cluster in Azure Container Service. U ziet ook informatie over actieve containers en toepassingen.  

![Marathon-gebruikersinterface](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Een met Docker ingedeelde container implementeren
Voor het implementeren van een nieuwe container via Marathon, klikt u op **Toepassing maken** en voert u de volgende gegevens op de tabbladen van het formulier in:

| Veld | Waarde |
| --- | --- |
| Id |nginx |
| Geheugen | 32 |
| afbeelding |nginx |
| Netwerk |Overbrugd |
| Hostpoort |80 |
| Protocol |TCP |

![Nieuwe toepassingsgebruikersinterface: algemeen](./media/container-service-mesos-marathon-ui/dcos4.png)

![Nieuwe toepassingsgebruikersinterface: Docker-container](./media/container-service-mesos-marathon-ui/dcos5.png)

![Nieuwe toepassingsgebruikersinterface: poort- en servicedetectie](./media/container-service-mesos-marathon-ui/dcos6.png)

Als u de containerpoort statisch wilt toewijzen aan een poort op de agent, gebruikt u de JSON-modus. U doet dit door de wizard Nieuwe toepassing over te schakelen naar de **JSON-modus** met behulp van de wisselknop. Voer daarna de volgende instelling in onder de sectie `portMappings` van de definitie van de toepassing. In dit voorbeeld wordt poort 80 van de container gebonden aan poort 80 van de DC/OS-agent. Nadat u deze wijziging hebt aangebracht, kunt u deze wizard uit JSON-modus halen.

```none
"hostPort": 80,
```

![Nieuwe toepassingsgebruikersinterface: voorbeeld van poort 80](./media/container-service-mesos-marathon-ui/dcos13.png)

Als u statuscontroles wilt inschakelen, stelt u een pad in op het tabblad **Statuscontroles**.

![Nieuwe toepassingsgebruikersinterface: statuscontroles](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

Het DC/OS-cluster wordt geïmplementeerd met een set persoonlijke en openbare agents. Om te zorgen dat het cluster toegang heeft tot toepassingen vanaf internet, moet u de toepassingen implementeren naar een openbare agent. Hiertoe selecteert u het tabblad **Optioneel** van de wizard Nieuwe toepassing en voert u **slave_public** in bij de **Geaccepteerde resourcerollen**.

Klik vervolgens op **Toepassing maken**.

![Nieuwe toepassingsgebruikersinterface: instellingen openbare agent](./media/container-service-mesos-marathon-ui/dcos14.png)

Op de hoofdpagina van Marathon ziet u de implementatiestatus voor de container. Eerst wordt de status **Implementeren** weergegeven. Wanneer de implementatie is voltooid, verandert de status in **Uitvoeren**.

![Hoofdpagina van Marathon: implementatiestatus van container](./media/container-service-mesos-marathon-ui/dcos7.png)

Wanneer u terugschakelt naar de DC/OS Web UI (http:\//localhost/), ziet u dat een taak (in dit geval een container met docker-indeling) wordt uitgevoerd op het DC/OS-cluster.

![DC/OS-webgebruikersinterface: taak die wordt uitgevoerd op het cluster](./media/container-service-mesos-marathon-ui/dcos8.png)

U kunt het clusterknooppunt waarop de taak wordt uitgevoerd weergeven door op het tabblad **Knooppunten** te klikken.

![DC/OS-webgebruikersinterface: clusterknooppunt van taak](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>De container bereiken

In dit voor beeld wordt de toepassing uitgevoerd op een knoop punt van een open bare agent. U bereikt de toepassing via internet door te bladeren naar de FQDN van de agent van het cluster: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`, waarbij:

* **DNSPREFIX** het DNS-voorvoegsel is dat is opgegeven tijdens de implementatie van het cluster.
* **REGION** de regio is waarin uw resourcegroep zich bevindt.

    ![Nginx van Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Volgende stappen
* [Werken met DC/OS en de Marathon API](container-service-mesos-marathon-rest.md)

* Gedetailleerde informatie over de Azure-containerservice met Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
