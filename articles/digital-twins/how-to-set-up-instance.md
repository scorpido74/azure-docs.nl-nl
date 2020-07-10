---
title: Een Azure Digital Twins-exemplaar maken
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fecacbd2c7c6549a1321367157bb179321779ca9
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206513"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Een Azure Digital Twins-instantie instellen

Dit artikel begeleidt u stapsgewijs door de basis stappen voor het instellen van een nieuw Azure Digital Apparaatdubbels-exemplaar. Dit omvat het maken van het exemplaar en het toewijzen van [Azure Active Directory (Aad)-](../active-directory/fundamentals/active-directory-whatis.md) machtigingen aan het exemplaar voor uzelf.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Een Azure Digital Twins-instantie instellen

Vervolgens maakt u een nieuwe Azure-resource groep voor gebruik in deze procedure. Vervolgens kunt u **een nieuw exemplaar van Azure Digital apparaatdubbels maken** binnen die resource groep. 

U moet ook een naam opgeven voor uw exemplaar en een regio voor de implementatie kiezen. Als u wilt zien welke regio's Azure Digital Apparaatdubbels ondersteunen, gaat u naar [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

>[!NOTE]
> De naam van het nieuwe exemplaar moet uniek zijn binnen de regio (wat betekent dat als een ander Azure Digital Apparaatdubbels-exemplaar in die regio al gebruikmaakt van de naam die u kiest, moet u een andere naam kiezen).

Maak de resource groep en het exemplaar met de volgende opdrachten:

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

Het resultaat van deze opdrachten ziet er ongeveer als volgt uit, zoals het uitvoeren van informatie over de resources die u hebt gemaakt:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Opdrachtvenster met het maken van een resource groep en een Azure Digital Apparaatdubbels-exemplaar":::

Noteer de *hostnaam*, *naam*en *ResourceGroup* van het Azure Digital apparaatdubbels-exemplaar van de uitvoer. Dit zijn alle sleutel waarden die u mogelijk nodig hebt, zodat u met uw Azure Digital Apparaatdubbels-exemplaar kunt blijven werken om verificatie en gerelateerde Azure-resources in te stellen.

> [!TIP]
> U kunt deze eigenschappen, samen met alle eigenschappen van uw exemplaar, op elk gewenst moment weer geven door uit te voeren `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Azure Active Directory machtigingen toewijzen

Azure Digital Apparaatdubbels maakt gebruik van [Azure Active Directory (Aad)](../active-directory/fundamentals/active-directory-whatis.md) voor op rollen gebaseerd toegangs beheer (RBAC). Dit betekent dat voordat u gegevens vlak aanroepen naar uw Azure Digital Apparaatdubbels-exemplaar kunt maken, u eerst een rol met deze machtigingen moet toewijzen.

Als u Azure Digital Apparaatdubbels wilt gebruiken met een client toepassing, moet u er ook voor zorgen dat uw client-app kan worden geverifieerd tegen Azure Digital Apparaatdubbels. Dit doet u door een registratie van een Azure Active Directory-app (AAD) in te stellen, die u kunt lezen over [de instructies: een client toepassing verifiëren](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Uzelf een rol toewijzen

Maak een roltoewijzing voor uzelf in het Azure Digital Apparaatdubbels-exemplaar met behulp van uw e-mail adres dat is gekoppeld aan de AAD-Tenant op uw Azure-abonnement. 

Om dit te kunnen doen, moet u worden geclassificeerd als een eigenaar van uw Azure-abonnement. U kunt dit controleren door de opdracht uit te voeren `az role assignment list --assignee <your-Azure-email>` en te controleren in de uitvoer waarvan de *roleDefinitionName* -waarde *eigenaar*is. Als u merkt dat de waarde *Inzender* is of iets anders is dan de *eigenaar*, neemt u contact op met uw abonnements beheerder met de bevoegdheid om machtigingen te verlenen in uw abonnement. Ze kunnen uw rol op het hele abonnement verhogen zodat u de volgende opdracht kunt uitvoeren, of een eigenaar kan namens u de volgende opdracht uitvoeren om uw Azure Digital Apparaatdubbels-machtigingen voor u in te stellen.

Als u de machtigingen van uw gebruiker "eigenaar" wilt toewijzen in uw Azure Digital Apparaatdubbels-exemplaar, gebruikt u de volgende opdracht (moet worden uitgevoerd door een eigenaar van het Azure-abonnement):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Het resultaat van deze opdracht is een gegenereerde informatie over de roltoewijzing die is gemaakt.

> [!TIP]
> Als u in plaats daarvan een *400: onjuiste aanvraag* -fout krijgt, voert u de volgende opdracht uit om de *ObjectID* voor uw gebruiker op te halen:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Herhaal vervolgens de opdracht roltoewijzing met de *object-id* van uw gebruiker in plaats van uw e-mail adres.

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan en machtigingen te beheren.

## <a name="next-steps"></a>Volgende stappen

Zie een client-app instellen en verifiëren voor het werken met uw exemplaar:
* [Instructies: een client toepassing verifiëren](how-to-authenticate-client.md)
