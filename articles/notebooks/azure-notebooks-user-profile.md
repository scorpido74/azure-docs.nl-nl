---
title: Gebruikersprofiel en -ID voor gebruik met Azure-laptops
description: Over het maken en beheren van uw gebruikersprofiel en gebruikers-ID met Azure-Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 1fddefeb2a54ae775a9016799ffff1963eab247e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970148"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Uw profiel en gebruikers-ID voor Azure-laptops

In de ruimte die krachtige, samenwerking van Azure-notitieblokken wordt uw gebruikersprofiel anderen uw openbare installatiekopie:

[@no__t-Azure Notebooks profiel pagina 1An](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Uw gebruikers-ID is onderdeel van de URL's die u gebruiken voor het delen van projecten en notitieblokken. De volgende lijst beschrijft de verschillende URL-patronen:

- `https://notebooks.azure.com/<user_id>`: De profiel pagina.
- `https://notebooks.azure.com/<user_id>/projects`: Uw projecten. U ziet alle projecten. andere gebruikers zien alleen uw openbare projecten.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Project bestanden.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klonen van een specifieke projecten.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: De HTML-preview van een specifiek notitie blok of bestand.

## <a name="your-user-id"></a>Uw gebruikers-ID

Wanneer voor de eerste keer aanmeldt bij Azure-laptops, wordt een tijdelijke gebruikers-ID, zoals "anon-idr3ca" automatisch toegewezen door uw account. Als u een gebruikers-ID die begint hebt met "anon-', Azure-notitieblokken vraagt u om dit te wijzigen wanneer u zich aanmeldt:

![Prompt voor het maken van een gebruikers-ID bij het aanmelden bij Azure-laptops](media/accounts/create-user-id.png)

Een **gebruikers-ID configureren** opdracht wordt ook weergegeven naast de naam van de tijdelijke gebruiker:

![Gebruikers-ID-opdracht die wordt weergegeven wanneer u een tijdelijke-ID configureren](media/accounts/configure-user-id-command.png)

U kunt ook uw gebruikers-ID op elk gewenst moment wijzigen op uw profielpagina.

Een gebruikers-ID moet bestaan uit vier tot zestien letters, cijfers en afbreek streepjes. Er zijn geen andere tekens zijn toegestaan en de gebruikers-ID mag niet beginnen of eindigen met een afbreekstreepje of gebruikmaken van meerdere opeenvolgende afbreekstreepjes in een rij. Omdat gebruikers-Id's uniek zijn voor alle Azure Notebooks accounts, ziet u mogelijk het bericht ' de gebruikers-ID is al in gebruik '. (Het bericht wordt ook weer gegeven als u een micro soft-handels merk wilt gebruiken als een gebruikers-ID). In deze gevallen kiest u een andere gebruikers-ID.

> [!Important]
> Bijgewerkt met de ID wordt ongeldig alle URL's die u mogelijk hebt gedeeld met uw vorige ID. Terug naar de vorige-ID voor het valideren van de koppelingen kunt u uw ID. Het is echter mogelijk dat een andere gebruiker voor een niet-gebruikte claim in de tussentijd de ID.

## <a name="your-profile"></a>Uw profiel

Uw profiel bestaat uit het publiek zichtbare gegevens in de URL, `https://notebooks.azure.com/<user_id>`. Uw profielpagina ziet ook uw recent gebruikte projecten en projecten met een ster.

Als u wilt uw profiel bewerkt, gebruikt u de **profielgegevens bewerken** opdracht op uw profielpagina. De secties van het profiel zijn als volgt:

| Sectie | Inhoud |
| --- | --- |
| Profielfoto | Een afbeelding die wordt weergegeven op uw profielpagina. |
| Accountgegevens | Uw weergavenaam, gebruikers-ID en openbare e-mailaccount. Het e-mailaccount biedt andere gebruikers in een gemiddelde contact met u opnemen en kan afwijken van de [account](azure-notebooks-user-account.md) kunt u zich aanmelden bij Azure-notitieblokken zelf. |
| Profielgegevens | Uw locatie, bedrijf, functietitel, website en een korte beschrijving van uzelf. |
| Sociale-profielen | Uw GItHub, Twitter en Facebook-ID's, als u wilt delen. |
| Privacyinstellingen | Biedt twee opdrachten uit:<ul><li>**Mijn profiel exporteren**: gemaakt en gedownload een *.zip* -bestand met de informatie die Azure-notitieblokken in uw profiel opslaat, zoals uw foto's, profielgegevens en -Logboeken.</li><li>**Mijn account verwijderen**: Hiermee worden al uw persoonlijke gegevens die zijn opgeslagen in Azure Notebooks permanent verwijderd.</li></ul> |
| Sitefuncties inschakelen | Kunt u aspecten van het gedrag van Azure-notitieblokken te bepalen:<ul><li>**Unified Frontend voor laptops**: kunt u sneller opstarten van de notebook en betere persistentie.</li><li>**Voer in jjupyterlab standaard**de volgende handelingen uit: Azure Notebooks biedt standaard een eenvoudige gebruikers interface die geschikt is voor de meeste gebruikers. Jjupyterlab biedt een uitgebreidere maar meer gecompliceerde interface voor ervaren gebruikers.</li><li>**VNext Website**: Hiermee kunt u de gemoderniseerd web-indeling die wordt weergegeven in deze documentatie.</li></ul> |

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Zelfstudie: een run maken een Jupyter-notebook te doen, lineaire regressie](tutorial-create-run-jupyter-notebook.md)
