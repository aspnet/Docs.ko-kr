---
title: ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드 생성 사용
author: rick-anderson
description: ASP.NET Core 2 단계 인증을 사용 하는 TOTP authenticator 앱에 대 한 QR 코드 생성을 사용 하도록 설정 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b778e7238911ec9966edf7f0f7becd113b1e197a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060835"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="0a07c-103">ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드 생성 사용</span><span class="sxs-lookup"><span data-stu-id="0a07c-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0a07c-104">QR 코드에 ASP.NET Core 2.0 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0a07c-105">ASP.NET Core는 개별 인증에 대 한 인증자 응용 프로그램 지원과 함께 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="0a07c-106">시간 기반 일회용 암호 알고리즘 (TOTP)을 사용 하는 2 단계 인증 (2FA) authenticator 앱은 2FA의 업계 권장 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="0a07c-107">2FA는 TOTP를 사용 하 여 SMS 2FA에 선호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="0a07c-108">Authenticator 앱은 사용자 이름과 암호를 확인 한 후 사용자가 입력 해야 하는 6 ~ 8 자리 코드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="0a07c-109">일반적으로 인증자 앱은 스마트폰에 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="0a07c-110">ASP.NET Core 웹 앱 템플릿은 인증자을 지원 하지만 QRCode 생성에 대 한 지원을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="0a07c-111">QRCode 생성기는 2FA의 설치를 용이 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="0a07c-112">이 문서에서는 2FA 구성 페이지에 [QR 코드](https://wikipedia.org/wiki/QR_code) 생성을 추가 하는 과정을 안내 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="0a07c-113">[Google](xref:security/authentication/google-logins) 또는 [Facebook](xref:security/authentication/facebook-logins)과 같은 외부 인증 공급자를 사용 하 여 2 단계 인증을 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="0a07c-114">외부 로그인은 외부 로그인 공급자가 제공 하는 모든 메커니즘에 의해 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="0a07c-115">예를 들어 [Microsoft](xref:security/authentication/microsoft-logins) 인증 공급자에는 하드웨어 키 또는 다른 2fa 방법이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="0a07c-116">기본 템플릿이 "local" 2FA를 적용 한 경우 사용자는 일반적으로 사용 되는 시나리오가 아닌 2 개의 2 개의 FA 접근 방법을 충족 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="0a07c-117">2FA 구성 페이지에 QR 코드 추가</span><span class="sxs-lookup"><span data-stu-id="0a07c-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="0a07c-118">이러한 지침은 리포지토리의 *qrcode.js* 를 사용 https://davidshimjs.github.io/qrcodejs/ 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="0a07c-119">프로젝트의 폴더에 [qrcode.js javascript 라이브러리](https://davidshimjs.github.io/qrcodejs/) 를 다운로드 합니다 `wwwroot\lib` .</span><span class="sxs-lookup"><span data-stu-id="0a07c-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="0a07c-120">[스 캐 폴드 Identity](xref:security/authentication/scaffold-identity) 의 지침에 따라 */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml* 을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml* .</span></span>
* <span data-ttu-id="0a07c-121">*/Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml* 에서 파일 끝에 있는 섹션을 찾습니다 `Scripts` .</span><span class="sxs-lookup"><span data-stu-id="0a07c-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml* , locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="0a07c-122">*페이지/계정/관리/* o s s. cshtml ( Razor 페이지) 또는 *Views/manage/enableauthenticator* (MVC)에서 파일의 끝 부분에 있는 섹션을 찾습니다 `Scripts` .</span><span class="sxs-lookup"><span data-stu-id="0a07c-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="0a07c-123">섹션을 업데이트 `Scripts` 하 여 추가 된 라이브러리에 대 `qrcodejs` 한 참조 및 QR 코드를 생성 하는 호출을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="0a07c-124">다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="0a07c-125">이러한 지침에 연결 되는 단락을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="0a07c-126">앱을 실행 하 고 QR 코드를 스캔 하 여 인증자가 증명 한 코드의 유효성을 검사할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="0a07c-127">QR 코드에서 사이트 이름 변경</span><span class="sxs-lookup"><span data-stu-id="0a07c-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0a07c-128">QR 코드의 사이트 이름은 처음 프로젝트를 만들 때 선택한 프로젝트 이름에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="0a07c-129">`GenerateQrCodeUri(string email, string unformattedKey)` */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml.cs* 에서 메서드를 검색 하 여 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs* .</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0a07c-130">QR 코드의 사이트 이름은 처음 프로젝트를 만들 때 선택한 프로젝트 이름에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="0a07c-131">`GenerateQrCodeUri(string email, string unformattedKey)` *Pages/Account/Manage/enableauthenticator. cshtml* ( Razor Pages) 파일 또는 *Controllers/ManageController* (MVC) 파일에서 메서드를 검색 하 여이를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0a07c-132">템플릿의 기본 코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="0a07c-133">호출에서 두 번째 매개 변수는 `string.Format` 솔루션 이름에서 가져온 사이트 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="0a07c-134">모든 값으로 변경할 수 있지만 항상 URL로 인코딩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="0a07c-135">다른 QR 코드 라이브러리 사용</span><span class="sxs-lookup"><span data-stu-id="0a07c-135">Using a different QR Code library</span></span>

<span data-ttu-id="0a07c-136">QR 코드 라이브러리를 원하는 라이브러리로 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="0a07c-137">HTML에는 라이브러리에서 제공 하는 `qrCode` 메커니즘에 따라 QR 코드를 넣을 수 있는 요소가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="0a07c-138">QR 코드에 대해 올바른 형식의 URL은에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="0a07c-139">`AuthenticatorUri` 모델의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="0a07c-140">`data-url``qrCodeData`요소의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="0a07c-141">TOTP 클라이언트 및 서버 시간 오차</span><span class="sxs-lookup"><span data-stu-id="0a07c-141">TOTP client and server time skew</span></span>

<span data-ttu-id="0a07c-142">TOTP (시간 기반 One-Time 암호) 인증은 서버 및 인증자 장치에 정확한 시간이 있는 경우에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="0a07c-143">토큰은 30 초 동안만 지속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="0a07c-144">TOTP 2FA 로그인이 실패 하는 경우 서버 시간이 정확 하 고 정확한 NTP 서비스와 동기화 될 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a07c-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
