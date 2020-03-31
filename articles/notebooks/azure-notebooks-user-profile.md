---
title: Gebruikersprofiel en id voor gebruik met Azure Notebooks Preview
description: Uw gebruikersprofiel en gebruikers-id maken en beheren met Azure-notitieblokken, die onderdeel worden van de URL van gedeelde notitieblokken.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646276"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Uw profiel en gebruikersnaam voor De Voorvertoning van Azure Notebooks

Binnen de krachtige, collaboratieve ruimte van Azure Notebooks presenteert uw gebruikersprofiel uw openbare afbeelding aan anderen:

[![Een profielpagina van Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Uw gebruikersnaam maakt deel uit van de URL's die u gebruikt om projecten en notitieblokken te delen. In de volgende lijst worden de verschillende URL-patronen beschreven:

- `https://notebooks.azure.com/<user_id>`: Uw profielpagina.
- `https://notebooks.azure.com/<user_id>/projects`: Uw projecten. Je ziet alle projecten; andere gebruikers zien alleen uw openbare projecten.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Projectbestanden.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klonen van een specifiek project.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: De HTML-voorbeeld van een specifiek notitieblok of bestand.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>Uw gebruikers-id

Wanneer u zich voor de eerste keer aanmeldt bij Azure Notebooks, krijgt uw account automatisch een tijdelijke gebruikers-id toegewezen, zoals 'anon-idr3ca'. Zolang u een gebruikersnaam hebt die begint met 'anon', vraagt Azure Notebooks u deze te wijzigen wanneer u zich aanmeldt:

![Vragen om een gebruikers-id te maken wanneer u zich aanmeldt bij Azure-notitieblokken](media/accounts/create-user-id.png)

Een opdracht **Gebruikersnaam configureren** wordt ook naast de tijdelijke gebruikersnaam weergegeven:

![De opdracht Gebruikersnaam configureren die wordt weergegeven wanneer u een tijdelijke id gebruikt](media/accounts/configure-user-id-command.png)

U uw gebruikersnaam ook op elk gewenst moment wijzigen op uw profielpagina.

Een gebruikersnaam moet bestaan uit vier tot zestien letters, cijfers en koppeltekens. Er zijn geen andere tekens toegestaan en de gebruikersnaam kan niet beginnen of eindigen met een koppelteken of meerdere koppeltekens op een rij gebruiken. Omdat gebruikers-id's uniek zijn voor alle Azure Notebooks-accounts, ziet u mogelijk het bericht 'Gebruikersnaam is al in gebruik'. (Het bericht wordt ook weergegeven als u een Microsoft-handelsmerk als gebruikersnaam probeert te gebruiken.) Kies in deze gevallen een andere gebruikersnaam.

> [!Important]
> Als u uw id wijzigt, worden alle URL's die u mogelijk hebt gedeeld met uw vorige id ongeldig. U uw id weer wijzigen naar uw vorige id om de koppelingen opnieuw te valideren. Het is echter mogelijk voor een andere gebruiker om een ongebruikte id te claimen in de tussentijd.

## <a name="your-profile"></a>Je profiel

Uw profiel bestaat uit openbaar te bekijken `https://notebooks.azure.com/<user_id>`informatie op de URL, . Uw profielpagina toont ook uw recent gebruikte projecten en projecten met een ster.

Als u uw profiel wilt bewerken, gebruikt u de opdracht **Profielgegevens bewerken** op uw profielpagina. De secties van uw profiel zijn als volgt:

| Sectie | Inhoud |
| --- | --- |
| Profielfoto | Een afbeelding die wordt weergegeven op je profielpagina. |
| Accountgegevens | Uw weergavenaam, gebruikersnaam en openbaar e-mailaccount. Het e-mailaccount biedt andere gebruikers een gemiddelde om contact met u op te nemen en kan afwijken van het [account](azure-notebooks-user-account.md) dat u gebruikt om u aan te melden bij Azure Notebooks zelf. |
| Profielinformatie | Uw locatie, bedrijf, functietitel, website, en een korte beschrijving van jezelf. |
| Sociale profielen | Je GItHub-, Twitter- en Facebook-id's, als je ze wilt delen. |
| Privacyinstellingen | Biedt twee opdrachten:<ul><li>**Mijn profiel exporteren:** maakt en downloadt een *.zip-bestand* met alle informatie die Azure Notebooks opslaat in uw profiel, inclusief uw foto, profielgegevens en beveiligingslogboeken.</li><li>**Mijn account verwijderen:** verwijdert permanent al uw persoonlijke gegevens die zijn opgeslagen in Azure-notitieblokken.</li></ul> |
| Sitefuncties inschakelen | Hiermee u aspecten van het gedrag van Azure-notitieblokken beheren:<ul><li>**Unified Frontend voor notebooks:** maakt een snellere notebook opstarten en een betere persistentie.</li><li>**Standaard worden uitgevoerd in JupyterLab:** Azure Notebooks bieden standaard een eenvoudige gebruikersinterface die geschikt is voor de meeste gebruikers. JupyterLab biedt een rijkere maar ingewikkeldere interface voor ervaren gebruikers.</li><li>**VNext Website**: maakt de gemoderniseerde weblay-out in deze documentatie mogelijk.</li></ul> |

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Zelfstudie: maak een run een Jupyter-notitieblok om lineaire regressie uit te voeren](tutorial-create-run-jupyter-notebook.md)
