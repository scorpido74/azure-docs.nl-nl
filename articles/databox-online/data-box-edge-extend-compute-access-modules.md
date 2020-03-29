---
title: Compute-netwerk beheren op Azure Data Box Edge om toegang te krijgen tot modules| Microsoft Documenten
description: Beschrijft hoe u het rekennetwerk op uw Data Box Edge uitbreidt tot toegang tot modules via een extern IP-adres.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917233"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Compute-netwerk inschakelen op uw Azure Data Box Edge

In dit artikel wordt beschreven hoe de modules die op uw Azure Data Box Edge worden uitgevoerd, toegang hebben tot het rekennetwerk dat op het apparaat is ingeschakeld.

Als u het netwerk wilt configureren, neemt u de volgende stappen:

- Een netwerkinterface inschakelen op uw Data Box Edge-apparaat voor compute
- Een module toevoegen om toegang te krijgen tot het compute-netwerk op uw Data Box Edge
- Controleren of de module toegang heeft tot de ingeschakelde netwerkinterface

In deze zelfstudie gebruikt u een webserver-appmodule om het scenario aan te tonen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u het volgende nodig:

- Een Data Box Edge-apparaat met apparaatconfiguratie voltooid.
- U hebt **de rekenstap configureren** volgens de [zelfstudie: gegevens transformeren met Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) op uw apparaat. Uw apparaat moet een bijbehorende IoT Hub-bron, een IoT-apparaat en een IoT Edge-apparaat hebben.

## <a name="enable-network-interface-for-compute"></a>Netwerkinterface inschakelen voor compute

Als u toegang wilt krijgen tot de modules die op uw apparaat worden uitgevoerd via een extern netwerk, moet u een IP-adres toewijzen aan een netwerkinterface op uw apparaat. U deze rekeninstellingen beheren vanaf uw lokale web-gebruikersinterface.

Volg de volgende stappen op uw lokale web-gebruikersinterface om rekeninstellingen te configureren.

1. Ga in de lokale webgebruikersinterface naar **Configuratie > Compute-instellingen**.  

2. **Schakel** de netwerkinterface in die u wilt gebruiken om verbinding te maken met een rekenmodule die u op het apparaat uitvoert.

    - Als u statische IP-adressen gebruikt, voert u een IP-adres in voor de netwerkinterface.
    - Als u DHCP gebruikt, worden de IP-adressen automatisch toegewezen. In dit voorbeeld wordt DHCP gebruikt.

    ![Rekeninstellingen inschakelen 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Selecteer **Toepassen** om de instellingen toe te passen. Noteer het IP-adres dat aan de netwerkinterface is toegewezen als u DHCP gebruikt.

    ![Rekeninstellingen inschakelen](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Module webserver-app toevoegen

Volg de volgende stappen om een webserver-appmodule toe te voegen op uw Data Box Edge-apparaat.

1. Ga naar de IoT Hub-bron die is gekoppeld aan uw Data Box Edge-apparaat en selecteer vervolgens **IoT Edge-apparaat**.
2. Selecteer het IoT Edge-apparaat dat is gekoppeld aan uw Data Box Edge-apparaat. Selecteer **Modules instellen**op de **apparaatgegevens**. Selecteer **bij Modules toevoegen**de optie + **Toevoegen** en selecteer vervolgens **IoT Edge Module**.
3. In het **aangepaste ieblad van de IoT Edge-modules:**

    1. Geef een **naam op** voor de webservermodule van uw app die u wilt implementeren.
    2. Geef een **Image URI** voor uw moduleafbeelding. Een module die overeenkomt met de opgegeven naam en tags wordt opgehaald. In dit `nginx:stable` geval zal trekken een stabiele nginx afbeelding (gelabeld als stabiel) uit de openbare [Docker repository](https://hub.docker.com/_/nginx/).
    3. Plak in de **opties voor containermaken**de volgende voorbeeldcode:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Met deze configuratie hebt u toegang tot de module met behulp van het ip-gegevensnetwerk via *http* op TCP-poort 8080 (met de standaardwebserverpoort 80).

        ![Poortgegevens opgeven in het aangepaste moduleblad van IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Selecteer **Opslaan**.

## <a name="verify-module-access"></a>Moduletoegang verifiëren

1. Controleer of de module is geïmplementeerd en wordt uitgevoerd. Op de pagina **Apparaatdetails** moet op het tabblad **Modules** de runtime-status van de module **worden uitgevoerd.**  
2. Maak verbinding met de module van de webserver-app. Open een browservenster en typ:

    `http://<compute-network-IP-address>:8080`

    U moet zien dat de webserver-app wordt uitgevoerd.

    ![Verbinding met module via opgegeven poort controleren](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Gebruikers beheren via Azure Portal](data-box-edge-manage-users.md).
