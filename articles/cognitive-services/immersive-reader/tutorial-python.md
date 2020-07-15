---
title: 'Zelfstudie: De Insluitende lezer starten met behulp van Python'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie wordt beschreven hoe u een Python-toepassing maakt waarmee de Insluitende lezer wordt gestart.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.custom: tracking-python
ms.openlocfilehash: ea9e6e1f06bacd28a4e9894039de824c3a5b4af4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044937"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Zelfstudie: De Insluitende lezer starten met behulp van het Python-voorbeeldproject

In het [overzicht](./overview.md) hebt u geleerd wat de Insluitende lezer is en hoe het bewezen technieken implementeert om begrijpend lezen te verbeteren voor taalverwervers, beginnende lezers en studenten met leerproblemen. In deze zelfstudie wordt beschreven hoe u een Python-webtoepassing maakt waarmee de Insluitende lezer wordt gestart. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Python-web-app maken met Pip, Flask, Jinja en virtualenv met behulp van een voorbeeldproject
> * Een toegangstoken verkrijgen
> * De Insluitende lezer starten met voorbeeldinhoud

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een resource voor insluitende lezers die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om deze in te stellen. U hebt enkele waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [Git](https://git-scm.com/)
* [SDK voor insluitende lezers](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) en [pip](https://docs.python.org/3/installing/index.html). Vanaf Python 3.4 is pip standaard toegevoegd aan de binaire installatieprogramma's van Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) en [virtualenvwrapper-win voor Windows](https://pypi.org/project/virtualenvwrapper-win/) of [virtualenvwrapper voor OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [request-module](https://pypi.org/project/requests/2.7.0/)
* Een code-editor, zoals [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Verificatiereferenties configureren

Maak een nieuw bestand met de naam _.env_ en plak de volgende code hierin, waarbij u de waarden opgeeft die zijn verstrekt bij het maken van uw Insluitende lezer-resource.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Zorg ervoor dat u dit bestand niet doorvoert in broncodebeheer, omdat het geheimen bevat die niet openbaar moeten worden gemaakt.

Het API-eindpunt **getimmersivereadertoken** moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth-](https://oauth.net/2/)) om te voorkomen dat niet-geautoriseerde gebruikers tokens verkrijgen die kunnen worden gebruikt voor uw Insluitende lezer-service en -facturering. Dit valt buiten het bereik van deze zelfstudie.

## <a name="create-a-python-web-app-on-windows"></a>Een Python-web-app maken in Windows

Een Python-web-app maken met behulp van `flask` in Windows.

Installeer [Git](https://git-scm.com/).

Nadat Git is geïnstalleerd, opent u een opdrachtprompt en 'kloont' u de Git-opslagplaats van de SDK van de Insluitende lezer naar een map op uw computer

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installeer [Python](https://www.python.org/downloads/).

Schakel het selectievakje Add Python to PATH in.

![Dialoogvenster Python voor Windows installeren - stap 1](./media/pythoninstallone.jpg)

Voeg optionele functies toe door de selectievakjes in te schakelen en klik op de knop 'Next'.

![Dialoogvenster Python voor Windows installeren - stap 2](./media/pythoninstalltwo.jpg)

Kies 'Custom installation' en stel het installatiepad in als uw hoofdmap, bijvoorbeeld `C:\Python37-32\`. Klik vervolgens op de knop 'Install'.

![Dialoogvenster Python voor Windows installeren - stap 3](./media/pythoninstallthree.jpg)

Nadat de installatie van Python is voltooid, opent u een opdrachtprompt en `cd` naar de map met Python-scripts.

```cmd
cd C:\Python37-32\Scripts
```

Installeer Flask.

```cmd
pip install flask
```

Installeer Jinja2. Een sjabloonengine met volledige functionaliteit voor Python.

```cmd
pip install jinja2
```

Installeer virtualenv. Een hulpprogramma om geïsoleerde Python-omgevingen te maken.

```cmd
pip install virtualenv
```

Installeer virtualenvwrapper-win. Het idee achter virtualenvwrapper is om het gebruik van virtualenv te vereenvoudigen.

```cmd
pip install virtualenvwrapper-win
```

Installeer de requests-module. Requests is een voor Apache2 gelicentieerde HTTP-bibliotheek, geschreven in Python.

```cmd
pip install requests
```

Installeer de python-dotenv-module. Deze module leest het sleutel-waardepaar van het .env-bestand en voegt dit toe aan de omgevingsvariabele.

```cmd
pip install python-dotenv
```

Een virtuele omgeving maken

```cmd
mkvirtualenv advanced-python
```

`cd` naar de hoofdmap van het voorbeeldproject.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Verbind het voorbeeldproject met de omgeving. Hiermee wordt de zojuist gemaakte virtuele omgeving toegewezen aan de basismap van het voorbeeldproject.

```cmd
setprojectdir .
```

Activeer de virtuele omgeving.

```cmd
activate
```

Het project moet nu actief zijn en u ziet iets als `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` in de opdrachtprompt.

Deactiveer de omgeving.

```cmd
deactivate
```

Het voorvoegsel `(advanced-python)` moet nu zijn verdwenen omdat de omgeving nu is gedeactiveerd.

Voer `workon advanced-python` uit vanuit de hoofdmap van het voorbeeldproject om de omgeving opnieuw te activeren.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>De Insluitende lezer starten met voorbeeldinhoud

Wanneer de omgeving actief is, voert u het voorbeeldproject uit door `flask run` vanuit de hoofdmap van het voorbeeldproject in te voeren.

```cmd
flask run
```

Open uw browser en ga naar _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Een Python-web-app maken in OSX

Maak een Python-web-app met behulp van `flask` in OSX.

Installeer [Git](https://git-scm.com/).

Nadat Git is geïnstalleerd, opent u Terminal en 'kloont' u de Git-opslagplaats van de SDK van de Insluitende lezer naar een map op uw computer

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installeer [Python](https://www.python.org/downloads/).

De Python-hoofdmap, bijvoorbeeld `Python37-32`, moet zich nu in de map Toepassingen bevinden.

Nadat de installatie van Python is voltooid, opent u Terminal en `cd` naar de map met Python-scripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Pip installeren.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Voer vervolgens de volgende handelingen uit om pip te installeren voor de gebruiker die momenteel is aangemeld om machtigingsproblemen te voorkomen.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Als dit wordt gevraagd, voert u uw wachtwoord in.
- Voeg het pad van de pip-installatie toe aan de padvariabele.
- Ga naar de onderkant van het bestand en geef het pad dat u wilt toevoegen als laatste item in de lijst op, bijvoorbeeld `PATH=$PATH:/usr/local/bin`.
- Druk op Control-X om af te sluiten.
- Voer `Y` in om de gewijzigde buffer op te slaan.
- Dat is alles. U kunt dit testen door het volgende te typen in een nieuw Terminal-venster: `echo $PATH`.

Installeer Flask.

```bash
pip install flask --user
```

Installeer Jinja2. Een sjabloonengine met volledige functionaliteit voor Python.

```bash
pip install Jinja2 --user
```

Installeer virtualenv. Een hulpprogramma om geïsoleerde Python-omgevingen te maken.

```bash
pip install virtualenv --user
```

Installeer virtualenvwrapper. Het idee achter virtualenvwrapper is om het gebruik van virtualenv te vereenvoudigen.

```bash
pip install virtualenvwrapper --user
```

Installeer de requests-module. Requests is een voor Apache2 gelicentieerde HTTP-bibliotheek, geschreven in Python.

```bash
pip install requests --user
```

Installeer de python-dotenv-module. Deze module leest het sleutel-waardepaar van het .env-bestand en voegt dit toe aan de omgevingsvariabele.

```bash
pip install python-dotenv --user
```

Kies een map waarin u uw virtuele omgevingen wilt opslaan en voer deze opdracht uit

```bash
mkdir ~/.virtualenvs
```

`cd` naar de Python-map met de voorbeeldtoepassing van de Insluitende lezer-SDK.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Een virtuele omgeving maken

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Verbind het voorbeeldproject met de omgeving. Hiermee wordt de zojuist gemaakte virtuele omgeving toegewezen aan de basismap van het voorbeeldproject.

```bash
setprojectdir .
```

Activeer de virtuele omgeving.

```bash
activate
```

Het project moet nu actief zijn en u ziet iets als `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` in de opdrachtprompt.

Deactiveer de omgeving.

```bash
deactivate
```

Het voorvoegsel `(advanced-python)` moet nu zijn verdwenen omdat de omgeving nu is gedeactiveerd.

Voer `workon advanced-python` uit vanuit de hoofdmap van het voorbeeldproject om de omgeving opnieuw te activeren.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>De Insluitende lezer starten met voorbeeldinhoud

Wanneer de omgeving actief is, voert u het voorbeeldproject uit door `flask run` vanuit de hoofdmap van het voorbeeldproject in te voeren.

```bash
flask run
```

Open uw browser en ga naar _http://localhost:5000_ .

## <a name="next-steps"></a>Volgende stappen

* De [SDK voor insluitende lezers](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor insluitende lezers](./reference.md) verkennen
* Codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) bekijken
