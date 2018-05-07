---
title: "Пользовательские модули форматирования в веб-интерфейсах API платформы ASP.NET Core MVC"
author: tdykstra
description: "Узнайте, как создавать и использовать пользовательские модули форматирования для веб-интерфейсов API в ASP.NET Core."
manager: wpickett
ms.author: tdykstra
ms.date: 02/08/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/models/custom-formatters
ms.openlocfilehash: 8a42f2d885bd0a0c6d2bd05f9c589def2e15d50a
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2018
---
# <a name="custom-formatters-in-aspnet-core-mvc-web-apis"></a><span data-ttu-id="67dce-103">Пользовательские модули форматирования в веб-интерфейсах API платформы ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="67dce-103">Custom formatters in ASP.NET Core MVC web APIs</span></span>

<span data-ttu-id="67dce-104">Автор: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra)</span><span class="sxs-lookup"><span data-stu-id="67dce-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="67dce-105">В ASP.NET Core MVC имеется встроенная поддержка обмена данными в веб-интерфейсах API с помощью форматов JSON, XML или обычного текста.</span><span class="sxs-lookup"><span data-stu-id="67dce-105">ASP.NET Core MVC has built-in support for data exchange in web APIs by using JSON, XML, or plain text formats.</span></span> <span data-ttu-id="67dce-106">В этой статье показано, как добавить поддержку дополнительных форматов, создав пользовательские модули форматирования.</span><span class="sxs-lookup"><span data-stu-id="67dce-106">This article shows how to add support for additional formats by creating custom formatters.</span></span>

<span data-ttu-id="67dce-107">[Просмотреть или скачать образец с GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="67dce-107">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="67dce-108">Когда следует использовать пользовательские модули форматирования</span><span class="sxs-lookup"><span data-stu-id="67dce-108">When to use custom formatters</span></span>

<span data-ttu-id="67dce-109">Используйте пользовательский модуль форматирования, если необходимо, чтобы процесс [согласования содержимого](xref:mvc/models/formatting) поддерживал тип содержимого, который не поддерживается встроенными модулями форматирования (JSON, XML и обычный текст).</span><span class="sxs-lookup"><span data-stu-id="67dce-109">Use a custom formatter when you want the [content negotiation](xref:mvc/models/formatting) process to support a content type that isn't supported by the built-in formatters (JSON, XML, and plain text).</span></span>

<span data-ttu-id="67dce-110">Например, если некоторые из клиентов вашего веб-интерфейса API могут работать с форматом [Protobuf](https://github.com/google/protobuf), для обмена данными с ними может быть желательно использовать этот формат, так как он более эффективен.</span><span class="sxs-lookup"><span data-stu-id="67dce-110">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span>  <span data-ttu-id="67dce-111">Вам может также потребоваться реализовать отправку имен и адресов веб-интерфейсом API в формате [vCard](https://wikipedia.org/wiki/VCard), распространенном формате для передачи контактных данных.</span><span class="sxs-lookup"><span data-stu-id="67dce-111">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="67dce-112">В образце приложения, представленном в этой статье, реализуется простой модуль форматирования vCard.</span><span class="sxs-lookup"><span data-stu-id="67dce-112">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="67dce-113">Общие сведения об использовании пользовательского модуля форматирования</span><span class="sxs-lookup"><span data-stu-id="67dce-113">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="67dce-114">Чтобы создать и использовать пользовательский модуль форматирования, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="67dce-114">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="67dce-115">Создайте класс модуля форматирования выходных данных, если необходимо сериализовать данные, отправляемые клиенту.</span><span class="sxs-lookup"><span data-stu-id="67dce-115">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="67dce-116">Создайте класс модуля форматирования входных данных, если необходимо десериализировать данные, получаемые от клиента.</span><span class="sxs-lookup"><span data-stu-id="67dce-116">Create an input formatter class if you want to deserialize data received from the client.</span></span> 
* <span data-ttu-id="67dce-117">Добавьте экземпляры модулей форматирования в коллекции `InputFormatters` и `OutputFormatters` в [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="67dce-117">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="67dce-118">В следующих разделах приводятся инструкции по выполнению каждого из этих действий с примерами кода.</span><span class="sxs-lookup"><span data-stu-id="67dce-118">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="67dce-119">Создание пользовательского класса модуля форматирования</span><span class="sxs-lookup"><span data-stu-id="67dce-119">How to create a custom formatter class</span></span>

<span data-ttu-id="67dce-120">Чтобы создать модуль форматирования, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="67dce-120">To create a formatter:</span></span>

* <span data-ttu-id="67dce-121">Наследуйте класс от подходящего базового класса.</span><span class="sxs-lookup"><span data-stu-id="67dce-121">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="67dce-122">Укажите в конструкторе допустимые типы передаваемых данных и кодировки.</span><span class="sxs-lookup"><span data-stu-id="67dce-122">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="67dce-123">Переопределите методы `CanReadType`/`CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="67dce-123">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="67dce-124">Переопределите методы `ReadRequestBodyAsync`/`WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="67dce-124">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="67dce-125">Наследование от подходящего базового класса</span><span class="sxs-lookup"><span data-stu-id="67dce-125">Derive from the appropriate base class</span></span>

<span data-ttu-id="67dce-126">Для текстовых типов данных (например, vCard) произведите наследование от базового класса [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) или [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).</span><span class="sxs-lookup"><span data-stu-id="67dce-126">For text media types (for example, vCard), derive from the [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="67dce-127">Для двоичных типов произведите наследование от базового класса [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) или [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter).</span><span class="sxs-lookup"><span data-stu-id="67dce-127">For binary types, derive from the [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="67dce-128">Указание допустимых типов передаваемых данных и кодировок.</span><span class="sxs-lookup"><span data-stu-id="67dce-128">Specify valid media types and encodings</span></span>

<span data-ttu-id="67dce-129">Укажите в конструкторе допустимые типы передаваемых данных и кодировки, добавив их в коллекции `SupportedMediaTypes` и `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="67dce-129">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

> [!NOTE]  
> <span data-ttu-id="67dce-130">Внедрение зависимостей конструктора в класс модуля форматирования невозможно.</span><span class="sxs-lookup"><span data-stu-id="67dce-130">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="67dce-131">Например, невозможно получить средство ведения журнала, добавив соответствующий параметр в конструктор.</span><span class="sxs-lookup"><span data-stu-id="67dce-131">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="67dce-132">Для доступа к службам необходимо использовать объект контекста, который передается в ваши методы.</span><span class="sxs-lookup"><span data-stu-id="67dce-132">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="67dce-133">В приведенном [ниже](#read-write) примере кода показано, как это делается.</span><span class="sxs-lookup"><span data-stu-id="67dce-133">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="67dce-134">Переопределение методов CanReadType и CanWriteType</span><span class="sxs-lookup"><span data-stu-id="67dce-134">Override CanReadType/CanWriteType</span></span> 

<span data-ttu-id="67dce-135">Укажите типы, в которые может осуществляться десериализация или из которых может осуществляться сериализация, переопределив метод `CanReadType` или `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="67dce-135">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="67dce-136">Например, текст в формате vCard может создаваться только из типа `Contact`, и наоборот.</span><span class="sxs-lookup"><span data-stu-id="67dce-136">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="67dce-137">Метод CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="67dce-137">The CanWriteResult method</span></span>

<span data-ttu-id="67dce-138">В некоторых ситуациях следует переопределять метод `CanWriteResult`, а не `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="67dce-138">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="67dce-139">Используйте `CanWriteResult`, если выполняются указанные ниже условия.</span><span class="sxs-lookup"><span data-stu-id="67dce-139">Use `CanWriteResult` if the following conditions are true:</span></span>

  * <span data-ttu-id="67dce-140">Метод действия возвращает класс модели.</span><span class="sxs-lookup"><span data-stu-id="67dce-140">Your action method returns a model class.</span></span>
  * <span data-ttu-id="67dce-141">Существуют производные классы, которые могут возвращаться во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="67dce-141">There are derived classes which might be returned at runtime.</span></span>
  * <span data-ttu-id="67dce-142">Во время выполнения необходимо знать, какой производный класс был возвращен действием.</span><span class="sxs-lookup"><span data-stu-id="67dce-142">You need to know at runtime which derived class was returned by the action.</span></span>  

<span data-ttu-id="67dce-143">Предположим, сигнатура метода действия возвращает тип `Person`, но может также возвращать типы `Student` и `Instructor`, производные от `Person`.</span><span class="sxs-lookup"><span data-stu-id="67dce-143">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="67dce-144">Если модуль форматирования должен обрабатывать только объекты `Student`, проверьте тип свойства [Object](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) в объекте контекста, предоставленном методу `CanWriteResult`.</span><span class="sxs-lookup"><span data-stu-id="67dce-144">If you want your formatter to handle only `Student` objects, check the type of [Object](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="67dce-145">Обратите внимание на то, что если метод действия возвращает `IActionResult`, использовать `CanWriteResult` нет необходимости. В этом случае метод `CanWriteType` получает тип во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="67dce-145">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>
### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="67dce-146">Переопределение методов ReadRequestBodyAsync и WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="67dce-146">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span> 

<span data-ttu-id="67dce-147">Фактическая работа по десериализации и сериализации осуществляется в методе `ReadRequestBodyAsync` или `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="67dce-147">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span>  <span data-ttu-id="67dce-148">В выделенных строках ниже показано, как получить службы из контейнера внедрения зависимостей (получить их из параметров конструктора нельзя).</span><span class="sxs-lookup"><span data-stu-id="67dce-148">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="67dce-149">Настройка использования пользовательского модуля форматирования в MVC</span><span class="sxs-lookup"><span data-stu-id="67dce-149">How to configure MVC to use a custom formatter</span></span>
 
<span data-ttu-id="67dce-150">Для использования пользовательского модуля форматирования добавьте экземпляр его класса в коллекцию `InputFormatters` или `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="67dce-150">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="67dce-151">Модули форматирования обрабатываются в порядке добавления.</span><span class="sxs-lookup"><span data-stu-id="67dce-151">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="67dce-152">Первый модуль имеет приоритет.</span><span class="sxs-lookup"><span data-stu-id="67dce-152">The first one takes precedence.</span></span> 

## <a name="next-steps"></a><span data-ttu-id="67dce-153">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="67dce-153">Next steps</span></span>

<span data-ttu-id="67dce-154">Ознакомьтесь с [примером приложения](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), в котором реализуются простые модули форматирования входных и выходных данных в формате vCard.</span><span class="sxs-lookup"><span data-stu-id="67dce-154">See the [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span>  <span data-ttu-id="67dce-155">Это приложение считывает и записывает карточки vCard, которые выглядят следующим образом:</span><span class="sxs-lookup"><span data-stu-id="67dce-155">The application reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="67dce-156">Чтобы увидеть выходные данные vCard, запустите приложение и отправьте запрос Get с заголовком Accept "text/vcard" на адрес `http://localhost:63313/api/contacts/` (при запуске из Visual Studio) или `http://localhost:5000/api/contacts/` (при запуске из командной строки).</span><span class="sxs-lookup"><span data-stu-id="67dce-156">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="67dce-157">Чтобы добавить карточку vCard в коллекцию контактов в памяти, отправьте запрос Post на тот же URL-адрес. Запрос должен иметь заголовок Content-Type "text/vcard", а в его теле должен содержаться текст карточки vCard, отформатированный так, как показано выше.</span><span class="sxs-lookup"><span data-stu-id="67dce-157">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>