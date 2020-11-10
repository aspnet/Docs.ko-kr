---
title: Swagger/OpenAPI를 사용한 ASP.NET Core 웹 API 설명서
author: RicoSuter
description: 이 자습서에서는 Swagger를 추가하여 웹 API 앱에 대한 설명서 및 도움말 페이지를 생성하는 연습을 제공합니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062456"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="37742-103">Swagger/OpenAPI를 사용한 ASP.NET Core 웹 API 설명서</span><span class="sxs-lookup"><span data-stu-id="37742-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="37742-104">작성자: [Christoph Nienaber](https://twitter.com/zuckerthoben) 및 [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="37742-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="37742-105">Swagger(OpenAPI)는 REST API를 설명하는 언어 중립적 사양입니다.</span><span class="sxs-lookup"><span data-stu-id="37742-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="37742-106">이를 통해 소스 코드에 직접 액세스하지 않고도 컴퓨터와 사람이 REST API의 기능을 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37742-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="37742-107">주요 목표는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37742-107">Its main goals are to:</span></span>

* <span data-ttu-id="37742-108">분리된 서비스를 연결하는 데 필요한 작업량을 최소화합니다.</span><span class="sxs-lookup"><span data-stu-id="37742-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="37742-109">서비스를 정확하게 문서화하는 데 필요한 시간을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="37742-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="37742-110">.NET의 주요 OpenAPI 구현 두 가지는 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)과 [NSwag](https://github.com/RicoSuter/NSwag)입니다. 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37742-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="37742-111">Swashbuckle 시작</span><span class="sxs-lookup"><span data-stu-id="37742-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="37742-112">NSwag 시작</span><span class="sxs-lookup"><span data-stu-id="37742-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="37742-113">OpenAPI 대 Swagger</span><span class="sxs-lookup"><span data-stu-id="37742-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="37742-114">Swagger 프로젝트는 2015년에 OpenAPI 이니셔티브에 기부되었으며, 그 이후 OpenAPI라고 불립니다.</span><span class="sxs-lookup"><span data-stu-id="37742-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="37742-115">두 이름은 혼용되지만</span><span class="sxs-lookup"><span data-stu-id="37742-115">Both names are used interchangeably.</span></span> <span data-ttu-id="37742-116">"OpenAPI"는 사양을 가리키고,</span><span class="sxs-lookup"><span data-stu-id="37742-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="37742-117">"Swagger"는 OpenAPI 사양을 사용하는 오픈 소스 제품과 SmartBear의 상용 제품군을 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="37742-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="37742-118">[OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator)와 같은 후속 오픈 소스 제품도 SmartBear가 릴리스하지 않았지만 Swagger 제품군 이름에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="37742-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="37742-119">요약:</span><span class="sxs-lookup"><span data-stu-id="37742-119">In short:</span></span>

* <span data-ttu-id="37742-120">OpenAPI는 사양입니다.</span><span class="sxs-lookup"><span data-stu-id="37742-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="37742-121">Swagger는 OpenAPI 사양을 사용하는 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="37742-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="37742-122">OpenAPIGenerator와 SwaggerUI를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37742-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="37742-123">OpenAPI 사양(openapi.json)</span><span class="sxs-lookup"><span data-stu-id="37742-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="37742-124">OpenAPI 사양은 API의 기능을 설명하는 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="37742-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="37742-125">이 문서는 컨트롤러 및 모델 내의 XML 및 특성 주석을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="37742-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="37742-126">이 문서는 OpenAPI 흐름의 핵심 부분이며, SwaggerUI와 같은 도구를 구동하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="37742-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="37742-127">기본적으로 이 문서의 이름은 *openapi.json* 입니다.</span><span class="sxs-lookup"><span data-stu-id="37742-127">By default, it's named *openapi.json*.</span></span> <span data-ttu-id="37742-128">다음은 간략하게 축약된 OpenAPI 사양의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="37742-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a><span data-ttu-id="37742-129">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="37742-129">Swagger UI</span></span>

<span data-ttu-id="37742-130">[Swagger UI](https://swagger.io/swagger-ui/)는 생성된 OpenAPI 사양을 사용하여 서비스에 대한 정보를 제공하는 웹 기반 UI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="37742-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="37742-131">Swashbuckle과 NSwag에는 모두 임베디드 버전의 Swagger UI가 포함되어 있어, 미들웨어 등록 호출을 사용하여 ASP.NET Core 앱에서 호스팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37742-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="37742-132">웹 UI는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37742-132">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="37742-134">컨트롤러의 각 공용 작업 메서드는 UI에서 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37742-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="37742-135">메서드 이름을 선택하여 섹션을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="37742-135">Select a method name to expand the section.</span></span> <span data-ttu-id="37742-136">필요한 매개 변수를 모두 추가하고 **사용해 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="37742-136">Add any necessary parameters, and select **Try it out!**.</span></span>

![예제 Swagger GET 테스트](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="37742-138">스크린샷에 사용된 Swagger UI 버전은 버전 2입니다.</span><span class="sxs-lookup"><span data-stu-id="37742-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="37742-139">버전 3 예제는 [Petstore 예제](https://petstore.swagger.io/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37742-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="37742-140">다음 단계</span><span class="sxs-lookup"><span data-stu-id="37742-140">Next steps</span></span>

* [<span data-ttu-id="37742-141">Swashbuckle 시작</span><span class="sxs-lookup"><span data-stu-id="37742-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="37742-142">NSwag 시작</span><span class="sxs-lookup"><span data-stu-id="37742-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
