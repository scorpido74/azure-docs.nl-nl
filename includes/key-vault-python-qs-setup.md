---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482119"
---
1. Controleer of u beschikt over een [Azure-account met een actief abonnement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Installeer [Python 2.7+ or 3.5.3+](https://www.python.org/downloads).

1. Installeer de [Azure CLI](/cli/azure/install-azure-cli).

1. Volg de instructies in [Verificatie configureren voor lokale ontwikkeling](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication), waarmee u een lokale service-principal aanmaakt en deze beschikbaar maakt voor de Azure Key Vault-client voor Python via omgevingsvariabelen. 

    Wanneer code rechtstreeks op Azure wordt uitgevoerd, is er geen afzonderlijke service-principal nodig als de app beheerde identiteit gebruikt.

1. In een terminal- of opdrachtprompt maakt u een geschikte projectmap en maakt en activeert u een virtuele Python-omgeving, zoals wordt beschreven in [Virtuele Python-omgevingen gebruiken](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Installeer Azure Active Directory Identity-bibliotheek:

    ```terminal
    pip install azure.identity
    ```
