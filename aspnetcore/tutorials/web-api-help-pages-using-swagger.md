---
title: Swagger/OpenAPI를 사용한 ASP.NET Core 웹 API 설명서
author: RicoSuter
description: 이 자습서에서는 Swagger를 추가하여 웹 API 앱에 대한 설명서 및 도움말 페이지를 생성하는 연습을 제공합니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93062456"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>Swagger/OpenAPI를 사용한 ASP.NET Core 웹 API 설명서

작성자: [Christoph Nienaber](https://twitter.com/zuckerthoben) 및 [Rico Suter](https://blog.rsuter.com/)

Swagger(OpenAPI)는 REST API를 설명하는 언어 중립적 사양입니다. 이를 통해 소스 코드에 직접 액세스하지 않고도 컴퓨터와 사람이 REST API의 기능을 이해할 수 있습니다. 주요 목표는 다음과 같습니다.

* 분리된 서비스를 연결하는 데 필요한 작업량을 최소화합니다.
* 서비스를 정확하게 문서화하는 데 필요한 시간을 줄입니다.

.NET의 주요 OpenAPI 구현 두 가지는 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)과 [NSwag](https://github.com/RicoSuter/NSwag)입니다. 다음을 참조하세요.

* [Swashbuckle 시작](xref:tutorials/get-started-with-swashbuckle)
* [NSwag 시작](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenAPI 대 Swagger

Swagger 프로젝트는 2015년에 OpenAPI 이니셔티브에 기부되었으며, 그 이후 OpenAPI라고 불립니다. 두 이름은 혼용되지만 "OpenAPI"는 사양을 가리키고, "Swagger"는 OpenAPI 사양을 사용하는 오픈 소스 제품과 SmartBear의 상용 제품군을 가리킵니다. [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator)와 같은 후속 오픈 소스 제품도 SmartBear가 릴리스하지 않았지만 Swagger 제품군 이름에 속합니다.

요약:

* OpenAPI는 사양입니다.
* Swagger는 OpenAPI 사양을 사용하는 도구입니다. OpenAPIGenerator와 SwaggerUI를 예로 들 수 있습니다.

## <a name="openapi-specification-openapijson"></a>OpenAPI 사양(openapi.json)

OpenAPI 사양은 API의 기능을 설명하는 문서입니다. 이 문서는 컨트롤러 및 모델 내의 XML 및 특성 주석을 기반으로 합니다. 이 문서는 OpenAPI 흐름의 핵심 부분이며, SwaggerUI와 같은 도구를 구동하는 데 사용됩니다. 기본적으로 이 문서의 이름은 *openapi.json* 입니다. 다음은 간략하게 축약된 OpenAPI 사양의 예입니다.

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

## <a name="swagger-ui"></a>Swagger UI

[Swagger UI](https://swagger.io/swagger-ui/)는 생성된 OpenAPI 사양을 사용하여 서비스에 대한 정보를 제공하는 웹 기반 UI를 제공합니다. Swashbuckle과 NSwag에는 모두 임베디드 버전의 Swagger UI가 포함되어 있어, 미들웨어 등록 호출을 사용하여 ASP.NET Core 앱에서 호스팅할 수 있습니다. 웹 UI는 다음과 같습니다.

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

컨트롤러의 각 공용 작업 메서드는 UI에서 테스트할 수 있습니다. 메서드 이름을 선택하여 섹션을 확장합니다. 필요한 매개 변수를 모두 추가하고 **사용해 보기** 를 선택합니다.

![예제 Swagger GET 테스트](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> 스크린샷에 사용된 Swagger UI 버전은 버전 2입니다. 버전 3 예제는 [Petstore 예제](https://petstore.swagger.io/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Swashbuckle 시작](xref:tutorials/get-started-with-swashbuckle)
* [NSwag 시작](xref:tutorials/get-started-with-nswag)
