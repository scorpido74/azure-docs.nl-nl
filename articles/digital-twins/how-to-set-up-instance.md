---
title: Een Azure Digital Apparaatdubbels-exemplaar maken
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f097861f97e7ec62019fa631e2ac608d2e0c5024
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612871"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Een Azure Digital Apparaatdubbels-exemplaar instellen

Dit artikel begeleidt u stapsgewijs door de basis stappen voor het instellen van een nieuw Azure Digital Apparaatdubbels-exemplaar. Dit omvat het maken van het exemplaar en het toewijzen van [Azure Active Directory (Aad)-](../active-directory/fundamentals/active-directory-whatis.md) machtigingen aan het exemplaar voor uzelf.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Een Azure Digital Apparaatdubbels-exemplaar instellen

Voer vervolgens de volgende opdrachten uit om een nieuwe Azure-resource groep te maken voor gebruik in deze procedure en maak vervolgens een nieuw exemplaar van Azure Digital Apparaatdubbels in deze resource groep.

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

> [!TIP]
> Als u een lijst met Azure-regio namen wilt uitvoeren die kunnen worden door gegeven in opdrachten in de Azure CLI, voert u de volgende opdracht uit:
> ```azurecli
> az account list-locations -o table
> ```
> Als u wilt zien welke regio's Azure Digital Apparaatdubbels ondersteunen, gaat u naar [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

Het resultaat van deze opdrachten ziet er ongeveer als volgt uit, zoals het uitvoeren van informatie over de resources die u hebt gemaakt:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Opdrachtvenster met het maken van een resource groep en een Azure Digital Apparaatdubbels-exemplaar":::

Noteer de *hostnaam*, *naam*en *ResourceGroup* van het Azure Digital apparaatdubbels-exemplaar van de uitvoer. Dit zijn alle sleutel waarden die u mogelijk nodig hebt, zodat u met uw Azure Digital Apparaatdubbels-exemplaar kunt blijven werken om verificatie en gerelateerde Azure-resources in te stellen.

> [!TIP]
> U kunt deze eigenschappen, samen met alle eigenschappen van uw exemplaar, op elk gewenst moment weer geven door uit te voeren `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Azure Active Directory machtigingen toewijzen

Azure Digital Apparaatdubbels maakt gebruik van [Azure Active Directory (Aad)](../active-directory/fundamentals/active-directory-whatis.md) voor op rollen gebaseerd toegangs beheer (RBAC). Dit betekent dat voordat u gegevens vlak aanroepen naar uw Azure Digital Apparaatdubbels-exemplaar kunt maken, u eerst een rol met deze machtigingen moet toewijzen.

Als u Azure Digital Apparaatdubbels wilt gebruiken met een client toepassing, moet u er ook voor zorgen dat uw client-app kan worden geverifieerd tegen Azure Digital Apparaatdubbels. Dit doet u door een registratie van een Azure Active Directory-app (AAD) in te stellen, die u kunt lezen over [de instructies: een client toepassing verifiëren](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Uzelf een rol toewijzen

Maak een roltoewijzing voor uzelf met behulp van uw e-mail adres dat is gekoppeld aan de AAD-Tenant op uw Azure-abonnement. Zorg er eerst voor dat u bent geclassificeerd als een eigenaar van uw Azure-abonnement. Vervolgens kunt u de volgende opdracht gebruiken om uw gebruiker toe te wijzen aan een rol van eigenaar voor uw Azure Digital Apparaatdubbels-exemplaar:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Het resultaat van deze opdracht is gegenereerde informatie over de roltoewijzing die u hebt gemaakt.

> [!TIP]
> Als u in plaats daarvan een *400: onjuiste aanvraag* -fout krijgt, voert u de volgende opdracht uit om de *ObjectID* voor uw gebruiker op te halen:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Herhaal vervolgens de opdracht roltoewijzing met de *object-id* van uw gebruiker in plaats van uw e-mail adres.

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan.

## <a name="next-steps"></a>Volgende stappen

Zie een client-app instellen en verifiëren voor het werken met uw exemplaar:
* [Instructies: een client toepassing verifiëren](how-to-authenticate-client.md)
