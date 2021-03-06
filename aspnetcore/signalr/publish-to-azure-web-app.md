---
title: Veröffentlichen einer ASP.net Core SignalR-App auf Azure App Service
author: bradygaster
description: Erfahren Sie, wie Sie eine ASP.net Core SignalR-App zum Azure App Service veröffentlichen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d03a007ca883b3d0391b848e3e92c90469ee640a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652651"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>Veröffentlichen einer ASP.net Core signalr-app in Azure App Service

Von [Brady Gastern](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) ist ein [Microsoft Cloud Computing](https://azure.microsoft.com/) Platform-Dienst zum Hosting von Web-Apps, einschließlich ASP.net Core.

> [!NOTE]
> Dieser Artikel bezieht sich auf das Veröffentlichen einer ASP.net Core signalr-App aus Visual Studio. Weitere Informationen finden Sie unter [signalr Service für Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Veröffentlichen der App

In diesem Artikel wird die Veröffentlichung mit den Tools in Visual Studio behandelt. Visual Studio Code Benutzer können [Azure CLI](/cli/azure) Befehle zum Veröffentlichen von apps in Azure verwenden. Weitere Informationen finden Sie unter [Veröffentlichen einer ASP.net Core-app in Azure mit Befehlszeilen Tools](/azure/app-service/app-service-web-get-started-dotnet).

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Vergewissern Sie sich, dass im Dialogfeld **Veröffentlichungsziel auswählen** die Option **App Service** und **neu erstellen** ausgewählt ist.

1. Wählen Sie im Dropdown Feld " **veröffentlichen** " die Option **Profil erstellen** aus.

   Geben Sie die in der folgenden Tabelle beschriebenen Informationen in das Dialogfeld **App Service erstellen** ein, und wählen Sie **Erstellen**aus.

   | Element               | BESCHREIBUNG |
   | ------------------ | ----------- |
   | **Name**           | Der eindeutige Name der app. |
   | **Abonnement**   | Azure-Abonnement, das von der APP verwendet wird. |
   | **Ressourcengruppe** | Gruppe verwandter Ressourcen, zu denen die APP gehört. |
   | **Hostingplan**   | Der Tarif für die Web-App. |

1. Wählen Sie in der Dropdown Liste **Abhängigkeiten** > **Hinzufügen** den **Azure-SignalR Dienst** aus:

   ![Bereich Abhängigkeiten, in dem die Auswahl von Azure SignalR-Dienst in der Dropdown Liste hinzufügen angezeigt wird](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. Wählen Sie im Dialogfeld **Azure SignalR-Dienst** die Option **neue Azure SignalR Dienst Instanz erstellen**aus.

1. Geben Sie einen **Namen**, eine **Ressourcengruppe**und einen **Speicherort**an. Wechseln Sie zurück zum Dialogfeld **Azure SignalR Service** , und wählen Sie **Hinzufügen**.

Visual Studio führt die folgenden Aufgaben aus:

* Erstellt ein Veröffentlichungs Profil mit Veröffentlichungs Einstellungen.
* Erstellt eine *Azure-Web-App* mit den bereitgestellten Details.
* Veröffentlicht die app.
* Hiermit wird ein Browser gestartet, der die Web-App lädt.

Das Format der App-URL ist `{APP SERVICE NAME}.azurewebsites.net`. Eine APP mit dem Namen `SignalRChatApp` hat z. b. eine URL `https://signalrchatapp.azurewebsites.net`.

Wenn beim Bereitstellen einer APP, die auf eine Vorschauversion von .net Core ausgerichtet ist, ein Fehler vom Typ "http *502,2-* ungültiges Gateway" auftritt, finden Sie weitere Informationen unter Bereitstellen [ASP.net Core Vorschau Release Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)

## <a name="configure-the-app-in-azure-app-service"></a>Konfigurieren der app in Azure App Service

> [!NOTE]
> *Dieser Abschnitt gilt nur für apps, die nicht den Azure SignalR-Dienst verwenden.*
>
> Wenn die APP den Azure SignalR-Dienst verwendet, ist für die APP Service keine Konfiguration der in diesem Abschnitt beschriebenen arr-Affinität (Application Request Routing) und der in diesem Abschnitt beschriebenen websockets erforderlich. Clients verbinden ihre websockets mit dem Azure SignalR-Dienst und nicht direkt mit der app.

Aktivieren Sie für apps, die ohne den Azure SignalR Service gehostet werden, Folgendes:

* [Arr-Affinität](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) zum Weiterleiten von Anforderungen von einem Benutzer an dieselbe App Service Instanz. Die Standardeinstellung ist **on**.
* [Websockets](xref:fundamentals/websockets) , damit der websockets-Transport funktioniert. Die Standardeinstellung ist **Off**.

1. Navigieren Sie in der Azure-Portal zu der Web-App in **App Services**.
1. Öffnen Sie die **Konfigurations** > **allgemeinen Einstellungen**.
1. Legen Sie **websockets** **auf ein**fest.
1. Vergewissern Sie sich, dass **arr-Affinität** **auf**ein festgelegt ist.

## <a name="app-service-plan-limits"></a>App Service Plan Limits

Websockets und andere Transporte sind auf Grundlage des ausgewählten App Service Plans beschränkt. Weitere Informationen finden Sie in den Abschnitten *Azure-Cloud Services Limits* und *App Service Limits* im Artikel Einschränkungen für [Azure-Abonnements und Dienste, Kontingente und Einschränkungen](/azure/azure-subscription-service-limits#app-service-limits) .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Was ist Azure SignalR Service?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Veröffentlichen einer ASP.net Core-app in Azure mit Befehlszeilen Tools](/azure/app-service/app-service-web-get-started-dotnet)
* [Hosten und Bereitstellen ASP.net Core Vorschau-apps in Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
