---
title: Azure Service Fabric Mesh-toepassingsgeheimen opslaan en gebruiken | Microsoft Docs
description: Service Fabric Mesh biedt ondersteuning voor geheimen als Azure-resources. Hier vindt u informatie over het opslaan en beheren van geheimen met uw Service Fabric-mesh-toepassingen.
services: service-fabric-mesh
keywords: geheimen
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 72188517c237b170b709c48f16d3c131985f95d1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686232"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-toepassingsgeheimen
Service Fabric Mesh biedt ondersteuning voor geheimen als Azure-resources. Een Service Fabric Mesh-geheim kan bestaan uit gevoelige informatie in tekstvorm, zoals opslagverbindingsreeksen, wachtwoorden of andere waarden die veilig moeten worden opgeslagen en verzonden.

![Overzicht van Mesh-geheimen][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh-geheimenresources
Een Mesh-toepassingsgeheim bestaat uit:
* Een **Geheimen**-resource. Dit is een container die geheimen in tekstvorm bevat. Geheimen die zijn opgenomen in de **Geheimen**-resource, worden veilig opgeslagen en verzonden.
* Een of meer **Geheimen/waarden**-resources die zijn opgeslagen in de **Geheimen**-resourcecontainer. Elke **Geheimen/waarden**-resource wordt gekenmerkt door een uniek versienummer.

## <a name="next-steps"></a>Volgende stappen 
Voor meer informatie over Service Fabric Mesh-geheimen zie:
- [Service Fabric Mesh-toepassingsgeheimen beheren](service-fabric-mesh-howto-manage-secrets.md)
- [Inleiding tot het Service Fabric-resourcemodel](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
