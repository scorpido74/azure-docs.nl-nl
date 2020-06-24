---
title: Front-end-Frameworks configureren met de preview-versie van statische Web Apps van Azure
description: Instellingen voor populaire front-end-frameworks die nodig zijn voor statische Azure-Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: cshoe
ms.openlocfilehash: 41f0c44ad65af5fdf2560da3f977a28f135af878
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976707"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Front-end-frameworks en-bibliotheken configureren met de preview-versie van Azure static Web Apps

De statische Web Apps van Azure vereist dat u de juiste configuratie waarden hebt in het [Build-configuratie bestand](github-actions-workflow.md) voor uw front-end-Framework of-bibliotheek.

## <a name="configuration"></a>Configuratie

De volgende tabel bevat de instellingen voor een reeks frameworks en bibliotheken<sup>1</sup>.

De bedoeling van de tabel kolommen wordt uitgelegd door de volgende items:

- **Locatie van app-artefact**: hier wordt de waarde weer gegeven voor `app_artifact_location` . Dit is de [map voor de samengestelde versies van toepassings bestanden](github-actions-workflow.md#build-and-deploy).

- **Aangepaste build-opdracht**: wanneer voor het Framework een andere opdracht dan of is vereist `npm run build` `npm run azure:build` , kunt u een [aangepaste build-opdracht](github-actions-workflow.md#custom-build-commands)definiëren.

| Framework | Locatie van app-artefact | Opdracht voor aangepaste build |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n.v.t. <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Hoek, universeel](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | N.v.t. |
| [Backbone.js](https://backbonejs.org/) | `/` | N.v.t. |
| [Ember](https://emberjs.com/) | `dist` | N.v.t. |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | N.v.t. |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | N.v.t. |
| [Hyperapp](https://hyperapp.dev/) | `/` | N.v.t. |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | N.v.t. |
| [jQuery](https://jquery.com/) | `/` | N.v.t. |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | N.v.t. |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | N.v.t. |
| [Marko](https://markojs.com/) | `public` | N.v.t. |
| [Meteor](https://www.meteor.com/) | `bundle` | N.v.t. |
| [Mithril](https://mithril.js.org/) | `dist` | N.v.t. |
| [Omvat](https://www.polymer-project.org/) | `build/default` | N.v.t. |
| [Preact](https://preactjs.com/) | `build` | N.v.t. |
| [React](https://reactjs.org/) | `build` | N.v.t. |
| [Muis](https://stenciljs.com/) | `www` | N.v.t. |
| [Svelte](https://svelte.dev/) | `public` | N.v.t. |
| [Three.js](https://threejs.org/) | `/` | N.v.t. |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | N.v.t. |
| [Vue](http://vuejs.com/) | `dist` | N.v.t. |

<sup>1</sup> de bovenstaande tabel is niet bedoeld als een uitputtende lijst van frameworks en bibliotheken die samen werken met Azure static web apps.

<sup>2</sup> niet van toepassing

## <a name="next-steps"></a>Volgende stappen

- [Bouw- en werkstroomconfiguratie](github-actions-workflow.md)
