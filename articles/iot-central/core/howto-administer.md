---
title: Uw Azure IoT Central-toepassing beheren | Microsoft Docs
description: Als beheerder kunt u uw Azure IoT Central-toepassing beheren door de toepassings naam, URL, afbeelding uploaden, kopiëren en verwijderen van een toepassing te wijzigen
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4c729d2502464df021df4d03e989d489b2d1ecea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954589"
---
# <a name="manage-your-iot-central-application"></a>Uw IoT Central-toepassing beheren

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u, als beheerder, de toepassing kunt beheren door de naam en URL van de toepassing te wijzigen, een installatie kopie te uploaden, ook kunt u leren hoe u een toepassing kopieert en verwijdert in uw Azure IoT Central-toepassing.

Voor toegang tot en gebruik van de sectie **beheer** moet u de rol **beheerder** hebben voor een Azure IOT Central-toepassing. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegewezen aan **de beheerdersrol** voor die toepassing. 

## <a name="change-application-name-and-url"></a>Toepassings naam en-URL wijzigen

Op de pagina **Toepassings instellingen** kunt u de naam en URL van uw toepassing wijzigen en vervolgens **Opslaan**selecteren.

![Pagina Toepassings instellingen](media/howto-administer/image0-a.png)

Als uw beheerder een aangepast thema voor uw toepassing maakt, bevat deze pagina een optie om de **toepassings naam** in de gebruikers interface te verbergen. Dit is handig als het toepassings logo in het aangepaste thema de naam van de toepassing bevat. Zie [de Azure IOT Central-gebruikers interface aanpassen](./howto-customize-ui.md)voor meer informatie.

> [!Note]
> Als u uw URL wijzigt, kan uw oude URL worden overgenomen door een andere Azure IoT Central-klant. Als dat het geval is, kan het niet meer worden gebruikt. Wanneer u uw URL wijzigt, werkt de oude URL niet meer en moet u uw gebruikers op de hoogte stellen van de nieuwe URL die u wilt gebruiken.

## <a name="prepare-and-upload-image"></a>Installatiekopie voorbereiden en uploaden

Zie [installatie kopieën voorbereiden en uploaden naar uw Azure IOT Central-toepassing](howto-prepare-images.md)om de installatie kopie van de toepassing te wijzigen.

## <a name="copy-an-application"></a>Een toepassing kopiëren

U kunt een kopie maken van elke toepassing, min eventuele exemplaren van apparaten, geschiedenis van apparaatgegevens en gebruikers gegevens. De kopie is een betalen per gebruik-toepassing waarvoor u kosten in rekening brengt. U kunt op deze manier geen proef toepassing maken.

Selecteer **kopiëren**. Voer in het dialoog venster de details in voor de nieuwe toepassing voor betalen naar gebruik. Selecteer vervolgens **kopiëren** om te bevestigen dat u wilt door gaan. Meer informatie over de velden in dit formulier vindt u in Snelstartgids voor het [maken van een toepassing](quick-deploy-iot-central.md) .

![Pagina Toepassings instellingen](media/howto-administer/appcopy2.png)

Nadat de Kopieer bewerking van de app is voltooid, kunt u naar de nieuwe toepassing navigeren met behulp van de koppeling.

![Pagina Toepassings instellingen](media/howto-administer/appcopy3a.png)

Als u een toepassing kopieert, wordt ook de definitie van regels en e-mail acties gekopieerd. Sommige acties zoals flow, Logic Apps enz. zijn gekoppeld aan specifieke regels via de regel-ID. Wanneer een regel naar een andere toepassing wordt gekopieerd, krijgt deze een eigen regel-ID. In dit geval moeten gebruikers een nieuwe actie maken en vervolgens de nieuwe regel koppelen. Over het algemeen is het een goed idee om de regels en acties te controleren om ervoor te zorgen dat ze up-to-date zijn in de nieuwe app.

> [!WARNING]
> Als een dash board tegels bevat waarin informatie over specifieke apparaten wordt weer gegeven, worden in die tegels **de aangevraagde bron niet** in de nieuwe toepassing gevonden. U moet deze tegels opnieuw configureren om informatie weer te geven over apparaten in de nieuwe toepassing.

## <a name="delete-an-application"></a>Een toepassing verwijderen

Gebruik de knop **verwijderen** om uw IOT Central-toepassing permanent te verwijderen. Met deze actie worden alle gegevens die aan de toepassing zijn gekoppeld, definitief verwijderd.

> [!Note]
> Als u een toepassing wilt verwijderen, moet u ook machtigingen hebben om resources te verwijderen in het Azure-abonnement dat u hebt gekozen tijdens het maken van de toepassing. Zie op [rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot uw Azure-abonnements resources](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)voor meer informatie.


## <a name="manage-programatically"></a>Programmatisch beheren

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
 
Nu u hebt geleerd hoe u uw Azure IoT Central-toepassing beheert, is de voorgestelde volgende stap meer informatie over het [beheren van gebruikers en rollen](howto-manage-users-roles.md) in azure IOT Central.