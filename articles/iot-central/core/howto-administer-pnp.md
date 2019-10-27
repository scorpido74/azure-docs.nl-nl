---
title: Instellingen van Azure IoT Central-toepassing wijzigen | Microsoft Docs
description: Als beheerder kunt u uw Azure IoT Central-toepassing beheren door de toepassings naam, URL, afbeelding uploaden en een toepassing te verwijderen
author: viv-liu
ms.author: viviali
ms.date: 10/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7396b7dc22f0be77b9fc62a4dea379cf03a6befd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951807"
---
# <a name="change-iot-central-application-settings"></a>IoT Central toepassings instellingen wijzigen

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In dit artikel wordt beschreven hoe u, als beheerder, de toepassing kunt beheren door de naam en URL van de toepassing te wijzigen, een installatie kopie te uploaden en een toepassing te verwijderen in uw Azure IoT Central-toepassing.

Voor toegang tot en gebruik van de sectie **beheer** moet u de rol **beheerder** hebben voor een Azure IOT Central-toepassing. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegewezen aan **de beheerdersrol** voor die toepassing.

## <a name="change-application-name-and-url"></a>Toepassings naam en-URL wijzigen

Op de pagina **Toepassings instellingen** kunt u de naam en URL van uw toepassing wijzigen en vervolgens **Opslaan**selecteren.

![Pagina Toepassings instellingen](media/howto-administer-pnp/image0-a.png)

Als uw beheerder een aangepast thema voor uw toepassing maakt, bevat deze pagina een optie om de **toepassings naam** in de gebruikers interface te verbergen. Deze optie is handig als het toepassings logo in het aangepaste thema de naam van de toepassing bevat. Zie [de Azure IOT Central-gebruikers interface aanpassen](./howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)voor meer informatie.

> [!Note]
> Als u uw URL wijzigt, kan uw oude URL worden overgenomen door een andere Azure IoT Central-klant. Als dat het geval is, kan het niet meer worden gebruikt. Wanneer u uw URL wijzigt, werkt de oude URL niet meer en moet u uw gebruikers op de hoogte stellen van de nieuwe URL die u wilt gebruiken.

## <a name="delete-an-application"></a>Een toepassing verwijderen

Gebruik de knop **verwijderen** om uw IOT Central-toepassing permanent te verwijderen. Met deze actie worden alle gegevens die aan de toepassing zijn gekoppeld, definitief verwijderd.

> [!Note]
> Als u een toepassing wilt verwijderen, moet u ook machtigingen hebben om resources te verwijderen in het Azure-abonnement dat u hebt gekozen tijdens het maken van de toepassing. Zie op [rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot uw Azure-abonnements resources](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)voor meer informatie.

## <a name="manage-programmatically"></a>Programmatisch beheren

IoT Central Azure Resource Manager SDK-pakketten zijn beschikbaar voor knoop punt, C#python,, Ruby, Java en go. U kunt deze pakketten gebruiken om IoT Central-toepassingen te maken, weer te geven, bij te werken of te verwijderen. De pakketten bevatten helpers voor het beheren van verificatie en het afhandelen van fouten.

In [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)vindt u voor beelden van hoe u de Azure Resource Manager sdk's kunt gebruiken.

Voor meer informatie raadpleegt u de volgende GitHub-opslag plaatsen en-pakketten:

| Taal | Opslag plaats | Pakket |
| ---------| ---------- | ------- |
| Knooppunt | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw Azure IoT Central-toepassing beheert, is de voorgestelde volgende stap meer informatie over het [beheren van gebruikers en rollen](howto-manage-users-roles-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in azure IOT Central.
