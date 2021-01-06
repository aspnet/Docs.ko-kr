* <span data-ttu-id="a879c-101">다음 명령을 실행하여 HTTPS 개발 인증서를 신뢰합니다.</span><span class="sxs-lookup"><span data-stu-id="a879c-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="a879c-102">앞의 명령은 Linux에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a879c-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="a879c-103">인증서 신뢰에 대해서는 Linux 배포 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a879c-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="a879c-104">이전 명령으로 인해 다음 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a879c-104">The preceding command displays the following dialog:</span></span>

  ![보안 경고 대화 상자](~/getting-started/_static/cert.png)

* <span data-ttu-id="a879c-106">개발 인증서를 신뢰하는 데 동의하는 경우 **예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a879c-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="a879c-107">자세한 내용은 [ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a879c-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]