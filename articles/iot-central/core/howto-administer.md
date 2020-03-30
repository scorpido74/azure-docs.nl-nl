---
title: Azure IoT Central-toepassingsinstellingen wijzigen | Microsoft Documenten
description: Als beheerder u uw Azure IoT Central-toepassing beheren door de naam van de toepassing, URL, uploadafbeelding en verwijderen van een toepassing te wijzigen
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158675"
---
# <a name="change-iot-central-application-settings"></a>Instellingen voor IoT Central-toepassingen wijzigen



In dit artikel wordt beschreven hoe u als beheerder de toepassing beheren door de naam en URL van de toepassing te wijzigen, afbeelding te uploaden en een toepassing in uw Azure IoT Central-toepassing te verwijderen.

Als u de sectie **Beheer** wilt openen en gebruiken, moet u zich in de **functie Administrator** bevinden voor een Azure IoT Central-toepassing. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegewezen aan de **administratorrol** voor die toepassing.

## <a name="change-application-name-and-url"></a>De naam en URL van de toepassing wijzigen

Op de pagina **Toepassingsinstellingen** u de naam en url van uw toepassing wijzigen en vervolgens **Opslaan selecteren.**

![Pagina Toepassingsinstellingen](media/howto-administer/image0-a.png)

Als uw beheerder een aangepast thema voor uw toepassing maakt, bevat deze pagina een optie om de **toepassingsnaam** in de gebruikersinterface te verbergen. Deze optie is handig als het toepassingslogo in het aangepaste thema de toepassingsnaam bevat. Zie [De Azure IoT Central UI aanpassen](./howto-customize-ui.md)voor meer informatie.

> [!Note]
> Als u uw URL wijzigt, kan uw oude URL worden overgenomen door een andere Azure IoT Central-klant. Als dat gebeurt, is het niet langer beschikbaar voor u om te gebruiken. Wanneer u uw URL wijzigt, werkt de oude URL niet meer en moet u uw gebruikers op de hoogte stellen van de nieuwe URL die u wilt gebruiken.

## <a name="delete-an-application"></a>Een toepassing verwijderen

Gebruik de knop **Verwijderen** om uw IoT Central-toepassing permanent te verwijderen. Met deze actie worden alle gegevens die aan de toepassing zijn gekoppeld, permanent verwijderd.

> [!Note]
> Als u een toepassing wilt verwijderen, moet u ook machtigingen hebben om resources te verwijderen in het Azure-abonnement dat u hebt gekozen toen u de toepassing maakte. Zie [Toegangsbeheer op basis van rollen gebruiken om toegang tot uw Azure-abonnementsbronnen te beheren](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)voor meer informatie.

## <a name="manage-programmatically"></a>Programmatisch beheren

IoT Central Azure Resource Manager SDK-pakketten zijn beschikbaar voor Node, Python, C#, Ruby, Java en Go. U deze pakketten gebruiken om IoT Central-toepassingen te maken, te vermelden, bij te werken of te verwijderen. De pakketten bevatten helpers voor het beheren van verificatie en foutafhandeling.

U voorbeelden vinden van het gebruik van de [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)Azure Resource Manager SDKs op.

Zie voor meer informatie de volgende GitHub-opslagplaatsen en -pakketten:

| Taal | Opslagplaats | Pakket |
| ---------| ---------- | ------- |
| Knooppunt | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Aan de slag | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Volgende stappen

Nu u meer weet over het beheren van uw Azure IoT Central-toepassing, is de voorgestelde volgende stap om meer te weten te komen over [Gebruikers en rollen beheren](howto-manage-users-roles.md) in Azure IoT Central.
