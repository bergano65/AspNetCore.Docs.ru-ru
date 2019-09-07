---
title: Учебник. Вызов веб-API ASP.NET Core с помощью JavaScript
author: rick-anderson
description: Узнайте, как вызывать веб-API ASP.NET Core с помощью JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 0070816149d64fc1d71d453eb0f135050c78597a
ms.sourcegitcommit: de17150e5ec7507d7114dde0e5dbc2e45a66ef53
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70116643"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="21d94-103">Учебник. Вызов веб-API ASP.NET Core с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="21d94-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="21d94-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="21d94-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="21d94-105">В этом руководстве описано, как вызвать веб-API ASP.NET Core с помощью JavaScript и [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="21d94-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="21d94-106">Если вы используете ASP.NET Core 2.2, см. соответствующий раздел руководства по [вызову веб-API с помощью JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="21d94-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="21d94-107">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="21d94-107">Prerequisites</span></span>

* <span data-ttu-id="21d94-108">Изучите [Учебник. Создание веб-API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="21d94-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="21d94-109">Опыт работы с CSS, HTML и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="21d94-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="21d94-110">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="21d94-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="21d94-111">В этом разделе описано, как добавить HTML-страницу, содержащую формы для создания и администрирования элементов списка задач.</span><span class="sxs-lookup"><span data-stu-id="21d94-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="21d94-112">Обработчики событий присоединяются к элементам на странице.</span><span class="sxs-lookup"><span data-stu-id="21d94-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="21d94-113">При использовании обработчиков событий создаются запросы HTTP к методам действия веб-API.</span><span class="sxs-lookup"><span data-stu-id="21d94-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="21d94-114">Функция Fetch API `fetch` инициирует каждый такой запрос HTTP.</span><span class="sxs-lookup"><span data-stu-id="21d94-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="21d94-115">Функция `fetch` возвращает объект `Promise`, который содержит ответ HTTP, представленный в виде объекта `Response`.</span><span class="sxs-lookup"><span data-stu-id="21d94-115">The `fetch` function returns a `Promise` object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="21d94-116">Распространенным подходом является извлечение текста ответа JSON путем вызова функции `json` в объекте `Response`.</span><span class="sxs-lookup"><span data-stu-id="21d94-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="21d94-117">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="21d94-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="21d94-118">Самый простой вызов `fetch` принимает один параметр, представляющий маршрут.</span><span class="sxs-lookup"><span data-stu-id="21d94-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="21d94-119">Второй параметр (объект `init`) является необязательным.</span><span class="sxs-lookup"><span data-stu-id="21d94-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="21d94-120">`init` используется для настройки запроса HTTP.</span><span class="sxs-lookup"><span data-stu-id="21d94-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="21d94-121">Настройте в приложении [обслуживание статических файлов](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) и [включите сопоставление файлов по умолчанию](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="21d94-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="21d94-122">Вставьте в метод `Configure` в файле *Startup.cs* следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="21d94-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="21d94-123">Создайте каталог *wwwroot* в корневой папке проекта.</span><span class="sxs-lookup"><span data-stu-id="21d94-123">Create a *wwwroot* directory in the project root.</span></span>

1. <span data-ttu-id="21d94-124">Добавьте HTML-файл *index.html* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="21d94-124">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="21d94-125">Замените его содержимое следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="21d94-125">Replace its contents with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="21d94-126">Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="21d94-126">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="21d94-127">Замените его содержимое следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="21d94-127">Replace its contents with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="21d94-128">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="21d94-128">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="21d94-129">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="21d94-129">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="21d94-130">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="21d94-130">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="21d94-131">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="21d94-131">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="21d94-132">Ниже приводится пояснение запросов веб-API.</span><span class="sxs-lookup"><span data-stu-id="21d94-132">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="21d94-133">Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="21d94-133">Get a list of to-do items</span></span>

<span data-ttu-id="21d94-134">В следующем коде HTTP-запрос GET направляется по пути *api/TodoItems*:</span><span class="sxs-lookup"><span data-stu-id="21d94-134">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="21d94-135">Когда веб-API возвращает код состояния, указывающий на успешное выполнение, вызывается функция `_displayItems`.</span><span class="sxs-lookup"><span data-stu-id="21d94-135">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="21d94-136">Каждый элемент списка задач в параметре массива, который принимается `_displayItems`, добавляется в таблицу с помощью кнопок **Изменить** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="21d94-136">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="21d94-137">Если запрос веб-API завершается сбоем, в консоли браузера регистрируется сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="21d94-137">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="21d94-138">Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="21d94-138">Add a to-do item</span></span>

<span data-ttu-id="21d94-139">В приведенном ниже коде выполняется следующее:</span><span class="sxs-lookup"><span data-stu-id="21d94-139">In the following code:</span></span>

* <span data-ttu-id="21d94-140">Переменная `item` объявляется для создания представления объектного литерала элемента списка задач.</span><span class="sxs-lookup"><span data-stu-id="21d94-140">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="21d94-141">Для запроса Fetch настраиваются следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="21d94-141">A Fetch request is configured with the following options:</span></span>
    * <span data-ttu-id="21d94-142">`method` определяет команду действия HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="21d94-142">`method`&mdash;specifies the POST HTTP action verb.</span></span>
    * <span data-ttu-id="21d94-143">`body` определяет представление JSON текста запроса.</span><span class="sxs-lookup"><span data-stu-id="21d94-143">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="21d94-144">JSON создается путем передачи литерала объекта, хранящегося в `item`, в функцию [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="21d94-144">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
    * <span data-ttu-id="21d94-145">`headers` определяет заголовки `Accept` и `Content-Type` запросов HTTP.</span><span class="sxs-lookup"><span data-stu-id="21d94-145">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="21d94-146">Для обеих параметров устанавливается значение `application/json`, чтобы классифицировать тип носителя при получении и отправке соответственно.</span><span class="sxs-lookup"><span data-stu-id="21d94-146">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="21d94-147">HTTP-запрос POST направляется по пути *api/TodoItems*.</span><span class="sxs-lookup"><span data-stu-id="21d94-147">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="21d94-148">Когда веб-API возвращает код состояния, указывающий на успешное выполнение, вызывается функция `getItems` для обновления таблицы HTML.</span><span class="sxs-lookup"><span data-stu-id="21d94-148">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="21d94-149">Если запрос веб-API завершается сбоем, в консоли браузера регистрируется сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="21d94-149">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="21d94-150">Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="21d94-150">Update a to-do item</span></span>

<span data-ttu-id="21d94-151">Обновление элемента списка задач аналогично его добавлению. Но есть два существенных отличия:</span><span class="sxs-lookup"><span data-stu-id="21d94-151">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="21d94-152">Путь имеет суффикс с уникальным идентификатором обновляемого элемента.</span><span class="sxs-lookup"><span data-stu-id="21d94-152">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="21d94-153">Например, *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="21d94-153">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="21d94-154">Команда действия HTTP — это PUT, как указано в параметре `method`.</span><span class="sxs-lookup"><span data-stu-id="21d94-154">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="21d94-155">Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="21d94-155">Delete a to-do item</span></span>

<span data-ttu-id="21d94-156">Чтобы удалить элемент списка задач, укажите для параметра запроса `method` значение `DELETE` и определите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="21d94-156">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="21d94-157">Перейдите к следующему руководству, в котором описано, как создавать страницы справки по веб-API:</span><span class="sxs-lookup"><span data-stu-id="21d94-157">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end