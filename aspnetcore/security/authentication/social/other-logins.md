---
title: 외부 OAuth 인증 공급자
author: rick-anderson
description: ASP.NET Core 앱에서 작동 하는 외부 OAuth 인증 공급자를 검색 합니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/otherlogins
ms.openlocfilehash: 11f770b752a6654ffe73b4fe005c09711515ac74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053269"
---
# <a name="external-oauth-authentication-providers"></a><span data-ttu-id="4c431-103">외부 OAuth 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="4c431-103">External OAuth authentication providers</span></span>

<span data-ttu-id="4c431-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd)및 [valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="4c431-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="4c431-105">다음 목록에는 ASP.NET Core 앱에서 작동 하는 일반적인 외부 OAuth 인증 공급자가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4c431-105">The following list includes common external OAuth authentication providers that work with ASP.NET Core apps.</span></span> <span data-ttu-id="4c431-106">[Aspnet-로 리브](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth)에서 유지 관리 되는 패키지와 같은 타사 NuGet 패키지를 사용 하 여 ASP.NET Core 팀에서 구현 하는 인증 공급자를 보완할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4c431-106">Third-party NuGet packages, such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), can be used to complement the authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="4c431-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([명령](https://developer.linkedin.com/docs/oauth2))</span><span class="sxs-lookup"><span data-stu-id="4c431-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))</span></span>

* <span data-ttu-id="4c431-108">[Instagram](https://www.instagram.com/developer/register/) [명령 (명령](https://www.instagram.com/developer/authentication/))</span><span class="sxs-lookup"><span data-stu-id="4c431-108">[Instagram](https://www.instagram.com/developer/register/) ([Instructions](https://www.instagram.com/developer/authentication/))</span></span>

* <span data-ttu-id="4c431-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([지침](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span><span class="sxs-lookup"><span data-stu-id="4c431-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span></span>

* <span data-ttu-id="4c431-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([지침](https://developer.github.com/v3/oauth/))</span><span class="sxs-lookup"><span data-stu-id="4c431-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Instructions](https://developer.github.com/v3/oauth/))</span></span>

* <span data-ttu-id="4c431-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([지침](https://developer.yahoo.com/bbauth/user.html))</span><span class="sxs-lookup"><span data-stu-id="4c431-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Instructions](https://developer.yahoo.com/bbauth/user.html))</span></span>

* <span data-ttu-id="4c431-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([지침](https://www.tumblr.com/docs/api/v2#auth))</span><span class="sxs-lookup"><span data-stu-id="4c431-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Instructions](https://www.tumblr.com/docs/api/v2#auth))</span></span>

* <span data-ttu-id="4c431-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([지침](https://developers.pinterest.com/docs/api/overview/?))</span><span class="sxs-lookup"><span data-stu-id="4c431-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Instructions](https://developers.pinterest.com/docs/api/overview/?))</span></span>

* <span data-ttu-id="4c431-114">[포켓](https://getpocket.com/developer/apps/new) ([명령](https://getpocket.com/developer/docs/authentication))</span><span class="sxs-lookup"><span data-stu-id="4c431-114">[Pocket](https://getpocket.com/developer/apps/new) ([Instructions](https://getpocket.com/developer/docs/authentication))</span></span>

* <span data-ttu-id="4c431-115">[Flickr](https://www.flickr.com/services/apps/create) ([명령](https://www.flickr.com/services/api/auth.oauth.html))</span><span class="sxs-lookup"><span data-stu-id="4c431-115">[Flickr](https://www.flickr.com/services/apps/create) ([Instructions](https://www.flickr.com/services/api/auth.oauth.html))</span></span>

* <span data-ttu-id="4c431-116">[Dribble](https://dribbble.com/signup) ([지침](https://developer.dribbble.com/v1/oauth/))</span><span class="sxs-lookup"><span data-stu-id="4c431-116">[Dribble](https://dribbble.com/signup) ([Instructions](https://developer.dribbble.com/v1/oauth/))</span></span>

* <span data-ttu-id="4c431-117">[Vimeo](https://vimeo.com/join) ([지침](https://developer.vimeo.com/api/authentication))</span><span class="sxs-lookup"><span data-stu-id="4c431-117">[Vimeo](https://vimeo.com/join) ([Instructions](https://developer.vimeo.com/api/authentication))</span></span>

* <span data-ttu-id="4c431-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([지침](https://developers.soundcloud.com/blog/we-love-oauth-2))</span><span class="sxs-lookup"><span data-stu-id="4c431-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))</span></span>

* <span data-ttu-id="4c431-119">[Vk](https://vk.com/apps?act=manage) ([명령](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span><span class="sxs-lookup"><span data-stu-id="4c431-119">[VK](https://vk.com/apps?act=manage) ([Instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span></span>

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
