---
title: Zelf-hostende Azure API Management-Gateway implementeren naar docker | Microsoft Docs
description: Meer informatie over het implementeren van een zelf-hostende Azure API Management gateway naar docker
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
ms.openlocfilehash: fc7e0f7e4e0e80a030a437bc4f0f13360595f32e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747704"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Een Azure API Management zelf-hostende Gateway implementeren naar docker

Dit artikel bevat de stappen voor het implementeren van een zelf-hostende Azure API Management-Gateway in een docker-omgeving.

> [!NOTE]
> De zelf-hostende gateway functie is beschikbaar als preview-versie. Tijdens de preview-periode is de zelf-hostende gateway alleen beschikbaar in de lagen ontwikkelaar en Premium zonder extra kosten. De Developer-laag is beperkt tot één zelf-hostende gateway-implementatie.

## <a name="prerequisites"></a>Vereisten

- Lees de volgende snelstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
- Maak een docker-omgeving. [Docker voor desktop](https://www.docker.com/products/docker-desktop) is een goede optie voor ontwikkelings-en evaluatie doeleinden. Raadpleeg [docker-documentatie](https://docs.docker.com) voor informatie over alle docker-edities, hun functies en uitgebreide documentatie over docker zelf.
- [Een gateway bron inrichten in uw API Management-exemplaar](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Zelf-hostende gateway is verpakt als een op Linux gebaseerde docker-container.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>De zelf-hostende Gateway implementeren naar docker

1. Selecteer **gateways** onder **instellingen**.
2. Selecteer de gateway resource die u wilt implementeren.
3. Selecteer **implementatie**.
4. Houd er rekening mee dat een nieuw token in het tekstvak **token** voor u automatisch wordt gegenereerd met de standaard waarden voor de **verlopende** en **geheime sleutel** . Pas een van beide of beide indien gewenst aan en selecteer **genereren** om een nieuw token te maken.
4. Zorg ervoor dat **docker** is geselecteerd onder **implementatie scripts**.
5. Selecteer de bestands koppeling **env. conf** naast de **omgeving** om het bestand te downloaden.
6. Selecteer het pictogram **kopiëren** aan de rechter kant van het tekstvak **uitvoeren** om de docker-opdracht op het klem bord op te slaan.
7. Plak de opdracht in het venster Terminal (of Command). Pas de poort toewijzingen en de container naam waar nodig aan. Houd er rekening mee dat de opdracht verwacht dat het gedownloade omgevings bestand aanwezig is in de huidige map.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Voer de opdracht uit. De opdracht geeft uw docker-omgeving de instructie om de container uit te voeren, waarbij de door de host gehoste installatie kopie van de gateway wordt gedownload van het micro soft-Container Registry en de HTTP-poorten (8080) en HTTPS (8081) van de container worden toegewezen aan poorten 80 en 443 op de hosts.
9. Voer de onderstaande opdracht uit om te controleren of de gateway pod wordt uitgevoerd:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Ga terug naar Azure Portal en bevestig dat het gateway-knoop punt dat u zojuist hebt geïmplementeerd, de status in orde rapporteert.

![Gateway status](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Gebruik <code>console docker container logs <gateway-name></code> opdracht om een moment opname van het zelf-hostende gateway logboek weer te geven.
>
> Gebruik <code>docker container logs --help</code> opdracht om alle opties voor het weer geven van logboeken weer te geven.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure API Management zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)voor meer informatie over de zelf-hostende gateway.
* [Configureer de aangepaste domein naam voor de zelf-hostende gateway](api-management-howto-configure-custom-domain-gateway.md).
