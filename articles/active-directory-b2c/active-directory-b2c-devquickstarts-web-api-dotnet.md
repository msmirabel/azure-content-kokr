<properties
	pageTitle="Azure Active Directory B2C 미리 보기 | Microsoft Azure"
	description="Azure Active Directory B2C를 사용하여 Web API를 호출하는 웹 응용 프로그램을 빌드하는 방법을 알아봅니다."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Azure AD B2C 미리 보기: .NET 웹앱에서 Web API 호출


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD(Azure Active Directory) B2C를 사용하여 몇 가지 간단한 단계로 강력한 셀프 서비스 ID 관리 기능을 웹앱 및 Web API에 추가할 수 있습니다. 이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 .NET Web API를 호출하는 .NET MVC(모델-뷰-컨트롤러) "할 일 모음" 웹앱을 만드는 방법을 보여 줍니다. 웹앱 및 Web API는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

이 문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다. 아직 준비되지 않은 경우 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)를 시작하여 Azure AD B2C의 기본 사항에 대해 알아봅니다.

## Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 아직 없는 경우 [B2C 디렉터리를 만든](active-directory-b2c-get-started.md) 후에 이 가이드를 계속 진행합니다.

## 응용 프로그램 만들기

다음으로 B2C 디렉터리에서 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 웹앱과 Web API 모두는 단일 **응용 프로그램 ID**에서 표현됩니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

- 응용 프로그램에서 **웹앱/Web API**를 포함합니다.
- **회신 URL**로 `https://localhost:44316/`을 입력합니다. 이 코드 샘플에 대한 기본 URL입니다.
- 응용 프로그램에 **응용 프로그램 암호**를 만들고 복사합니다. 이 시간은 나중에 필요합니다. 참고로 이 값은 사용하기 전에 [XML 이스케이프](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)되어야 합니다.
- 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 이 ID는 나중에도 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 웹앱은 등록, 로그인 및 편집 프로필 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

- 등록 정책에서 **표시 이름** 및 다른 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다. 이러한 정책 이름이 나중에 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보려면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)를 시작합니다.

## 코드 다운로드

이 자습서에 대한 코드는 [GitHub에서 유지 관리됩니다](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). 진행하면서 샘플을 빌드하기 위해 [골격 프로젝트를 .zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip)할 수 있습니다. 기본 구조를 복제할 수도 있습니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

완성된 앱도 [.zip 파일로 다운로드하거나](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.

샘플 코드를 다운로드한 후 Visual Studio .sln 파일을 열어 시작합니다. 솔루션에 `TaskWebApp` 프로젝트와 `TaskService` 프로젝트라는 두 개의 프로젝트가 있습니다. `TaskWebApp`은 사용자와 상호 작용하는 WPF(Windows Presentation Foundation) 웹앱 프런트 엔드이고, `TaskService`는 각 사용자의 할 일 모음을 저장하는 앱의 백 엔드 Web API입니다.

## 작업 서비스 구성

`TaskService`는 `TaskWebApp`의 요청을 받은 경우 유효한 액세스 토큰을 검사하여 요청을 인증합니다. 액세스 토큰의 유효성을 검사하려면 앱에 대한 정보를 `TaskService`에 제공해야 합니다. `TaskService` 프로젝트에서 프로젝트 루트에 있는 `web.config` 파일을 열고 `<appSettings>` 섹션의 값을 바꿉니다.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


이 문서에서는 `TaskService`의 보안을 유지하는 방법에 대한 세부 정보를 다루지 않습니다. Web API가 Azure AD B2C를 사용하여 요청을 안전하게 인증하는 방법을 알아보려면 [Web API 시작 문서](active-directory-b2c-devquickstarts-api-dotnet.md)를 확인하세요.

## 작업 웹앱 구성

`TaskWebApp`이 Azure AD B2C와 통신하도록 하려면 몇 가지 공통 매개 변수를 제공해야 합니다. `TaskWebApp` 프로젝트에서 프로젝트 루트에 있는 `web.config` 파일을 열고 `<appSettings>` 섹션의 값을 바꿉니다. 이러한 값은 웹앱 전체에서 사용됩니다.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

로그인 정책 이름을 제공하도록 요청하는 두 개의 `[PolicyAuthorize]` 데코레이터도 있습니다. 사용자가 인증이 필요한 앱의 페이지에 액세스하려고 하면 `[PolicyAuthorize]` 특성이 특정 정책을 호출하는 데 사용됩니다.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

## 액세스 토큰을 가져오고 작업 API를 호출합니다.

이 섹션은 Microsoft의 라이브러리 및 프레임워크를 사용하여 웹앱에서 OAuth 2.0 토큰 교환을 완료하는 방법을 보여줍니다. 인증 코드 및 액세스 토큰에 익숙하지 않은 경우 [OpenID Connect 프로토콜 참조](active-directory-b2c-reference-protocols.md)에서 자세히 알아볼 수 있습니다.

### 권한 부여 코드 가져오기

`TaskService` Web API를 호출하는 첫 번째 단계는 사용자를 인증하고 Azure AD에서 인증 코드를 받는 것입니다. 정책이 성공적으로 실행된 후에 Azure AD에서 인증 코드를 받을 수 있습니다. 여기에는 로그인, 등록 및 편집 프로필 정책이 포함됩니다.

시작하려면 Visual Studio 패키지 관리자 콘솔을 사용하여 OWIN OpenID Connect 미들웨어를 설치합니다. OWIN을 사용하여 Azure AD로 권한 부여 요청을 보내고 응답을 처리합니다.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

`App_Start\Startup.Auth.cs` 파일을 엽니다. OWIN 권한 부여 파이프라인을 여기서 구성하며 이는 B2C 디렉터리 및 만든 응용 프로그램의 세부 정보를 제공합니다.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
	public const string PolicyKey = "b2cpolicy";
	public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

	// App config settings
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

	// B2C policy identifiers
	public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
	public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
	public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

	public void ConfigureAuth(IAppBuilder app)
	{
		app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

		app.UseCookieAuthentication(new CookieAuthenticationOptions());

		OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
		{
			// These are standard OpenID Connect parameters, with values pulled from web.config
			ClientId = clientId,
			RedirectUri = redirectUri,
			PostLogoutRedirectUri = redirectUri,
			Notifications = new OpenIdConnectAuthenticationNotifications
			{
				AuthenticationFailed = OnAuthenticationFailed,
				RedirectToIdentityProvider = OnRedirectToIdentityProvider,
				AuthorizationCodeReceived = OnAuthorizationCodeReceived,
			},
			Scope = "openid offline_access",

			// The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
			// endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
			ConfigurationManager = new PolicyConfigurationManager(
				String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
				new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

			// This piece is optional - it is used for displaying the user's name in the navigation bar.
			TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
			{
				NameClaimType = "name",
			},
		};

		app.UseOpenIdConnectAuthentication(options);
	}
	...
}
```

### 인증 코드를 사용하여 액세스 토큰 가져오기

이제 웹앱은 B2C 디렉토리를 사용하여 사용자에게 권한을 부여하고 Azure AD에서 인증 코드를 다시 받도록 구성됩니다. 다음 단계는 Azure AD의 액세스 토큰에 대해 인증 코드를 교환합니다.

.NET 웹앱이 Azure AD에서 액세스 토큰을 가져와야 하는 경우 ADAL(Active Directory 인증 라이브러리)을 사용할 수 있습니다. 이 프로세스에 ADAL을 사용할 필요는 없지만 ADAL을 사용하면 많은 세부 사항이 자동으로 처리되어 작업이 보다 수월해집니다. 여기에는 OAuth 2.0 인증 메시지 보내기, 캐싱, 토큰 새로 고침 등이 포함됩니다.

먼저 패키지 관리자 콘솔을 사용하여 `TaskWebApp` 프로젝트에 ADAL을 설치합니다.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

그런 다음 토큰을 가져올 수 있도록 ADAL에 인증 코드를 전달해야 합니다. OWIN OpenID Connect 미들웨어에서 이 인증 코드를 사용할 수 있도록 알림을 제공합니다. 앱이 Azure AD에서 인증 코드를 받을 때마다 알림이 전송됩니다. `App_Start\Startup.Auth.cs`에서 ADAL을 사용하여 `OnAuthorizationCodeReceived` 알림 처리기를 구현합니다.

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and it is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default. In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
```

### 컨트롤러에서 액세스 토큰 가져오기

`TaskService` 백 엔드에 대한 액세스 토큰을 가져와 ADAL의 토큰 캐시에 저장한 후에는 이를 사용해야 합니다. `TasksController`는 `TaskService` API와의 통신을 담당하며, 작업을 읽고, 만들고, 삭제하기 위한 HTTP 요청을 API로 보냅니다. HTTP 요청을 보내기 전에 ADAL에서 액세스 토큰을 가져옵니다.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	AuthenticationResult result = null;
	try
	{
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

		// We don't care which policy is used to access the TaskService, so let's use the most recent policy
		string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;

		// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache and throw an exception if it cannot do so.
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

		...
	}
	catch (AdalException ee)
	{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
	}
	...
}
```

ADAL은 토큰을 캐시하고 토큰이 만료되면 새로 고치며 예외를 throw하여 사용자가 다시 로그인해야 하는 시기를 알려 줍니다. 앱에 토큰이 필요할 때마다 `AuthenticationContext.AcquireTokenSilentAsync(...)`를 호출하기만 하면 됩니다.

### Web API로부터 작업 읽기

이제 토큰이 있으므로 `Authorization` 헤더에서 HTTP `GET` 요청에 토큰을 연결하여 `TaskService`를 안전하게 호출할 수 있습니다.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	...

	try
	{
		HttpClient client = new HttpClient();
		HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

		// Add the token acquired from ADAL to the request headers
		request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
		HttpResponseMessage response = await client.SendAsync(request);

		if (response.IsSuccessStatusCode)
		{
			String responseString = await response.Content.ReadAsStringAsync();
			JArray tasks = JArray.Parse(responseString);
			ViewBag.Tasks = tasks;
			return View();
		}
		else
		{
			// If the call failed with access denied, then drop the current access token from the cache,
			// and show the user an error that indicates that they might need to sign in again.
			if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
			{
				var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
				foreach (TokenCacheItem tci in todoTokens)
					authContext.TokenCache.DeleteItem(tci);

				return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
			}
		}

		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
	}
	catch (Exception ex)
	{
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
	}
}

```

### Web API에서 작업 만들기 및 삭제

`POST` 및 `DELETE` 요청을 `TaskService`로 보낼 때와 동일한 패턴을 따릅니다. `AuthenticationContext.AcquireTokenSilentAsync(...)`를 호출하고 `Authorization` 헤더에서 결과 토큰을 요청에 연결합니다. 만들기 작업은 구현되어 있습니다. `TasksController.cs`에서 삭제 작업을 완료해 볼 수 있습니다.

## 사용자 로그아웃

웹앱에서 사용자가 로그아웃하면 ADAL 토큰 캐시의 선택을 취소하여 사용자의 인증된 세션의 나머지 부분을 제거합니다.

```C#
// Controllers/AccountController.cs

public void SignOut()
{
	if (Request.IsAuthenticated)
	{
		// When the user signs out, clear their token cache in the process
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		authContext.TokenCache.Clear();

		HttpContext.GetOwinContext().Authentication.SignOut(
		new AuthenticationProperties(
			new Dictionary<string, string>
			{
				{Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
			}), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
	}
}
```

## 샘플 앱 실행

마지막으로 `TaskClient`과 `TaskService`를 모두 빌드하고 실행합니다. 앱 등록 및 로그인 로그인한 사용자에 대한 작업을 만듭니다. 로그아웃했다가 다른 사용자로 로그인합니다. 해당 사용자에 대한 작업을 만듭니다. API 가 받는 액세스 토큰에서 사용자의 ID를 추출하므로 API에 사용자별 작업이 저장됩니다.

참조를 위해 완료된 샘플은 [.zip 파일로 제공](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)됩니다. 또한 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->