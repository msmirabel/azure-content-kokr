<properties
	pageTitle="Azure Data Factory 파이프라인에서 사용자 지정 작업 사용"
	description="사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 사용하는 방법에 대해 알아봅니다."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="spelluru"/>

# Azure Data Factory 파이프라인에서 사용자 지정 작업 사용
Azure Data Factory는 파이프라인에서 데이터를 이동 및 처리하는 데 사용되는 **복사 작업** 및 **HDInsight 작업**과 같은 기본 제공 작업을 지원합니다. 고유한 변환/처리 논리를 포함하는 사용자 지정 .NET 작업을 만들어 파이프라인에서 사용할 수도 있습니다. **Azure HDInsight** 클러스터 또는 **Azure 배치** 서비스를 사용하여 실행되도록 작업을 구성할 수 있습니다.

이 문서에서는 사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 사용하는 방법을 설명합니다. 사용자 지정 작업을 만들고 사용하기 위한 단계별 지침이 포함된 자세한 연습도 제공합니다. 이 연습에서는 HDInsight 연결된 서비스를 사용합니다. Azure Batch 연결된 서비스를 대신 사용하려면 **AzureBatch** 형식의 연결된 서비스를 만들고 파이프라인 JSON(**linkedServiceName**)의 작업 섹션에서 사용합니다. 사용자 지정 작업과 함께 Azure 배치를 사용하는 방법에 대한 자세한 내용은 [Azure 배치 연결된 서비스](#AzureBatch) 섹션을 참조하세요.


## <a name="walkthrough" /> 연습
이 연습에서는 사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 작업을 사용하는 단계별 지침을 제공합니다. 이 연습은 [Azure Data Factory 시작][adfgetstarted] 자습서에서 이어지는 내용입니다. 사용자 지정 작업의 작동을 확인하려면 먼저 시작 자습서를 살펴본 다음 이 연습을 수행해야 합니다.

### 필수 조건


- [Azure Data Factory 시작][adfgetstarted] 자습서. 이 연습을 수행하기 전에 이 문서에 나온 자습서를 완료해야 합니다.
- Visual Studio 2012 또는 2013
- [Azure .NET SDK][azure-developer-center] 다운로드 및 설치
- 최신 [Azure Data Factory용 NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/)(영문)를 다운로드하여 설치합니다. 지침은 연습에 있습니다.
- Azure 저장소용 NuGet 패키지 다운로드 및 설치. 지침이 이 연습에 나오므로 이 단계는 건너뛰어도 됩니다.

### 대략적인 단계 
1.	데이터 팩터리 솔루션에서 사용할 **사용자 지정 작업 만들기**. 사용자 지정 작업에는 데이터 처리 논리가 포함됩니다. 
	1.	Visual Studio(또는 원하는 코드 편집기)에서 .NET 클래스 라이브러리 프로젝트를 만들고 입력 데이터를 처리할 코드를 추가하고 프로젝트를 컴파일합니다.	
	2.	출력 폴더에서 모든 이진 파일과 PDB(선택 사항) 파일을 압축합니다.	
	3.	Azure Blob 저장소에 zip 파일을 업로드합니다. 자세한 단계는 사용자 지정 작업 만들기 섹션에 나와 있습니다. 
2. **사용자 지정 작업을 사용하는 Azure 데이터 팩터리 만들기**
	1. Azure 데이터 팩터리를 만듭니다.
	2. 연결된 서비스 만들기.
		1. StorageLinkedService: BLOB에 액세스하기 위한 저장소 자격 증명을 제공합니다.
		2. HDInsightLinkedService: Azure HDInsight를 계산으로 지정합니다.
	3. 데이터 집합을 만듭니다.
		1. InputDataset: 입력 BLOB에 대한 저장소 컨테이너 및 폴더를 지정합니다.
		1. OuputDataset: 출력 BLOB에 대한 저장소 컨테이너 및 폴더를 지정합니다.
	2. 사용자 지정 작업을 사용하는 파이프라인을 만듭니다.
	3. 파이프라인을 실행하고 테스트합니다.
	4. 파이프라인을 디버깅합니다.

## 사용자 지정 작업 만들기
Azure Data Factory 파이프라인에서 사용할 .NET 사용자 지정 작업을 만들려면 **IDotNetActivity** 인터페이스를 구현하는 클래스와 함께 **.NET 클래스 라이브러리** 프로젝트를 만들어야 합니다. 이 인터페이스는 Execute라는 하나의 메서드만 포함합니다. 해당 메서드의 서명은 다음과 같습니다.

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        
이 메서드에는 이해해야 하는 몇 가지 주요 구성 요소가 포함됩니다.

- 이 메서드는 다음과 같은 네 개의 매개 변수를 사용합니다.
	- **linkedServices**. 입/출력 데이터 원본(예: Azure Blob 저장소)을 데이터 팩터리에 연결하는 연결된 서비스의 열거형 목록입니다. 이 샘플에서는 입력 및 출력 모두에 사용되는 Azure 저장소 형식의 연결된 서비스가 하나만 있습니다. 
	- **datasets**. 데이터 집합의 열거형 목록입니다. 이 매개 변수를 사용하여 입력 및 출력 데이터 집합에 정의된 위치 및 스키마를 가져올 수 있습니다.
	- **activity**. 이 매개 변수는 현재 계산 엔터티를 나타냅니다(이 경우, Azure HDInsight).
	- **logger**. 로거를 사용하면 파이프라인에 대한 "User" 로그로 노출할 디버그 주석을 기록할 수 있습니다. 

- 이 메서드는 사용자 지정 작업을 함께 연결하는 데 사용할 수 있는 사전을 반환합니다. 이 샘플 솔루션에서는 이 기능을 사용하지 않습니다.

### 절차: 
1.	.NET 클래스 라이브러리 프로젝트를 만듭니다.
	<ol type="a">
	<li><b>Visual Studio 2012</b> 또는 <b>Visual Studio 2013</b>을 시작합니다.</li>
	<li><b>파일</b>을 클릭하고 <b>새로 만들기</b>를 가리킨 다음 <b>프로젝트</b>를 클릭합니다.</li>
	<li><b>템플릿</b>을 확장하고 <b>Visual C#</b>을 선택합니다. 이 연습에서는 C#을 사용하지만 다른 .NET 언어를 사용하여 사용자 지정 작업을 개발할 수도 있습니다.</li>
	<li>오른쪽의 프로젝트 형식 목록에서 <b>클래스 라이브러리</b>를 선택합니다.</li>
	<li><b>MyDotNetActivity</b>를 <b>이름</b>으로 입력합니다.</li>
	<li><b>C:\ADFGetStarted</b>를 <b>위치</b>로 선택합니다.</li>
	<li><b>확인</b>을 클릭하여 프로젝트를 만듭니다.</li>
</ol>
2.  <b>도구</b>를 클릭하고 <b>NuGet 패키지 관리자</b>를 가리킨 다음 <b>패키지 관리자 콘솔</b>을 클릭합니다.
3.	<b>패키지 관리자 콘솔</b>에서 다음 명령을 실행하여 <b>Microsoft.Azure.Management.DataFactories</b>를 가져옵니다.

		Install-Package Microsoft.Azure.Management.DataFactories

4. Azure 저장소 NuGet 패키지를 프로젝트로 가져옵니다.

		Install-Package Azure.Storage

5. 다음 **using** 문을 프로젝트의 원본 파일에 추가합니다.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6. **네임스페이스** 이름을 **MyDotNetActivityNS**로 변경합니다.

		namespace MyDotNetActivityNS

7. 클래스 이름을 **MyDotNetActivity**로 변경하고 아래와 같이 **IDotNetActivity** 인터페이스에서 클래스를 파생합니다.

		public class MyDotNetActivity : IDotNetActivity

8. **IDotNetActivity** 인터페이스의 **Execute** 메서드를 **MyDotNetActivity** 클래스에 구현(추가)하고 다음 샘플 코드를 메서드에 복사합니다.

	다음 샘플 코드에서는 입력 Blob의 줄 수를 계산하고 출력 Blob에 Blob 경로, Blob의 줄 수, 작업이 실행된 컴퓨터, 현재 날짜-시간 등과 같은 내용을 생성합니다.

		/// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
		/// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
			// to get extended properties (for example: SliceStart)
			DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

			// to log all extended properties			
			IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
			logger.Write("Logging extended properties if any...");
			foreach (KeyValuePair<string, string> entry in extendedProperties)
			{
				logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
			}
		

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
       			// with the data slice. 
        		// 
        	    // definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // return a new Dictionary object (unused in this code).
            return new Dictionary<string, string>();
        }

9. 다음과 같은 도우미 메서드를 추가합니다. **Execute** 메서드는 이러한 도우미 메서드를 호출합니다. **GetConnectionString** 메서드는 Azure 저장소 연결 문자열을 검색하고 **GetFolderPath** 메서드는 Blob 위치를 검색합니다. 가장 중요한 점은 **Calculate** 메서드가 각 BLOB를 반복하는 코드를 격리한다는 것입니다.

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.   
		/// </summary>

		private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that will be written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

	GetFolderPath 메서드는 데이터 집합이 가리키는 폴더로 경로를 반환하고 GetFileName 메서드는 데이터 집합이 가리키는 BLOB/파일의 이름을 반환합니다.
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "mycontainer/inputfolder/",
	
	Calculate 메서드는 입력 파일(폴더에서 BLOB)에서 Microsoft 키워드의 인스턴스 수를 계산합니다. 검색 용어("Microsoft")는 코드에 하드 코딩됩니다.

10. 프로젝트를 컴파일합니다. 메뉴에서 **빌드**를 클릭하고 **솔루션 빌드**를 클릭합니다.
11. **Windows 탐색기**를 시작하고 빌드 유형에 따라 **bin\\debug** 또는 **bin\\release** 폴더로 이동합니다.
12. <project folder>\\bin\\Debug 폴더의 이진을 모두 포함하는 zip 파일 **MyDotNetActivity.zip**을 만듭니다. 오류가 발생할 경우 문제를 발생시킨 소스 코드의 줄 번호 같은 추가 정보를 받을 수 있도록 **MyDotNetActivity.pdb** 파일을 포함할 수 있습니다.

	![이진 출력 파일](./media/data-factory-use-custom-activities/Binaries.png)
13. **ADFTutorialDataFactory**의 연결된 서비스 **StorageLinkedService**가 사용하는 Azure Blob 저장소의 Blob 컨테이너 **customactvitycontainer**에 Blob으로 **MyDotNetActivity.zip**을 업로드합니다. Blob 컨테이너 **customactivitycontainer**가 아직 없는 경우 새로 만듭니다.

> [AZURE.NOTE] 데이터 팩터리 프로젝트를 포함하는 Visual Studio의 솔루션에 이 .NET 작업 프로젝트를 추가하는 경우에는 zip 파일을 만들고 Azure Blob 저장소에 수동으로 업로드하는 마지막 두 단계를 수행할 필요가 없습니다. Visual Studio를 사용하여 데이터 팩터리 엔터티를 게시하면 이러한 단계가 게시 프로세스에서 자동으로 수행됩니다. Visual Studio를 사용하여 데이터 팩터리 엔터티를 만들고 게시하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline-using-vs.md) 및 [Azure Blob에서 Azure SQL로 데이터 복사](data-factory-get-started-using-vs.md) 문서를 참조하세요.

### Execute 메서드

이 섹션에서는 **Execute** 메서드에서 코드에 대한 자세한 내용과 참고 사항을 제공합니다.
 
1. 입력 컬렉션을 반복하는 멤버는 [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) 네임스페이스에 있습니다. Blob 컬렉션을 반복하려면 **BlobContinuationToken** 클래스를 사용해야 합니다. 본질적으로 루프를 종료하기 위한 메커니즘으로 토큰과 함께 do-while 루프를 사용해야 합니다. 자세한 내용은 [.NET에서 Blob 저장소를 사용하는 방법](../storage/storage-dotnet-how-to-use-blobs.md)(영문)을 참조하세요. 기본 루프는 다음과 같습니다.

		// Initialize the continuation token.
		BlobContinuationToken continuationToken = null;
		do
		{   
			// Get the list of input blobs from the input storage client object.
			BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    					              true,
    	                              BlobListingDetails.Metadata,
    	                              null,
    	                              continuationToken,
    	                              null,
    	                              null);
			// Return a string derived from parsing each blob.
    		output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
		} while (continuationToken != null);

	자세한 내용은 [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) 메서드에 대한 설명서를 참조하세요.

2.	Blob 집합을 진행하는 코드는 논리적으로 do-while 루프 안으로 들어갑니다. **Execute** 메서드에서 do-while 루프는 **Calculate**라는 메서드로 Blob 목록을 전달합니다. 이 메서드는 **output**이라는 문자열 변수를 반환하는데, 이는 세그먼트에서 모든 Blob을 반복한 결과입니다.

	**Calculate** 메서드에 전달된 Blob에서 검색 용어(**Microsoft**) 항목 수를 반환합니다.

			output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	**Calculate** 메서드가 작업을 완료한 후에는 새 Blob에 작성되어야 합니다. 따라서 처리된 모든 BLOB 집합에 대해 결과를 새 BLOB에 작성할 수 있습니다. 새 BLOB를 작성하려면 먼저 출력 데이터 집합을 찾습니다.

			// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
			Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

			// Convert to blob location object.
			outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	이 코드는 도우미 메서드인 **GetFolderPath**도 호출합니다. 이 메서드는 폴더 경로(저장소 컨테이너 이름)를 검색합니다.
 
			folderPath = GetFolderPath(outputDataset);

	**GetFolderPath**는 DataSet 개체를 AzureBlobDataSet로 캐스팅하며 여기에는 FolderPath라는 속성이 포함됩니다.
			
			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
			
			return blobDataset.FolderPath;

5.	이 코드는 파일 이름(BLOB 이름)을 검색할 **GetFileName** 메서드를 호출합니다. 이 코드는 폴더 경로를 가져오는 위의 코드와 유사합니다.

			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

			return blobDataset.FileName;

6.	파일 이름은 새 URI 개체를 만들어 기록합니다. URI 생성자는 컨테이너 이름을 반환하는 **BlobEndpoint** 속성을 사용합니다. 출력 BLOB URI를 생성하기 위해 폴더 경로 및 파일 이름이 추가됩니다.

			// Write the name of the file. 
			Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	파일 이름이 작성되었고 이제 Calculate 메서드에서 새 BLOB로 출력 문자열을 작성할 수 있습니다.

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## 데이터 팩터리 만들기

**사용자 지정 작업 만들기** 섹션에서 사용자 지정 작업을 만들고 이진과 함께 zip 파일을 업로드하고 PDB 파일을 Azure Blob 컨테이너에 업로드했습니다. 이 섹션에서는 **사용자 지정 작업**을 사용하는 **파이프라인**으로 Azure **데이터 팩터리**를 만듭니다.
 
사용자 지정 작업에 대한 입력 데이터 집합은 Blob 저장소에서 입력 폴더(mycontainer\\inputfolder)의 BLOB(파일)를 나타냅니다. 이 작업에 대한 출력 데이터 집합은 Blob 저장소에서 출력 폴더(mycontainer\\outputfolder)의 출력 BLOB를 나타냅니다.

다음과 같은 내용으로 file.txt라는 파일을 만들고 mycontainer\\inputfolder에 업로드합니다(mycontainer는 Azure Blob 컨테이너의 이름이고 inputfolder는 해당 컨테이너의 폴더 이름임).

	test custom activity Microsoft test custom activity Microsoft

입력 폴더는 폴더에 2개 이상의 파일이 포함된 경우에도 Azure Data Factory의 조각에 해당합니다. 각 조각이 파이프라인으로 처리될 때 사용자 지정 작업은 해당 조각에 대한 입력 폴더에서 모든 BLOB를 반복합니다.

mycontainer\\output 폴더에 1개 이상의 줄(입력 폴더에서 BLOB 수와 동일)이 포함된 하나의 출력 파일이 표시됩니다.
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


이 섹션에서 수행하는 단계는 다음과 같습니다.

1. **데이터 팩터리**를 만듭니다.
2. 사용자 지정 작업이 맵 전용 작업으로 실행될 HDInsight 클러스터에 대한 **연결된 서비스**와 입/출력 BLOB를 포함하는 Azure 저장소를 만듭니다. 
2. 사용자 지정 작업의 입력 및 출력을 나타내는 입력 및 출력 **데이터 집합**을 만듭니다. 
3. 사용자 지정 작업을 사용하는 **파이프라인**을 만들고 실행합니다.

### 1단계: 데이터 팩터리 만들기

1.	Azure 포털에 로그인한 후에 다음을 수행합니다.
	1.	왼쪽 메뉴에서 **새로 만들기**를 클릭합니다.
	2.	**새** 블레이드에서 **데이터 + 분석**을 클릭합니다.
	3.	**데이터 분석** 블레이드에서 **데이터 팩터리**를 클릭합니다.
2.	**새 데이터 팩터리** 블레이드에서 **CustomActivityFactory**를 이름으로 입력합니다. Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 "CustomActivityFactory"를 사용할 수 없습니다.** 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: **yournameCustomActivityFactory**) 다시 만듭니다.
3.	**리소스 그룹 이름**을 클릭하여 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. 
4.	데이터 팩터리를 만들려는 **구독** 및 **지역**을 사용하고 있는지 확인합니다. 
5.	**새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.
6.	Azure 포털의 **대시보드**에 생성된 데이터 팩터리가 표시됩니다.
7.	데이터 팩터리 만들기를 완료한 후에는 데이터 팩터리 블레이드가 표시되며 여기에 데이터 팩터리의 내용이 표시됩니다.

### 2단계: 연결된 서비스 만들기

연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure Data Factory에 연결합니다. 이 단계에서는 Azure 저장소 계정 및 Azure HDInsight 클러스터를 데이터 팩터리에 연결합니다.

#### Azure 저장소 연결된 서비스 만들기

1.	**CustomActivityFactory**에 대한 **데이터 팩터리** 블레이드에서 **작성 및 배포** 타일을 클릭합니다. 데이터 팩터리 편집기가 시작됩니다.
2.	명령 모음에서 **새 데이터 저장소**를 클릭하고 **Azure 저장소**를 선택합니다. 편집기에 Azure 저장소 연결된 서비스를 만들기 위한 JSON 스크립트가 표시됩니다.
3.	**계정 이름**을 Azure 저장소 계정 이름으로 변경하고 **계정 키**를 Azure 저장소 계정의 액세스 키로 변경합니다. 저장소 액세스 키를 확보하는 방법을 알아보려면 [저장소 액세스 키 보기, 복사 및 다시 생성](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)을 참조하세요.
4.	명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.


#### Azure HDInsight 연결된 서비스 만들기 
Azure Data Factory 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다.

> [AZURE.NOTE] 런타임에 .NET 작업의 한 인스턴스는 HDInsight 클러스터의 작업자 노드 하나에서만 실행됩니다. 여러 노드에서 실행되도록 확장할 수 없습니다. .NET 작업의 여러 인스턴스는 HDInsight 클러스터의 서로 다른 노드에서 병렬로 실행될 수 있습니다.

[Azure 데이터 팩터리에서 Pig 및 Hive 사용][hivewalkthrough]의 연습와 함께 [Azure 데이터 팩터리 시작][adfgetstarted] 자습서를 확장하는 경우, 이 연결된 서비스 만들기를 건너뛰고 ADFTutorialDataFactory에 이미 있는 연결된 서비스를 사용할 수 있습니다.


##### 주문형 HDInsight 클러스터를 사용하려면

1. **Azure 포털**의 데이터 팩터리 홈페이지에서 **작성자 및 배포**를 클릭합니다.
2. 데이터 팩터리 편집기의 명령 모음에서 **새 계산**을 클릭하고 메뉴에서 **주문형 HDInsight 클러스터**를 선택합니다.
2. JSON 스크립트에서 다음을 수행합니다.
	1. **clusterSize** 속성에 대해 HDInsight 클러스터의 크기를 지정합니다.
	3. **timeToLive** 속성에 대해 고객이 삭제되기 전에 유휴 상태로 유지될 수 있는 기간을 지정합니다.
	4. **version** 속성에 대해 사용할 HDInsight 버전을 지정합니다. 이 속성을 제외하면 최신 버전이 사용됩니다.  
	5. **linkedServiceName**에 대해 시작 자습서에서 만든 **StorageLinkedService**를 지정합니다.

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.2",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

##### 고유한 HDInsight 클러스터를 사용하려면

1. **Azure 포털**의 데이터 팩터리 홈페이지에서 **작성자 및 배포**를 클릭합니다.
2. **데이터 팩터리 편집기**의 명령 모음에서 **새 계산**을 클릭하고 메뉴에서 **HDInsight 클러스터**를 선택합니다.
2. JSON 스크립트에서 다음을 수행합니다.
	1. **clusterUri** 속성에 대해 HDInsight의 URL을 입력합니다. 예를 들어 https://<clustername>.azurehdinsight.net/을 입력합니다.     
	2. **UserName** 속성에 대해 HDInsight 클러스터에 액세스할 수 있는 사용자 이름을 입력합니다.
	3. **password** 속성에 대해 사용자 암호를 입력합니다.
	4. **LinkedServiceName** 속성에 대해 **StorageLinkedService**를 입력합니다. 시작 자습서에서 만든 연결된 서비스입니다..

2. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

### 3단계: 데이터 집합 만들기
이 단계에서는 입력 및 출력 데이터를 나타낼 데이터 집합을 만듭니다.

#### 입력 데이터 집합 만들기
1.	데이터 팩터리에 대한 **편집기**의 도구 모음에서 **새 데이터 집합** 단추를 클릭하고 드롭다운 메뉴에서 **Azure Blob 저장소**를 클릭합니다.
2.	오른쪽 창의 JSON을 다음 JSON 코드 조각으로 바꿉니다.

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "StorageLinkedService",
			        "typeProperties": {
			            "folderPath": "adftutorial/customactivityinput/",
			            "format": {
			                "type": "TextFormat"
			            }
			        },
			        "availability": {
			            "frequency": "Hour",
			            "interval": 1
			        },
			        "external": true,
			        "policy": {}
			    }
			}

	이 연습에서는 시작 시간: 2015-11-16T00:00:00Z 및 종료 시간: 2015-11-16T05:00:00Z로 나중에 파이프라인을 만듭니다. 매시간 데이터를 생성하도록 예약되었으므로 5개의 입/출력 조각이 있습니다(**00**:00:00 -> **05**:00:00 범위).

	입력 데이터 집합에 대한 **frequency** 및 **interval**은 **Hour** 및 **1**로 설정되며 이는 입력 조각이 매시간 제공됨을 의미합니다. 이 샘플에서는 intputfolder에서와 동일한 파일(file.txt)입니다.

	다음은 각 조각에 대한 시작 시간이며 위의 JSON 코드 조각에서 SliceStart 시스템 변수로 표현됩니다.

	
3.	도구 모음에서 **배포**를 클릭하여 **InputDataset**을 만들고 배포합니다. 편집기의 제목 표시줄에 **테이블이 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.


#### 출력 테이블 만들기

1. **데이터 팩터리 편집기**에서 **새 데이터 집합**을 클릭하고 명령 모음에서 **Azure Blob 저장소**를 클릭합니다.
2. 오른쪽 창의 JSON 스크립트를 다음 JSON 스크립트로 바꿉니다.

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "adftutorial/customactivityoutput",
		            "partitionedBy": [
		                {
		                    "name": "slice",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy-MM-dd-HH"
		                    }
		                }
		            ]
		        },
		        "availability": {
		            "frequency": "Hour",
		            "interval": 1
		        }
		    }
		}

 	출력 위치는 **adftutorial/customactivityoutput/YYYYMMDDHH/**입니다. 여기서 YYYYMMDDHH는 조각이 생성되는 연도, 월, 날짜 및 시간입니다. 자세한 내용은 [개발자 참조][adf-developer-reference](영문)를 참조하세요.

	각 입력 조각에 대해 출력 BLOB/파일이 생성됩니다. 각 조각에 대해 출력 파일의 이름을 지정하는 방법은 다음과 같습니다. **adftutorial\\customactivityoutput**이라는 하나의 출력 폴더에 모든 출력 파일이 생성됩니다.

	| 조각 | 시작 시간 | 출력 파일 |
	| :---- | :--------- | :---------- | 
	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

	입력 폴더에 있는 모든 파일은 위에 언급된 시작 시간의 조각 중 일부입니다. 이 조각을 처리할 때 사용자 지정 작업은 각 파일을 검색하고 검색 용어("Microsoft") 항목 수와 함께 출력 파일에 줄을 생성합니다. inputfolder에 세 개의 파일이 있는 경우 출력 파일에 각 시간별 조각에 해당하는 세 개의 줄이 생깁니다(예: 2015-11-16-00.txt, 2015-11-16:01:00:00.txt 등....).


2. 명령 모음에서 **배포**를 클릭하여 **OutputDataset**을 배포합니다.


### 사용자 지정 작업을 사용하는 파이프라인 만들기 및 실행

1. 데이터 팩터리 편집기의 명령 모음에서 **새 파이프라인**을 클릭합니다. 명령이 표시되지 않으면 **... (줄임표)**을 클릭하여 표시합니다.
2. 오른쪽 창의 JSON을 다음 JSON 스크립트로 바꿉니다. 사용자 고유의 클러스터를 사용하려고 하며 **HDInsightLinkedService** 연결된 서비스를 만드는 단계를 따른 경우 다음 JSON에서 **HDInsightOnDemandLinkedService**를 **HDInsightLinkedService**로 바꿉니다.

		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "InputDataset"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputDataset"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 1,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
		    "isPaused": false
		  }
		}

	**StartDateTime** 값을 현재 날짜 3일 전 날짜로, **EndDateTime** 값을 현재 날짜로 바꿉니다. StartDateTime 및 EndDateTime은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. 출력 테이블이 매일 생성되도록 예약되었으므로 3개의 조각이 생성됩니다.

	다음 사항에 유의하세요.

	- activities 섹션에는 **DotNetActivity** 유형의 작업 하나밖에 없습니다.
	- 시작 자습서에서 사용한 입력 테이블 **EmpTableFromBlob**을 동일하게 사용합니다.
	- 다음 단계에서 만들 새 출력 테이블 **OutputTableForCustom**을 사용합니다.
	- **AssemblyName**을 DLL의 이름 **MyActivities.dll**로 설정합니다.
	- **EntryPoint**를 **MyDotNetActivityNS.MyDotNetActivity**로 설정합니다.
	- **PackageLinkedService**가 사용자 지정 작업 zip 파일을 포함하는 Blob 저장소를 가리키는 **StorageLinkedService**로 설정됩니다. 입/출력 파일 및 사용자 지정 작업 zip 파일에 대해 서로 다른 Azure 저장소 계정을 사용하는 경우 다른 Azure 저장소 연결된 서비스를 만들어야 합니다. 이 문서에서는 동일한 Azure 저장소 계정을 사용 중이라고 가정합니다.
	- **PackageFile**을 **customactivitycontainer/MyDotNetActivity.zip**으로 설정합니다. <containerforthezip>/<nameofthezip.zip> 형식입니다.
	- 사용자 지정 작업은 입력으로 **InputDataset**을, 출력으로 **OutputDataset**을 사용합니다.
	- 사용자 지정 활동의 linkedServiceName 속성은 **HDInsightLinkedService**를 가리키며 Azure Data Factory에 사용자 지정 작업을 Azure HDInsight 클러스터에서 실행해야 함을 알려줍니다.
	- **isPaused** 속성은 기본적으로 **false**로 설정됩니다. 이 예제에서는 조각이 이전에 시작되므로 파이프라인이 즉시 실행됩니다. 파이프라인을 일시 중지하려면 이 속성을 true로 설정하고 다시 시작하려면 false로 다시 설정할 수 있습니다. 
	- **start** 시간과 **end** 시간은 **5**시간 차이이며 조각은 매시간 생성되므로 파이프라인에 의해 5조각이 생성됩니다. 


4. 명령 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다.

### 파이프라인 모니터링
 
8. Azure 포털의 데이터 팩터리 블레이드에서 **다이어그램**을 클릭합니다.
	
	![다이어그램 타일](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. 다이어그램 뷰에서 OutputDataset을 클릭합니다.
 
	![다이어그램 뷰](./media/data-factory-use-custom-activities/diagram.png)

10. 5개의 출력 조각이 이미 생성된 경우 준비 상태로 표시됩니다.

	![출력 분할](./media/data-factory-use-custom-activities/OutputSlices.png)
	
12. 출력 파일이 **adftutorial** 컨테이너의 Blob 저장소에 생성되었는지 확인합니다.

	![사용자 지정 작업의 출력][image-data-factory-ouput-from-custom-activity]

9. 출력 파일을 열면 다음과 유사한 출력이 표시됩니다.

	inputfolder/2015-11-16-00/file.txt 파일에 2개의 검색 용어 "Microsoft" 항목이 있습니다.

10.	[Azure 포털][azure-preview-portal] 또는 Azure PowerShell cmdlet을 사용하여 데이터 팩터리, 파이프라인 및 데이터 집합을 모니터링합니다. 포털을 통해서나 cmdlet을 사용하여 다운로드할 수 있는 로그(특히 user-0.log)에 있는 사용자 지정 활동의 코드에서 **ActivityLogger**의 메시지를 확인할 수 있습니다.

	![사용자 지정 작업의 로그 다운로드][image-data-factory-download-logs-from-custom-activity]


데이터 집합 및 파이프라인 모니터링에 대한 자세한 단계는 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md)를 참조하세요.

### 파이프라인 디버깅
디버깅은 몇 가지 기본적인 방법으로 구성됩니다.

1.	입력 조각이 **Ready**로 설정되지 않은 경우 입력 폴더 구조가 올바르고 file.txt가 입력 폴더에 있는지 확인합니다.
2.	사용자 지정 작업의 **Execute** 메서드에서 **IActivityLogger** 개체를 사용하여 문제 해결에 도움이 되는 정보를 기록합니다. 기록된 메시지는 user\_0.log 파일에 표시됩니다. 

	**OutputDataset** 블레이드에서 조각에 대한 **데이터 조각** 블레이드를 보려면 해당 조각을 클릭합니다. 해당 조각에 대한 **작업 실행**이 표시됩니다. 해당 조각에 대한 하나의 작업 실행이 표시됩니다. 명령 모음에서 실행을 클릭하면 동일한 조각에 대해 다른 작업 실행을 시작할 수 있습니다.

	작업 실행을 클릭하면 로그 파일 목록과 함께 **작업 실행 세부 정보** 블레이드가 표시됩니다. 기록된 메시지는 user\_0.log 파일에 표시됩니다. 오류가 발생하면 파이프라인/작업 JSON에서 재시도 횟수가 3으로 설정되므로 세 개의 작업 실행이 표시됩니다. 작업 실행을 클릭하면 문제 해결을 위해 검토할 수 있는 로그 파일이 표시됩니다.

	로그 파일 목록에서 **user-0.log**를 클릭합니다. 오른쪽 패널은 **IActivityLogger.Write** 메서드를 사용한 결과입니다.

	또한 **system-0.log**에서 시스템 오류 메시지 및 예외를 확인해야 합니다.

3.	zip 파일에 **PDB** 파일을 포함하여 오류 세부 정보에 오류 발생 시 **호출 스택**과 같은 정보를 포함하도록 합니다.
4.	사용자 지정 작업에 대한 zip 파일의 모든 파일은 하위 폴더가 없는 **최상위**여야 합니다.
5.	**assemblyName**(MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile**(customactivitycontainer/MyDotNetActivity.zip) 및 **packageLinkedService**(zip 파일을 포함하는 Azure Blob 저장소를 가리켜야 함)가 올바른 값으로 설정되었는지 확인합니다. 
6.	오류를 해결했고 조각을 다시 처리하려면 **OutputDataset** 블레이드에서 조각을 마우스 오른쪽 단추로 클릭하고 **실행**을 클릭합니다. 


## 사용자 지정 작업 업데이트
사용자 지정 작업의 코드를 업데이트하는 경우 코드를 작성하고 새 이진이 포함된 zip 파일을 Blob 저장소로 업로드합니다.

## 확장 속성 액세스
아래와 같이 작업 JSON에서 확장 속성을 선언할 수 있습니다.

	"typeProperties": {
	  "AssemblyName": "MyDotNetActivity.dll",
	  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	  "PackageLinkedService": "StorageLinkedService",
	  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	  "extendedProperties": {
	    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
		"DataFactoryName": "CustomActivityFactory"
	  }
	},

위의 예제에는 두 가지 확장 속성, **SliceStart** 및 **DataFactoryName**이 있습니다. SliceStart의 값은 SliceStart 시스템 변수를 기반으로 합니다. 지원되는 시스템 변수 목록은 [시스템 변수](data-factory-scheduling-and-execution.md#data-factory-system-variables)를 참조하세요. DataFactoryName의 값은 "CustomActivityFactory"로 하드 코드됩니다.

**Execute** 메서드에서 이러한 확장 속성에 액세스하려면 다음과 비슷한 코드를 사용합니다.

	// to get extended properties (for example: SliceStart)
	DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
	string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

	// to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
    	logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
	}


## <a name="AzureBatch"></a> Azure 배치 연결된 서비스 사용
> [AZURE.NOTE] Azure 배치 서비스에 대한 개요는 [Azure 배치 기본 사항][batch-technical-overview]을 참조하고, Azure 배치 서비스를 빨리 시작하려면 [.NET용 Azure 배치 라이브러리 시작][batch-get-started]을 참조하세요.

컴퓨팅 리소스로 Azure Batch를 사용하여 사용자 지정.NET 작업을 실행할 수 있습니다. 자체적인 Azure Batch 풀을 만들고 다른 구성과 함께 몇 가지 VM을 지정해야 합니다. Azure Batch 풀이 고객에게 제공하는 기능은 다음과 같습니다.

1. 하나에서 수천 개의 코어를 포함하는 풀을 만듭니다.
2. 수식에 따라 VM 수를 자동으로 조정
3. 모든 크기의 VM 지원
4. VM마다 몇 가지 작업을 구성 가능
5. 무제한의 작업 큐


이전 섹션에서 설명한 연습에서 Azure 배치 연결된 서비스를 사용하는 대략적인 단계는 다음과 같습니다.

1. [Azure 포털](http://manage.windowsazure.com)을 사용하여 Azure 배치 계정을 만듭니다. 지침은 [Azure 배치 계정 만들기 및 관리][batch-create-account] 문서를 참조하세요. Azure 배치 계정 이름 및 계정 키를 적어둡니다.

	[New-AzureBatchAccount][new-azure-batch-account] cmdlet을 사용하여 Azure 배치 계정을 만들 수도 있습니다. 이 cmdlet 사용에 관한 자세한 지침은 [Azure PowerShell을 사용하여 Azure 배치 계정 관리][azure-batch-blog]를 참조하세요.
2. Azure 배치 풀을 만듭니다. [Azure Batch 탐색기 도구][batch-explorer]의 소스 코드를 다운로드하여 컴파일하여 사용하거나 [.NET용 Azure Batch 라이브러리][batch-net-library]를 사용하여 Azure Batch 풀을 만들 수 있습니다. Azure 배치 탐색기를 사용하는 단계별 지침은 [Azure 배치 탐색기 샘플 연습][batch-explorer-walkthrough]을 참조하세요.

	[New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) cmdlet을 사용하여 Azure 배치 풀을 만들 수도 있습니다.

	2개 이상의 계산 노드와 함께 Azure 배치 풀을 만들어 조각이 병렬로 처리되도록 할 수 있습니다. 배치 탐색기를 사용하는 경우

	- 풀에 대한 ID(**풀 ID**)를 입력합니다. 데이터 팩터리 솔루션을 만들 때 필요하므로 **풀의 ID**를 메모해둡니다. 
	- 운영 체제 제품군 설정에 **Windows Server 2012 R2**를 지정합니다.
	- **계산 노드당 최대 작업** 설정에 대한 값으로 **2**를 지정합니다.
	- **대상 전용 수** 설정에 대한 값으로 **2**를 지정합니다. 

	데이터 팩터리 서비스가 Azure 배치에 adf-<pool name>:job-xxx 이름으로 작업을 만듭니다. 조각의 각 작업 실행에 대한 작업(task)이 만들어집니다. 처리를 위해 준비된 10개 조각이 있는 경우 이 작업에 10개 작업이 만들어집니다. 풀에 여러 계산 노드가 있는 경우 병렬로 실행 중인 두 개 이상의 조각을 포함할 수 있습니다. 계산 노드당 최대 작업이 1보다 크게 설정된 경우에도 동일한 계산에 실행 중인 두 개 이상의 조각을 포함할 수 있습니다.
	
	![배치 탐색기 작업](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

	![데이터 팩터리 및 배치](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

2. 다음 JSON 템플릿을 사용하여 Azure 배치 연결된 서비스를 만듭니다. 데이터 팩터리 편집기에 표시되는 유사한 템플릿을 사용하여 시작할 수 있습니다. JSON 조각에서 Azure 배치 계정 이름, 계정 키 및 풀 이름을 지정합니다.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT] **Azure 배치 계정 블레이드**의 **URL**은 accountname.region.batch.azure.com 형식을 사용합니다. JSON **batchUri** 속성의 경우 URL에서 **"accountname."을 제거**하고 **accountName** JSON 속성에 대해 **accountname**을 사용해야 합니다.

	**poolName** 속성의 경우 풀 이름 대신 풀 ID를 지정할 수도 있습니다.

	이러한 속성에 대한 설명은 [Azure 배치 연결된 서비스 MSDN 토픽](https://msdn.microsoft.com/library/mt163609.aspx)을 참조하세요.

2.  데이터 팩터리 편집기에서 연습에서 만든 파이프라인에 대한 JSON 정의를 열고 **HDInsightLinkedService**를 **AzureBatchLinkedService**로 바꿉니다.
3.  Azure 배치 서비스를 사용한 시나리오를 테스트할 수 있도록 파이프라인의 시작 시간과 종료 시간을 변경하는 것이 좋습니다.
4.  다음 다이어그램과 같이 Azure 배치 탐색기에서 조각 처리와 관련된 Azure 배치 작업을 확인할 수 있습니다.

	![Azure 배치 작업][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE] 데이터 팩터리 서비스는 HDInsight에 대해서와 마찬가지로 Azure Batch에 대한 주문형 옵션을 지원하지 않습니다. Azure Data Factory에서는 사용자 고유의 Azure Batch 풀만 사용할 수 있습니다.

## 참고 항목

[Azure 데이터 팩터리 업데이트: Azure 배치를 사용하여 ADF 사용자 지정 .NET 작업 실행](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=AcomDC_0128_2016-->