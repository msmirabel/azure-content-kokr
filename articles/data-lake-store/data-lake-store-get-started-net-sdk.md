<properties
   pageTitle="데이터 레이크 저장소 .NET SDK를 사용하여 응용 프로그램 개발 | Azure"
   description="Azure 데이터 레이크 저장소 .NET SDK를 사용하여 응용 프로그램 개발"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="nitinme"/>

# .NET SDK를 사용하여 Azure 데이터 레이크 저장소 시작

> [AZURE.SELECTOR]
- [포털 사용](data-lake-store-get-started-portal.md)
- [PowerShell 사용](data-lake-store-get-started-powershell.md)
- [.NET SDK 사용](data-lake-store-get-started-net-sdk.md)
- [Azure CLI 사용](data-lake-store-get-started-cli.md)
- [Node.js 사용](data-lake-store-manage-use-nodejs.md)

Azure 데이터 레이크 저장소 .NET SDK를 사용하여 Azure 데이터 레이크 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. 데이터 레이크에 대한 자세한 내용은 [Azure 데이터 레이크 저장소](data-lake-store-overview.md)를 참조하세요.

## 필수 조건

* Visual Studio 2013 또는 2015 아래 지침에서는 Visual Studio 2015를 사용합니다.
* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 데이터 레이크 저장소 공개 미리 보기를 위해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
* AAD(Azure Active Directory) 응용 프로그램을 만들고 **클라이언트 ID** 및 **회신 URI**를 검색합니다. AAD 응용 프로그램에 대한 자세한 내용 및 클라이언트 ID를 가져오는 방법에 대한 지침은 [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](../resource-group-create-service-principal-portal.md)를 참조하세요. 응용 프로그램을 만들었으면 회신 URI도 포털에서 사용할 수 있습니다.

## Azure Active Directory를 사용하여 인증하려면 어떻게 해야 하나요?

아래 코드 조각에서는 두 가지 인증 메서드를 제공합니다.

* **대화형** - 응용 프로그램을 사용하여 사용자가 로그인합니다. 아래 코드 조각의 메서드 `AuthenticateUser`에서 구현됩니다.

* **비대화형** - 응용 프로그램이 자체 자격 증명을 제공합니다. 아래 코드 조각의 메서드 `AuthenticateAppliaction`에서 구현됩니다.

아래 코드 조각에서는 두 접근 방법에 대한 메서드를 모두 제공하지만 이 문서에서는 `AuthenticateUser` 메서드를 사용합니다. 이 메서드를 사용하려면 AAD 응용 프로그램 클라이언트 ID 및 회신 URI를 제공해야 합니다. 필수 조건에 있는 링크에서 이들을 가져오는 방법에 대한 지침을 제공합니다.

>[AZURE.NOTE] 코드 조각을 수정하고 `AuthenticateApplication` 메서드를 대신 사용하려는 경우 클라이언트 ID 및 클라이언트 회신 URI 외에도 클라이언트 인증 키를 메서드에 대한 입력 항목으로 제공해야 합니다. [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](../resource-group-create-service-principal-portal.md) 문서에서는 클라이언트 인증 키를 생성하고 검색하는 방법에 대한 지침도 제공합니다.



## .NET 응용 프로그램 만들기

1. Visual Studio를 열고 콘솔 응용 프로그램을 만듭니다.

2. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

3. **새 프로젝트**에서 다음 값을 입력하거나 선택합니다.

	| 속성 | 값 |
	|----------|-----------------------------|
	| Category | Templates/Visual C#/Windows |
	| Template | Console Application |
	| 이름 | CreateADLApplication |

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. Nuget 패키지를 프로젝트에 추가합니다.

	1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
	2. **Nuget 패키지 관리자** 탭에서 **패키지 원본**이 **nuget.org**로 설정되어 있고 **Include Prerelease**(시험판 포함) 확인란이 선택되어 있는지 확인합니다.
	3. 다음 데이터 레이크 저장소 패키지를 검색하고 설치합니다.

		* `Microsoft.Azure.Management.DataLake.Store`
		* `Microsoft.Azure.Management.DataLake.StoreUploader`

		![Nuget 원본 추가](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "새 Azure 데이터 레이크 계정 만들기")

	4. 또한 Azure Active Directory 인증에 대한 `Microsoft.IdentityModel.Clients.ActiveDirectory` 패키지를 설치합니다. 이 패키지의 안정적인 버전을 설치하도록 **시험판 포함** 확인란을 *선택 취소*합니다.

		![Nuget 원본 추가](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "새 Azure 데이터 레이크 계정 만들기")


	5. **NuGet 패키지 관리자**를 닫습니다.

7. **Program.cs**를 열고 기존 코드 블록을 다음 코드로 바꿉니다. 또한 **\_adlsAccountName**, **\_resourceGroupName** 등의 코드 조각에서 호출되는 매개 변수의 값을 제공하고 **APPLICATION-CLIENT-ID**, **APPLICATION-REPLY-URI** 및 **SUBSCRIPTION-ID**의 자리 표시자를 바꿉니다.

	이 코드는 데이터 레이크 저장소 계정 만들기, 저장소에서 폴더 만들기, 파일 업로드, 파일 다운로드, 마지막으로 계정을 삭제하는 프로세스를 진행합니다. 업로드할 일부 샘플 데이터를 찾는 경우 [Azure 데이터 레이크 Git 리포지토리](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)의 **Ambulance Data** 폴더에 있을 수 있습니다.

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = @"C:\local_path\file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";

                    // Authenticate the user
                    var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                        "<APPLICATION-CLIENT-ID>", new Uri("https://<APPLICATION-REPLY-URI>")); // TODO: Replace bracketed values.

                    SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.

                    // Run sample scenarios
                    WaitForNewline("Authenticated.", "Creating NEW account.");
                    CreateAccount();
                    WaitForNewline("Account created.", "Creating a directory.");

                    // Create a directory in the Data Lake Store
                    CreateDirectory(remoteFolderPath);
                    WaitForNewline("Directory created.", "Showing directory info.");

                    // Get info about the directory in the Data Lake Store
                    var itemInfo = GetItemInfo(remoteFolderPath);
                    Console.WriteLine("Type: " + itemInfo.Type);
                    Console.WriteLine("Last modified (UTC): " +
                                      new DateTime(1970, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(
                                          itemInfo.ModificationTime.Value));
                    WaitForNewline("Directory info shown.", "Uploading a file.");

                    // Upload a file to the Data Lake Store
                    UploadFile(localFilePath, remoteFilePath);
                    WaitForNewline("File uploaded.", "Listing files and directories.");

                    // List the files in the Data Lake Store
                    var itemList = ListItems(remoteFolderPath);
                    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
                    Console.WriteLine(String.Join("\r\n", fileMenuItems));
                    WaitForNewline("Files and directories listed.", "Appending content to a file.");

                    // Append to a file in the Data Lake Store
                    AppendToFile(remoteFilePath, "123");
                    WaitForNewline("Content appended.", "Downloading a file.");

                    // Download a file from the Data Lake Store
                    DownloadFile(remoteFilePath, localFilePath);
                    WaitForNewline("File downloaded.", "Deleting account.");

                    // Delete account
                    DeleteAccount();
                    WaitForNewline("Account deleted. You can now exit.");
                }

                // Helper function to show status and wait for user input
                public static void WaitForNewline(string reason, string nextAction = "")
                {
                    if (!String.IsNullOrWhiteSpace(nextAction))
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                        Console.WriteLine(nextAction);
                    }
                    else
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                    }
                }

                // Authenticate the user with AAD through an interactive popup.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see:
                //   https://azure.microsoft.com/ko-KR/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                    var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                        PromptBehavior.Auto, UserIdentifier.AnyUser);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                // Authenticate the application with AAD through the application's secret key.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see:
                //   https://azure.microsoft.com/ko-KR/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                    var credential = new ClientCredential(appClientId, clientSecret);

                    var tokenAuthResult = authContext.AcquireToken(resource, credential);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                //Set up clients
                public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
                {
                    _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                    _adlsClient.SubscriptionId = subscriptionId;

                    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
                    _adlsFileSystemClient.SubscriptionId = subscriptionId;
                }

                // Create account
                public static void CreateAccount()
                {
                    // Create ADLS account
                    var adlsParameters = new DataLakeStoreAccount(location: _location);
                    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
                }

                // Delete account
                public static void DeleteAccount()
                {
                    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
                }

                // List all ADLS accounts within the subscription
                public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
                {
                    var response = _adlsClient.Account.List(_adlsAccountName);
                    var accounts = new List<DataLakeStoreAccount>(response);

                    while (response.NextPageLink != null)
                    {
                        response = _adlsClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // Upload a file
                public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
                {
                    var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                    var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                    var uploader = new DataLakeStoreUploader(parameters, frontend);
                    uploader.Execute();
                }

                // Concatenate files
                public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
                {
                    _adlsFileSystemClient.FileSystem.Concat(destFilePath, srcFilePaths, _adlsAccountName);
                }

                // Get file or directory info
                public static FileStatusProperties GetItemInfo(string path)
                {
                    return _adlsFileSystemClient.FileSystem.GetFileStatus(path, _adlsAccountName).FileStatus;
                }

                // List files and directories
                public static List<FileStatusProperties> ListItems(string directoryPath)
                {
                    return _adlsFileSystemClient.FileSystem.ListFileStatus(directoryPath, _adlsAccountName).FileStatuses.FileStatus.ToList();
                }

                // Download file
                public static void DownloadFile(string srcPath, string destPath)
                {
                    var stream = _adlsFileSystemClient.FileSystem.Open(srcPath, _adlsAccountName);
                    var fileStream = new FileStream(destPath, FileMode.Create);

                    stream.CopyTo(fileStream);
                    fileStream.Close();
                    stream.Close();
                }

                // Append to file
                public static void AppendToFile(string path, string content)
                {
                    var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

                    _adlsFileSystemClient.FileSystem.Append(path, stream, _adlsAccountName);
                }

                // Create a directory
                public static void CreateDirectory(string path)
                {
                    _adlsFileSystemClient.FileSystem.Mkdirs(path, _adlsAccountName);
                }
            }
        }


8. 응용 프로그램을 빌드 및 실행합니다. 프롬프트에 따라 응용 프로그램을 실행하고 완료합니다.

## 데이터 레이크 저장소 계정을 만드는 다른 방법

- [포털을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)
- [PowerShell을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-powershell.md)
- [Azure CLI를 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-cli.md)


## 다음 단계

- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

<!----HONumber=AcomDC_0309_2016-->