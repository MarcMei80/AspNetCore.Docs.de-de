---
title: Docker-Images für ASP.NET Core
author: tdykstra
description: Erfahren Sie, wie Sie veröffentlichte .NET Core-Docker-Images aus der Docker-Registrierung verwenden können. Übertragen Sie Images mithilfe von Pull, und erstellen Sie eigene Images.
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/09/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 48fc53a4c2139960c0f696af5732ff68fc6c4b8a
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65451013"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="bf549-104">Docker-Images für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf549-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="bf549-105">In diesem Tutorial wird gezeigt, wie eine ASP.NET Core-App in Docker-Containern ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bf549-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="bf549-106">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="bf549-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="bf549-107">Allgemeines zu Docker-Images in Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="bf549-107">Learn about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="bf549-108">Herunterladen einer ASP.NET Core-Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="bf549-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="bf549-109">Lokales Ausführen der Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="bf549-109">Run the sample app locally</span></span>
> * <span data-ttu-id="bf549-110">Ausführen der Beispiel-App in Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="bf549-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="bf549-111">Ausführen der Beispiel-App in Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="bf549-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="bf549-112">Manuelles Erstellen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="bf549-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="bf549-113">ASP.NET Core Docker-Images</span><span class="sxs-lookup"><span data-stu-id="bf549-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="bf549-114">Für dieses Tutorial laden Sie eine ASP.NET Core-Beispiel-App herunter, und führen sie in Docker-Containern aus.</span><span class="sxs-lookup"><span data-stu-id="bf549-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="bf549-115">Das Beispiel funktioniert sowohl mit Linux- als auch mit Windows-Containern.</span><span class="sxs-lookup"><span data-stu-id="bf549-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="bf549-116">Die Dockerfile-Beispieldatei verwendet das [Docker-Feature für mehrstufige Builds](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) zur Erstellung und Ausführung in verschiedenen Containern.</span><span class="sxs-lookup"><span data-stu-id="bf549-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="bf549-117">Die Erstellungs- und Ausführungscontainer werden aus Images erstellt, die im Docker-Hub von Microsoft bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="bf549-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="bf549-118">Das Beispiel verwendet dieses Image für die Erstellung der App.</span><span class="sxs-lookup"><span data-stu-id="bf549-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="bf549-119">Das Image enthält das .NET Core SDK mit den Befehlszeilentools (CLI).</span><span class="sxs-lookup"><span data-stu-id="bf549-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="bf549-120">Das Image ist für die lokale Entwicklung sowie für das Debuggen und für Komponententests optimiert.</span><span class="sxs-lookup"><span data-stu-id="bf549-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="bf549-121">Die für Entwicklung und Kompilierung installierten Tools machen dies zu einem relativ großen Image.</span><span class="sxs-lookup"><span data-stu-id="bf549-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet` 

   <span data-ttu-id="bf549-122">Das Beispiel verwendet dieses Image zum Ausführen der App.</span><span class="sxs-lookup"><span data-stu-id="bf549-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="bf549-123">Das Image enthält die ASP.NET Core-Runtime und -Bibliotheken und wurde für das Ausführen von Apps in der Produktion optimiert.</span><span class="sxs-lookup"><span data-stu-id="bf549-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="bf549-124">Das Image wurde im Hinblick auf Bereitstellungs- und App-Startgeschwindigkeit entwickelt und ist daher relativ klein, sodass die Netzwerkleistung von Docker-Registrierung zu Docker-Host optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="bf549-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="bf549-125">Nur die zum Ausführen einer App benötigten Binärdateien und Inhalte werden in den Container kopiert.</span><span class="sxs-lookup"><span data-stu-id="bf549-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="bf549-126">Die Inhalte sind bereit zur Ausführung und ermöglichen in kürzester Zeit nach dem `Docker run` das Starten der App.</span><span class="sxs-lookup"><span data-stu-id="bf549-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="bf549-127">Die dynamische Codekompilierung ist im Docker-Modell nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bf549-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bf549-128">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="bf549-128">Prerequisites</span></span>

* [<span data-ttu-id="bf549-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="bf549-129">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/core)

* <span data-ttu-id="bf549-130">Docker-Client 18.03 oder höher</span><span class="sxs-lookup"><span data-stu-id="bf549-130">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="bf549-131">Linux-Verteilungen</span><span class="sxs-lookup"><span data-stu-id="bf549-131">Linux distributions</span></span>
    * [<span data-ttu-id="bf549-132">CentOS</span><span class="sxs-lookup"><span data-stu-id="bf549-132">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="bf549-133">Debian</span><span class="sxs-lookup"><span data-stu-id="bf549-133">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="bf549-134">Fedora</span><span class="sxs-lookup"><span data-stu-id="bf549-134">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="bf549-135">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="bf549-135">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="bf549-136">macOS</span><span class="sxs-lookup"><span data-stu-id="bf549-136">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="bf549-137">Windows</span><span class="sxs-lookup"><span data-stu-id="bf549-137">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="bf549-138">Git</span><span class="sxs-lookup"><span data-stu-id="bf549-138">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="bf549-139">Herunterladen der Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="bf549-139">Download the sample app</span></span>

* <span data-ttu-id="bf549-140">Laden Sie das Beispiel durch Klonen des [.NET Core-Docker-Repositorys](https://github.com/dotnet/dotnet-docker) herunter:</span><span class="sxs-lookup"><span data-stu-id="bf549-140">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="bf549-141">Lokales Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="bf549-141">Run the app locally</span></span>

* <span data-ttu-id="bf549-142">Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="bf549-142">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="bf549-143">Führen Sie den folgenden Befehl aus, um die App zu erstellen und lokal auszuführen:</span><span class="sxs-lookup"><span data-stu-id="bf549-143">Run the following command to build and run the app locally:</span></span>

  ```console
  dotnet run
  ```

* <span data-ttu-id="bf549-144">Navigieren Sie in einem Browser zu `http://localhost:5000`, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="bf549-144">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="bf549-145">Drücken Sie Strg+C an der Eingabeaufforderung, um die App zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="bf549-145">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="bf549-146">Ausführen in einem Linux-Container</span><span class="sxs-lookup"><span data-stu-id="bf549-146">Run in a Linux container</span></span>

* <span data-ttu-id="bf549-147">Wechseln Sie im Docker-Client zu Linux-Containern.</span><span class="sxs-lookup"><span data-stu-id="bf549-147">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="bf549-148">Navigieren Sie zum Dockerfile-Ordner unter *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="bf549-148">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="bf549-149">Führen Sie die folgenden Befehle zum Erstellen und Ausführen des Beispiels in Docker aus:</span><span class="sxs-lookup"><span data-stu-id="bf549-149">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="bf549-150">Argumente des `build`-Befehls:</span><span class="sxs-lookup"><span data-stu-id="bf549-150">The `build` command arguments:</span></span>
  * <span data-ttu-id="bf549-151">Nennen Sie das Image „aspnetapp“.</span><span class="sxs-lookup"><span data-stu-id="bf549-151">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="bf549-152">Suchen Sie nach der Dockerfil-Datei im aktuellen Ordner (Punkt am Ende).</span><span class="sxs-lookup"><span data-stu-id="bf549-152">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="bf549-153">Argumente des run-Befehls:</span><span class="sxs-lookup"><span data-stu-id="bf549-153">The run command arguments:</span></span>
  * <span data-ttu-id="bf549-154">Ordnen Sie ein pseudo-TTY zu, und halten Sie es offen, wenn auch nicht angefügt.</span><span class="sxs-lookup"><span data-stu-id="bf549-154">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="bf549-155">(Dieselben Auswirkungen wie `--interactive --tty`.)</span><span class="sxs-lookup"><span data-stu-id="bf549-155">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="bf549-156">Entfernen Sie den Container, wenn er beendet wird.</span><span class="sxs-lookup"><span data-stu-id="bf549-156">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="bf549-157">Ordnen Sie Port 5000 auf dem lokalen Computer Port 80 im Container zu.</span><span class="sxs-lookup"><span data-stu-id="bf549-157">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="bf549-158">Nennen Sie den Container „aspnetcore_sample“.</span><span class="sxs-lookup"><span data-stu-id="bf549-158">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="bf549-159">Geben Sie das aspnetapp-Image an.</span><span class="sxs-lookup"><span data-stu-id="bf549-159">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="bf549-160">Navigieren Sie in einem Browser zu `http://localhost:5000`, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="bf549-160">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="bf549-161">Ausführen in einem Windows-Container</span><span class="sxs-lookup"><span data-stu-id="bf549-161">Run in a Windows container</span></span>

* <span data-ttu-id="bf549-162">Wechseln Sie im Docker-Windows zu Linux-Containern.</span><span class="sxs-lookup"><span data-stu-id="bf549-162">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="bf549-163">Navigieren Sie zum Dockerfile-Ordner unter `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="bf549-163">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="bf549-164">Führen Sie die folgenden Befehle zum Erstellen und Ausführen des Beispiels in Docker aus:</span><span class="sxs-lookup"><span data-stu-id="bf549-164">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="bf549-165">Für Windows-Container benötigen Sie die IP-Adresse des Containers (Navigieren zu `http://localhost:5000` funktioniert nicht):</span><span class="sxs-lookup"><span data-stu-id="bf549-165">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="bf549-166">Öffnen Sie eine weitere Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="bf549-166">Open up another command prompt.</span></span>
  * <span data-ttu-id="bf549-167">Führen Sie `docker ps` aus, um die ausgeführten Container anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bf549-167">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="bf549-168">Vergewissern Sie sich, dass der Container „aspnetcore_sample“ vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bf549-168">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="bf549-169">Führen Sie `docker exec aspnetcore_sample ipconfig` aus, um die IP-Adresse des Containers anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bf549-169">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="bf549-170">Die Ausgabe des Befehls sieht wie im folgenden Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="bf549-170">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="bf549-171">Kopieren Sie die IPv4-Adresse des Containers (z.B. 172.29.245.43), und fügen Sie sie in die Adressleiste des Browsers ein, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="bf549-171">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="bf549-172">Manuelles Erstellen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="bf549-172">Build and deploy manually</span></span>

<span data-ttu-id="bf549-173">In einigen Szenarien möchten Sie eine App möglicherweise in einem Container bereitstellen, indem Sie die zur Laufzeit benötigten Anwendungsdateien in diesen kopieren.</span><span class="sxs-lookup"><span data-stu-id="bf549-173">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="bf549-174">In diesem Abschnitt wird gezeigt, wie Sie die Bereitstellung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="bf549-174">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="bf549-175">Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="bf549-175">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="bf549-176">Führen Sie den Befehl [dotnet publish](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish.md) aus:</span><span class="sxs-lookup"><span data-stu-id="bf549-176">Run the [dotnet publish](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish.md) command:</span></span>

  ```console
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="bf549-177">Argumente des Befehls:</span><span class="sxs-lookup"><span data-stu-id="bf549-177">The command arguments:</span></span>
  * <span data-ttu-id="bf549-178">Erstellen Sie die Anwendung im Releasemodus (der Standardwert ist der Debugmodus).</span><span class="sxs-lookup"><span data-stu-id="bf549-178">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="bf549-179">Erstellen Sie die Dateien im Ordner *published*.</span><span class="sxs-lookup"><span data-stu-id="bf549-179">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="bf549-180">Führen Sie die Anwendung aus.</span><span class="sxs-lookup"><span data-stu-id="bf549-180">Run the application.</span></span>

  * <span data-ttu-id="bf549-181">Windows:</span><span class="sxs-lookup"><span data-stu-id="bf549-181">Windows:</span></span>

    ```console
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="bf549-182">Linux:</span><span class="sxs-lookup"><span data-stu-id="bf549-182">Linux:</span></span>

    ```bash
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="bf549-183">Navigieren Sie zu `http://localhost:5000`, um die Startseite anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="bf549-183">Browse to `http://localhost:5000` to see the home page.</span></span>

### <a name="the-dockerfile"></a><span data-ttu-id="bf549-184">Die Dockerfile-Datei</span><span class="sxs-lookup"><span data-stu-id="bf549-184">The Dockerfile</span></span>

<span data-ttu-id="bf549-185">Dies ist die Dockerfile-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="bf549-185">Here's the Dockerfile used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="bf549-186">Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf549-186">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```console
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a><span data-ttu-id="bf549-187">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bf549-187">Additional resources</span></span>

* [<span data-ttu-id="bf549-188">docker build-Befehl</span><span class="sxs-lookup"><span data-stu-id="bf549-188">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="bf549-189">docker run-Befehl</span><span class="sxs-lookup"><span data-stu-id="bf549-189">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="bf549-190">[Beispiel für ASP.NET Core-Docker](https://github.com/dotnet/dotnet-docker) (das in diesem Tutorial verwendete Beispiel)</span><span class="sxs-lookup"><span data-stu-id="bf549-190">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="bf549-191">Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="bf549-191">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="bf549-192">Arbeiten mit Visual Studio-Tools für Docker</span><span class="sxs-lookup"><span data-stu-id="bf549-192">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="bf549-193">Debuggen mit Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf549-193">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a><span data-ttu-id="bf549-194">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="bf549-194">Next steps</span></span>

<span data-ttu-id="bf549-195">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="bf549-195">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="bf549-196">Allgemeines zu Docker-Images in Microsoft .NET Core erfahren</span><span class="sxs-lookup"><span data-stu-id="bf549-196">Learned about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="bf549-197">ASP.NET Core-Beispiel-App heruntergeladen</span><span class="sxs-lookup"><span data-stu-id="bf549-197">Downloaded an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="bf549-198">Lokales Ausführen der Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="bf549-198">Run the sample app locally</span></span>
> * <span data-ttu-id="bf549-199">Ausführen der Beispiel-App in Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="bf549-199">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="bf549-200">Ausführen des Beispiels in Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="bf549-200">Run the sample with in Windows containers</span></span>
> * <span data-ttu-id="bf549-201">Erstellt und manuell bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="bf549-201">Built and deployed manually</span></span>

<span data-ttu-id="bf549-202">Im Git-Repository, das die Beispiel-App enthält, finden Sie auch die Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="bf549-202">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="bf549-203">Eine Übersicht über die im Repository verfügbaren Ressourcen finden Sie in der [Infodatei](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="bf549-203">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="bf549-204">Insbesondere erfahren Sie, wie Sie HTTPS implementieren:</span><span class="sxs-lookup"><span data-stu-id="bf549-204">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="bf549-205">Entwickeln von ASP.NET Core-Anwendungen mit Docker über HTTPS</span><span class="sxs-lookup"><span data-stu-id="bf549-205">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)