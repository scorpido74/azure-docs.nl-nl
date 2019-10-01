---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/01/2019
ms.author: marsma
ms.openlocfilehash: 67a0832f868fecd47983aa8cddff9cdf139b3f2a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702163"
---
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure Active Directory** (*niet* Azure AD B2C) in het menu links. U kunt ook **alle services** selecteren en vervolgens zoeken naar en **Azure Active Directory**selecteren.
1. Selecteer onder **beheren**de optie **app-registraties (verouderd)** .
1. Selecteer **Nieuwe toepassing registreren**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *managementapp1*.
1. Selecteer **Web-app/API**voor het **toepassings type**.
1. Voer een geldige URL in voor de **aanmeldings-URL**. Bijvoorbeeld `https://localhost`. Het eind punt hoeft niet bereikbaar te zijn, maar moet een geldige URL zijn.
1. Selecteer **Maken**.
1. Noteer de **toepassings-id** die wordt weer gegeven op de overzichts pagina van de **geregistreerde app** . U gebruikt deze waarde in een latere stap.
