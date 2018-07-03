---
title: Erste Schritte mit ASP.NET Core
author: rick-anderson
description: Kurztutorial, in dem eine einfache Hello World-App mit ASP.NET Core erstellt und ausgeführt wird.
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2018
uid: getting-started
ms.openlocfilehash: 22e9c982921cc03d89506e18ff99bf481027dda6
ms.sourcegitcommit: 931b6a2d7eb28a0f1295e8a95690b8c4c5f58477
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37077659"
---
# <a name="get-started-with-aspnet-core"></a><span data-ttu-id="287b0-103">Erste Schritte mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="287b0-103">Get started with ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

1. <span data-ttu-id="287b0-104">Installieren Sie [!INCLUDE [](~/includes/2.1-SDK.md)].</span><span class="sxs-lookup"><span data-stu-id="287b0-104">Install the [!INCLUDE [](~/includes/2.1-SDK.md)].</span></span>

2. <span data-ttu-id="287b0-105">Erstellen Sie ein ASP.NET Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="287b0-105">Create an ASP.NET Core project.</span></span> <span data-ttu-id="287b0-106">Öffnen Sie eine Befehlsshell, und geben Sie den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="287b0-106">Open a command shell and enter the following command:</span></span>

    ```console
    dotnet new webapp -o aspnetcoreapp
    ```

    <span data-ttu-id="287b0-107">[!INCLUDE [](~/includes/webapp-alias-notice.md) [](~/includes/webapp-alias-notice.md)]</span><span class="sxs-lookup"><span data-stu-id="287b0-107">[!INCLUDE [](~/includes/webapp-alias-notice.md) [](~/includes/webapp-alias-notice.md)]</span></span>

3. <span data-ttu-id="287b0-108">Vertrauen Sie dem HTTPS-Entwicklungszertifikat:</span><span class="sxs-lookup"><span data-stu-id="287b0-108">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="287b0-109">Windows</span><span class="sxs-lookup"><span data-stu-id="287b0-109">Windows</span></span>](#tab/windows)

    ```console
    dotnet dev-certs https --trust
    ```

    The preceding command displays the following dialog:

    ![Security warning dialog](_static/cert.png)

    Select **Yes** if you agree to trust the development certificate.

# <a name="macostabmacos"></a>[<span data-ttu-id="287b0-110">macOS</span><span class="sxs-lookup"><span data-stu-id="287b0-110">macOS</span></span>](#tab/macos)

    ```console
    dotnet dev-certs https --trust
    ```

    The preceding command displays the following message:

    *Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:*
    `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`
    *This command might prompt you for your password to install the certificate on the system keychain.
    Password:*

    Enter your password if you agree to trust the development certificate.

# <a name="linuxtablinux"></a>[<span data-ttu-id="287b0-111">Linux</span><span class="sxs-lookup"><span data-stu-id="287b0-111">Linux</span></span>](#tab/linux)

    See the documentation for your Linux distribution on how to trust the HTTPS development certificate
---

4. <span data-ttu-id="287b0-112">Führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="287b0-112">Run the app:</span></span>

    ```console
    cd aspnetcoreapp
    dotnet run
    ```

5. <span data-ttu-id="287b0-113">Wechseln Sie zu [http://localhost:5001](http://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="287b0-113">Browse to [http://localhost:5001](http://localhost:5001).</span></span>  <span data-ttu-id="287b0-114">Klicken Sie auf **Accept** (Akzeptieren), um die Datenschutz- und Cookierichtlinie zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="287b0-114">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="287b0-115">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="287b0-115">This app doesn't keep personal information.</span></span>

6. <span data-ttu-id="287b0-116">Öffnen Sie *Pages/About.cshtml*, und modifizieren Sie die Seite mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="287b0-116">Open *Pages/About.cshtml* and modify the page with the following highlighted markup:</span></span>

    [!code-cshtml[](sample/getting-started/about.cshtml?highlight=9)]

7. <span data-ttu-id="287b0-117">Navigieren Sie zu [http://localhost:5001/About](http://localhost:5001/About), und überprüfen Sie, ob die Änderungen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="287b0-117">Browse to [http://localhost:5001/About](http://localhost:5001/About) and verify the changes are displayed.</span></span>

[!INCLUDE [next steps](~/includes/getting-started/next-steps.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

1. <span data-ttu-id="287b0-118">Installieren Sie [!INCLUDE [](~/includes/net-core-sdk-download-link.md)].</span><span class="sxs-lookup"><span data-stu-id="287b0-118">Install the [!INCLUDE [](~/includes/net-core-sdk-download-link.md)].</span></span>

2. <span data-ttu-id="287b0-119">Erstellen Sie ein neues ASP.NET Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="287b0-119">Create a new ASP.NET Core project.</span></span>

   <span data-ttu-id="287b0-120">Öffnen Sie eine Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="287b0-120">Open a command shell.</span></span> <span data-ttu-id="287b0-121">Geben Sie folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="287b0-121">Enter the following command:</span></span>

    ```console
    dotnet new razor -o aspnetcoreapp
    ```

3. <span data-ttu-id="287b0-122">Führen Sie die App mit den folgenden Befehlen aus:</span><span class="sxs-lookup"><span data-stu-id="287b0-122">Run the app with the following commands:</span></span>

    ```console
    cd aspnetcoreapp
    dotnet run
    ```

4. <span data-ttu-id="287b0-123">Wechseln Sie zu [http://localhost:5000](http://localhost:5000).</span><span class="sxs-lookup"><span data-stu-id="287b0-123">Browse to [http://localhost:5000](http://localhost:5000).</span></span>

5. <span data-ttu-id="287b0-124">Öffnen Sie *Pages/About.cshtml*, und verändern Sie die Seite so, dass sie die Meldung „Hallo Welt!“ anzeigt.</span><span class="sxs-lookup"><span data-stu-id="287b0-124">Open *Pages/About.cshtml* and modify the page to display the message "Hello, world!</span></span> <span data-ttu-id="287b0-125">Die Zeit auf dem Server ist @DateTime.Now" :</span><span class="sxs-lookup"><span data-stu-id="287b0-125">The time on the server is @DateTime.Now":</span></span>

    [!code-cshtml[](sample/getting-started/about.cshtml?highlight=9&range=1-9)]

6. <span data-ttu-id="287b0-126">Wechseln Sie zu [http://localhost:5000/About](http://localhost:5000/About), und bestätigen Sie die Änderungen.</span><span class="sxs-lookup"><span data-stu-id="287b0-126">Browse to [http://localhost:5000/About](http://localhost:5000/About) and verify the changes.</span></span>

[!INCLUDE [next steps](~/includes/getting-started/next-steps.md)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

1. <span data-ttu-id="287b0-127">Installieren Sie den .NET Core **SDK Installer** für SDK 1.0.4 von der .NET Core-Seite [Alle Downloads](https://www.microsoft.com/net/download/all).</span><span class="sxs-lookup"><span data-stu-id="287b0-127">Install the .NET Core **SDK Installer** for SDK 1.0.4 from the [.NET Core All Downloads page](https://www.microsoft.com/net/download/all).</span></span>

2. <span data-ttu-id="287b0-128">Erstellen Sie einen Ordner für das neue ASP.NET Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="287b0-128">Create a folder for a new ASP.NET Core project.</span></span>

   <span data-ttu-id="287b0-129">Öffnen Sie eine Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="287b0-129">Open a command shell.</span></span> <span data-ttu-id="287b0-130">Geben Sie die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="287b0-130">Enter the following commands:</span></span>

   ```console
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   ```

3. <span data-ttu-id="287b0-131">Wenn Sie eine höhere Version des SDK auf Ihrem Computer installiert haben, erstellen Sie die Datei *global.json*, und wählen Sie das SDK 1.0.4 aus.</span><span class="sxs-lookup"><span data-stu-id="287b0-131">If you have installed a later SDK version on your machine, create a *global.json* file to select the 1.0.4 SDK.</span></span>

   ```json
   {
     "sdk": { "version": "1.0.4" }
   }
   ```

4. <span data-ttu-id="287b0-132">Erstellen Sie ein neues ASP.NET Core-Projekt.</span><span class="sxs-lookup"><span data-stu-id="287b0-132">Create a new ASP.NET Core project.</span></span>

   ```console
   dotnet new web
   ```

5. <span data-ttu-id="287b0-133">Stellen Sie die Pakete wieder her.</span><span class="sxs-lookup"><span data-stu-id="287b0-133">Restore the packages.</span></span>

    ```console
    dotnet restore
    ```

6. <span data-ttu-id="287b0-134">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="287b0-134">Run the app.</span></span>

   ```console
   dotnet run
   ```

   <span data-ttu-id="287b0-135">Mit dem Befehl [dotnet run](/dotnet/core/tools/dotnet-run) wird die App bei Bedarf zunächst erstellt.</span><span class="sxs-lookup"><span data-stu-id="287b0-135">The [dotnet run](/dotnet/core/tools/dotnet-run) command builds the app first, if needed.</span></span>

7. <span data-ttu-id="287b0-136">Wechseln Sie zu `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="287b0-136">Browse to `http://localhost:5000`.</span></span>

[!INCLUDE [next steps](~/includes/getting-started/next-steps.md)]
::: moniker-end