---
title: Uw Azure Red Hat OpenShift-ontwikkelomgeving instellen
description: Hier volgen de voorwaarden voor het werken met Microsoft Azure Red Hat OpenShift.
keywords: red hat openshift setup setup setup set-up
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477031"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Een Azure Red Hat OpenShift-ontwikkelaarsomgeving instellen

Als u Microsoft Azure Red Hat OpenShift-toepassingen wilt bouwen en uitvoeren, moet u het als:

* Installeer versie 2.0.65 (of hoger) van azure CLI (of gebruik de Azure Cloud Shell).
* Registreer u `AROGA` voor de functie en bijbehorende resourceproviders.
* Maak een Azure Active Directory-tenant (Azure AD) .
* Maak een Azure AD-toepassingsobject.
* Een Azure AD-gebruiker maken.

De volgende instructies zullen u door al deze voorwaarden heen lopen.

## <a name="install-the-azure-cli"></a>Azure-CLI installeren

Azure Red Hat OpenShift vereist versie 2.0.65 of hoger van de Azure CLI. Als u de Azure CLI al hebt geïnstalleerd, u controleren welke versie u hebt door het uitvoeren van:

```azurecli
az --version
```

De eerste uitgangsregel heeft bijvoorbeeld `azure-cli (2.0.65)`de CLI-versie.

Hier volgen instructies voor [het installeren van de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u een nieuwe installatie of een upgrade nodig hebt.

U ook de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)gebruiken. Wanneer u de Azure Cloud Shell gebruikt, moet u de **Bash-omgeving** selecteren als u van plan bent om samen met de reeks [Zelfstudievan Azure Red Hat OpenShift-cluster](tutorial-create-cluster.md) te volgen en te beheren.

## <a name="register-providers-and-features"></a>Providers en functies registreren

De `Microsoft.ContainerService AROGA` `Microsoft.Solutions`functie, `Microsoft.Compute` `Microsoft.Storage`, `Microsoft.KeyVault` `Microsoft.Network` , en providers moeten handmatig worden geregistreerd op uw abonnement voordat u uw eerste Azure Red Hat OpenShift-cluster implementeert.

Als u deze providers en functies handmatig wilt registreren, gebruikt u de volgende instructies van een Bash-shell als u de CLI-sessie hebt geïnstalleerd of vanuit de Azure Cloud Shell-sessie (Bash) in uw Azure-portal:

1. Als u meerdere Azure-abonnementen hebt, geeft u de relevante abonnements-id op:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registreer de Microsoft.ContainerService AROGA-functie:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registreer de Microsoft.Storage-provider:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registreer de Microsoft.Compute-provider:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Registreer de Microsoft.Solutions-provider:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registreer de Microsoft.Network-provider:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registreer de Microsoft.KeyVault-provider:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Vernieuw de registratie van de Microsoft.ContainerService-bronprovider:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Een Azure Active Directory-tenant (Azure AD) maken

De Azure Red Hat OpenShift-service vereist een gekoppelde Azure Active Directory-tenant (Azure AD) die uw organisatie en de relatie met Microsoft vertegenwoordigt. Met uw Azure AD-tenant u apps registreren, bouwen en beheren en andere Azure-services gebruiken.

Als u geen Azure AD hebt om te gebruiken als tenant voor uw Azure Red Hat OpenShift-cluster of als u een tenant wilt maken voor tests, volgt u de instructies in [Een Azure AD-tenant maken voor uw Azure Red Hat OpenShift-cluster](howto-create-tenant.md) voordat u verdergaat met deze handleiding.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Een Azure AD-gebruiker, beveiligingsgroep en toepassingsobject maken

Azure Red Hat OpenShift vereist machtigingen voor het uitvoeren van taken op uw cluster, zoals het configureren van opslag. Deze machtigingen worden weergegeven via een [serviceprincipal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). U wilt ook een nieuwe Active Directory-gebruiker maken voor het testen van apps die worden uitgevoerd op uw Azure Red Hat OpenShift-cluster.

Volg de instructies in [Een Azure AD-appobject maken en de gebruiker](howto-aad-app-configuration.md) om een serviceprincipal te maken, een url voor clientgeheim en verificatie-terugroep-URL voor uw app te genereren en een nieuwe Azure AD-beveiligingsgroep en -gebruiker te maken om toegang te krijgen tot het cluster.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om Azure Red Hat OpenShift te gebruiken!

Probeer de zelfstudie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
