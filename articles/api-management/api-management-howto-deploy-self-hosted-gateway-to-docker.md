---
title: Zelf gehoste Azure API Management-gateway implementeren voor Docker | Microsoft Documenten
description: Meer informatie over het implementeren van een zelfgehoste Azure API Management-gateway naar Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768500"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Een azure API Management self-hosted gateway implementeren voor Docker

In dit artikel vindt u de stappen voor het implementeren van een zelf gehoste Azure API Management-gateway in een Docker-omgeving.

> [!NOTE]
> Zelf gehoste gateway-functie is in preview. Tijdens de preview is de zelf gehoste gateway alleen beschikbaar in de lagen Developer en Premium zonder extra kosten. De ontwikkelaarslaag is beperkt tot één zelf gehoste gateway-implementatie.

## <a name="prerequisites"></a>Vereisten

- De volgende quickstart voltooien: [een azure API-beheerexemplaar maken](get-started-create-service-instance.md)
- Maak een Docker-omgeving. [Docker voor desktop](https://www.docker.com/products/docker-desktop) is een goede optie voor ontwikkelings- en evaluatiedoeleinden. Zie [Docker-documentatie](https://docs.docker.com) voor informatie over alle Docker-edities, hun functies en uitgebreide documentatie over Docker zelf.
- [Een gatewaybron inrichten in uw API-beheerexemplaar](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Self-hosted gateway is verpakt als een x86-64 Linux-gebaseerde Docker container.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>De zelfgehoste gateway naar Docker implementeren

1. Selecteer **Gateways** onder **Instellingen**.
2. Selecteer de gatewaybron die u wilt implementeren.
3. Selecteer **Implementatie**.
4. Houd er rekening mee dat een nieuw token in het tekstvak **Token** automatisch is gegenereerd met de waarden **standaard verloop** en **geheime sleutel.** Pas een van beide of beide aan indien gewenst en selecteer **Genereren** om een nieuw token te maken.
4. Controleer of **Docker** is geselecteerd onder **Implementatiescripts**.
5. Selecteer de koppeling **met env.conf-bestanden** naast de **omgeving** om het bestand te downloaden.
6. Selecteer **het kopieerpictogram** aan de rechterkant van het tekstvak **Uitvoeren** om de opdracht Docker op te slaan in het klembord.
7. Plak de opdracht op het terminalvenster (of opdracht). Pas indien nodig de poorttoewijzingen en containernaam aan. Houd er rekening mee dat de opdracht verwacht dat het gedownloade omgevingsbestand aanwezig is in de huidige map.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Voer de opdracht uit. De opdracht instrueert uw Docker-omgeving om de container uit te voeren, met behulp van zelf gehoste gatewayafbeelding die is gedownload van het Microsoft Container Registry, en om de HTTP-poorten (8080) en HTTPS (8081) van de container in kaart te brengen naar poorten 80 en 443 op de host.
9. Voer de onderstaande opdracht uit om te controleren of de gatewaypod wordt uitgevoerd:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Ga terug naar azure portal en bevestig dat gatewaynode die u zojuist hebt geïmplementeerd, de status van een gezonde status rapporteert.

![gatewaystatus](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Gebruik <code>console docker container logs <gateway-name></code> de opdracht om een momentopname van zelf gehost gatewaylogboek weer te geven.
>
> Gebruik <code>docker container logs --help</code> de opdracht om alle opties voor het weergeven van logboeken te bekijken.

## <a name="next-steps"></a>Volgende stappen

* Zie Azure [API Management zelf gehostgatewayoverzicht](self-hosted-gateway-overview.md)voor meer informatie over de zelf gehoste gateway.
* [Aangepaste domeinnaam configureren voor de zelf gehoste gateway.](api-management-howto-configure-custom-domain-gateway.md)
