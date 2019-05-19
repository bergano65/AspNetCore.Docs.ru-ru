---
title: Образы Docker для ASP.NET Core
author: tdykstra
description: Узнайте, как использовать опубликованные образы Docker для .NET Core из реестра Docker. Извлечение и создание образов.
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/09/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 48fc53a4c2139960c0f696af5732ff68fc6c4b8a
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65451012"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="3c513-104">Образы Docker для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c513-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="3c513-105">В этом руководстве показано, как запустить приложение ASP.NET Core в контейнерах Docker.</span><span class="sxs-lookup"><span data-stu-id="3c513-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="3c513-106">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="3c513-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="3c513-107">узнаете о создании образов Docker для Microsoft .NET Core;</span><span class="sxs-lookup"><span data-stu-id="3c513-107">Learn about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="3c513-108">скачивание примера приложения ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="3c513-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="3c513-109">локальное выполнение примера приложения;</span><span class="sxs-lookup"><span data-stu-id="3c513-109">Run the sample app locally</span></span>
> * <span data-ttu-id="3c513-110">выполнение примера приложения в контейнерах Linux;</span><span class="sxs-lookup"><span data-stu-id="3c513-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="3c513-111">выполнение примера приложения в контейнерах Windows;</span><span class="sxs-lookup"><span data-stu-id="3c513-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="3c513-112">локальная сборка и развертывание.</span><span class="sxs-lookup"><span data-stu-id="3c513-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="3c513-113">Образы Docker для .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c513-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="3c513-114">Для работы с этим руководством следует скачать пример приложения ASP.NET Core и запустить его в контейнерах Docker.</span><span class="sxs-lookup"><span data-stu-id="3c513-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="3c513-115">Этот пример поддерживается в контейнерах Linux и Windows.</span><span class="sxs-lookup"><span data-stu-id="3c513-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="3c513-116">Пример файла Dockerfile использует [функцию многоэтапной сборки Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) для сборки и выполнения в разных контейнерах.</span><span class="sxs-lookup"><span data-stu-id="3c513-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="3c513-117">Контейнеры для сборки и выполнения создаются на основе образов, предоставленных корпорацией Майкрософт в Docker Hub:</span><span class="sxs-lookup"><span data-stu-id="3c513-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="3c513-118">Этот образ используется в этом примере для создания приложения.</span><span class="sxs-lookup"><span data-stu-id="3c513-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="3c513-119">Образ содержит пакет SDK для .NET Core, который включает программы командной строки (CLI).</span><span class="sxs-lookup"><span data-stu-id="3c513-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="3c513-120">Он оптимизирован для локальной разработки, отладки и модульного тестирования.</span><span class="sxs-lookup"><span data-stu-id="3c513-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="3c513-121">Установленные средства разработки и компиляции делают этот образ относительно большим.</span><span class="sxs-lookup"><span data-stu-id="3c513-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet` 

   <span data-ttu-id="3c513-122">Этот образ используется в этом примере для выполнения приложения.</span><span class="sxs-lookup"><span data-stu-id="3c513-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="3c513-123">Этот образ содержит среду выполнения и библиотеки ASP.NET Core и оптимизирован для запуска приложений в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="3c513-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="3c513-124">Образ нацелен на высокую скорость развертывания и запуска приложений, поэтому он сравнительно невелик, что позволяет оптимизировать производительность сети между реестром Docker и узлом Docker.</span><span class="sxs-lookup"><span data-stu-id="3c513-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="3c513-125">В контейнер копируются только двоичные файлы и содержимое, необходимые для запуска приложений.</span><span class="sxs-lookup"><span data-stu-id="3c513-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="3c513-126">Все содержимое готово к запуску, что гарантирует самый короткий срок от запуска `Docker run` до запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="3c513-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="3c513-127">В модели Docker динамическая компиляция кода не требуется.</span><span class="sxs-lookup"><span data-stu-id="3c513-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3c513-128">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="3c513-128">Prerequisites</span></span>

* [<span data-ttu-id="3c513-129">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="3c513-129">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/core)

* <span data-ttu-id="3c513-130">Клиент Docker 18.03 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="3c513-130">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="3c513-131">Дистрибутивах Linux</span><span class="sxs-lookup"><span data-stu-id="3c513-131">Linux distributions</span></span>
    * [<span data-ttu-id="3c513-132">CentOS</span><span class="sxs-lookup"><span data-stu-id="3c513-132">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="3c513-133">Debian</span><span class="sxs-lookup"><span data-stu-id="3c513-133">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="3c513-134">Fedora</span><span class="sxs-lookup"><span data-stu-id="3c513-134">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="3c513-135">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="3c513-135">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="3c513-136">macOS</span><span class="sxs-lookup"><span data-stu-id="3c513-136">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="3c513-137">Windows</span><span class="sxs-lookup"><span data-stu-id="3c513-137">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="3c513-138">Git</span><span class="sxs-lookup"><span data-stu-id="3c513-138">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="3c513-139">Скачивание примера приложения</span><span class="sxs-lookup"><span data-stu-id="3c513-139">Download the sample app</span></span>

* <span data-ttu-id="3c513-140">Скачайте пример, клонировав [репозиторий Docker для .NET Core](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="3c513-140">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="3c513-141">Локальный запуск приложения</span><span class="sxs-lookup"><span data-stu-id="3c513-141">Run the app locally</span></span>

* <span data-ttu-id="3c513-142">Перейдите в папку проекта *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3c513-142">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="3c513-143">Выполните следующую команду, чтобы собрать и запустить приложение локально:</span><span class="sxs-lookup"><span data-stu-id="3c513-143">Run the following command to build and run the app locally:</span></span>

  ```console
  dotnet run
  ```

* <span data-ttu-id="3c513-144">В браузере перейдите по адресу `http://localhost:5000`, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="3c513-144">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="3c513-145">Нажмите сочетание клавиш CTRL+C в командной строке, чтобы остановить приложение.</span><span class="sxs-lookup"><span data-stu-id="3c513-145">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="3c513-146">Выполнение в контейнере Linux</span><span class="sxs-lookup"><span data-stu-id="3c513-146">Run in a Linux container</span></span>

* <span data-ttu-id="3c513-147">Переключите клиент Docker на контейнеры Linux.</span><span class="sxs-lookup"><span data-stu-id="3c513-147">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="3c513-148">Перейдите в папку проекта Dockerfile *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3c513-148">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="3c513-149">Выполните следующие команды, чтобы собрать и запустить пример в Docker:</span><span class="sxs-lookup"><span data-stu-id="3c513-149">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="3c513-150">Команда `build` выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="3c513-150">The `build` command arguments:</span></span>
  * <span data-ttu-id="3c513-151">присваивает образу имя aspnetapp;</span><span class="sxs-lookup"><span data-stu-id="3c513-151">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="3c513-152">ищет файл Dockerfile в текущей папке (точка в конце).</span><span class="sxs-lookup"><span data-stu-id="3c513-152">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="3c513-153">Команда run выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="3c513-153">The run command arguments:</span></span>
  * <span data-ttu-id="3c513-154">создает псевдотерминал и сохраняет это окно открытым, даже если он не подключен</span><span class="sxs-lookup"><span data-stu-id="3c513-154">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="3c513-155">(действует так же, как `--interactive --tty`);</span><span class="sxs-lookup"><span data-stu-id="3c513-155">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="3c513-156">автоматически удаляет контейнер при завершении работы;</span><span class="sxs-lookup"><span data-stu-id="3c513-156">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="3c513-157">сопоставляет порт 5000 на локальном компьютере с портом 80 в контейнере;</span><span class="sxs-lookup"><span data-stu-id="3c513-157">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="3c513-158">присваивает контейнеру имя aspnetcore_sample;</span><span class="sxs-lookup"><span data-stu-id="3c513-158">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="3c513-159">указывает образ aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="3c513-159">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="3c513-160">В браузере перейдите по адресу `http://localhost:5000`, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="3c513-160">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="3c513-161">Запуск в контейнере Windows</span><span class="sxs-lookup"><span data-stu-id="3c513-161">Run in a Windows container</span></span>

* <span data-ttu-id="3c513-162">Переключите клиент Docker на контейнеры Windows.</span><span class="sxs-lookup"><span data-stu-id="3c513-162">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="3c513-163">Перейдите к папке файла docker: `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="3c513-163">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="3c513-164">Выполните следующие команды, чтобы собрать и запустить пример в Docker:</span><span class="sxs-lookup"><span data-stu-id="3c513-164">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="3c513-165">Для контейнеров Windows вам нужен IP-адрес контейнера (адрес `http://localhost:5000` не будет работать):</span><span class="sxs-lookup"><span data-stu-id="3c513-165">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="3c513-166">Откройте другую командную строку.</span><span class="sxs-lookup"><span data-stu-id="3c513-166">Open up another command prompt.</span></span>
  * <span data-ttu-id="3c513-167">Запустите `docker ps`, чтобы получить список запущенных контейнеров.</span><span class="sxs-lookup"><span data-stu-id="3c513-167">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="3c513-168">Убедитесь, что в списке присутствует контейнер "aspnetcore_sample".</span><span class="sxs-lookup"><span data-stu-id="3c513-168">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="3c513-169">Выполните `docker exec aspnetcore_sample ipconfig`, чтобы отобразить IP-адрес контейнера.</span><span class="sxs-lookup"><span data-stu-id="3c513-169">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="3c513-170">Эта команда возвращает примерно такой результат:</span><span class="sxs-lookup"><span data-stu-id="3c513-170">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="3c513-171">Скопируйте IPv4-адрес контейнера (например 172.29.245.43) и вставьте его в адресную строку браузера, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="3c513-171">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="3c513-172">Локальная сборка и развертывание</span><span class="sxs-lookup"><span data-stu-id="3c513-172">Build and deploy manually</span></span>

<span data-ttu-id="3c513-173">В некоторых сценариях вам потребуется развернуть приложение в контейнер, скопировав нужные для выполнения файлы приложения.</span><span class="sxs-lookup"><span data-stu-id="3c513-173">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="3c513-174">В этом разделе показано, как развернуть приложение вручную.</span><span class="sxs-lookup"><span data-stu-id="3c513-174">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="3c513-175">Перейдите в папку проекта *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3c513-175">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="3c513-176">Выполните команду [dotnet publish](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish.md).</span><span class="sxs-lookup"><span data-stu-id="3c513-176">Run the [dotnet publish](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish.md) command:</span></span>

  ```console
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="3c513-177">Эта команда выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="3c513-177">The command arguments:</span></span>
  * <span data-ttu-id="3c513-178">собирает приложение в режиме выпуска (по умолчанию используется режим отладки);</span><span class="sxs-lookup"><span data-stu-id="3c513-178">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="3c513-179">создает файлы в папке *published*.</span><span class="sxs-lookup"><span data-stu-id="3c513-179">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="3c513-180">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="3c513-180">Run the application.</span></span>

  * <span data-ttu-id="3c513-181">Windows:</span><span class="sxs-lookup"><span data-stu-id="3c513-181">Windows:</span></span>

    ```console
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="3c513-182">Linux:</span><span class="sxs-lookup"><span data-stu-id="3c513-182">Linux:</span></span>

    ```bash
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="3c513-183">Перейдите по адресу `http://localhost:5000` на домашнюю страницу приложения.</span><span class="sxs-lookup"><span data-stu-id="3c513-183">Browse to `http://localhost:5000` to see the home page.</span></span>

### <a name="the-dockerfile"></a><span data-ttu-id="3c513-184">Файл Dockerfile</span><span class="sxs-lookup"><span data-stu-id="3c513-184">The Dockerfile</span></span>

<span data-ttu-id="3c513-185">Представленный здесь файл Dockerfile используется в команде `docker build`, которую вы выполняли ранее.</span><span class="sxs-lookup"><span data-stu-id="3c513-185">Here's the Dockerfile used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="3c513-186">Она использует `dotnet publish` для создания и развертывания так же, как показано в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="3c513-186">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

## <a name="additional-resources"></a><span data-ttu-id="3c513-187">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3c513-187">Additional resources</span></span>

* [<span data-ttu-id="3c513-188">Команда Docker build</span><span class="sxs-lookup"><span data-stu-id="3c513-188">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="3c513-189">Команда Docker run</span><span class="sxs-lookup"><span data-stu-id="3c513-189">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="3c513-190">[Пример ASP.NET Core для Docker](https://github.com/dotnet/dotnet-docker) (который используется в этом руководстве).</span><span class="sxs-lookup"><span data-stu-id="3c513-190">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="3c513-191">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="3c513-191">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="3c513-192">Работа со средствами Visual Studio для Docker</span><span class="sxs-lookup"><span data-stu-id="3c513-192">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="3c513-193">Отладка с помощью Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3c513-193">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a><span data-ttu-id="3c513-194">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="3c513-194">Next steps</span></span>

<span data-ttu-id="3c513-195">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="3c513-195">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="3c513-196">узнали о создании образов Docker для Microsoft .NET Core;</span><span class="sxs-lookup"><span data-stu-id="3c513-196">Learned about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="3c513-197">скачивание примера приложения ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="3c513-197">Downloaded an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="3c513-198">локальное выполнение примера приложения;</span><span class="sxs-lookup"><span data-stu-id="3c513-198">Run the sample app locally</span></span>
> * <span data-ttu-id="3c513-199">выполнение примера приложения в контейнерах Linux;</span><span class="sxs-lookup"><span data-stu-id="3c513-199">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="3c513-200">выполнение примера приложения в контейнерах Windows;</span><span class="sxs-lookup"><span data-stu-id="3c513-200">Run the sample with in Windows containers</span></span>
> * <span data-ttu-id="3c513-201">сборка и развертывание вручную.</span><span class="sxs-lookup"><span data-stu-id="3c513-201">Built and deployed manually</span></span>

<span data-ttu-id="3c513-202">Репозиторий Git помимо примера приложения содержит и документацию.</span><span class="sxs-lookup"><span data-stu-id="3c513-202">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="3c513-203">Обзор ресурсов, доступных в этом репозитории, см. [в файле README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="3c513-203">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="3c513-204">Особый интерес представляют сведения о реализации протокола HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3c513-204">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="3c513-205">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) (Разработка приложений ASP.NET Core с применением Docker по протоколу HTTPS)</span><span class="sxs-lookup"><span data-stu-id="3c513-205">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)</span></span>