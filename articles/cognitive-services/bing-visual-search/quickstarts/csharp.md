---
title: Bing Visual Search API에 대한 C# 빠른 시작 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bing Visual Search API에 이미지를 업로드하고 이미지에 대한 중요 정보를 다시 얻는 방법을 보여 줍니다.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: dd7531004759cdaeb59f4706dc2650d0db3c0cdb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377734"
---
# <a name="your-first-bing-visual-search-query-in-c"></a>C#의 첫 번째 Bing Visual Search 쿼리

Bing Visual Search API는 사용자가 제공하는 이미지에 대한 정보를 반환합니다. 이미지의 URL, 인사이트 토큰을 사용하거나 이미지를 업로드하여 이미지를 제공할 수 있습니다. 이러한 옵션에 대한 내용은 [Bing Visual Search API란?](../overview.md)을 참조하세요. 이 문서에서는 이미지 업로드 방법을 보여 줍니다. 이미지 업로드는 잘 알려진 이정표 사진을 찍은 후 해당 정보를 다시 얻는 모바일 시나리오에서 유용할 수 있습니다. 예를 들어, 중요 정보에는 이정표에 대한 잡학 지식이 포함될 수 있습니다. 

로컬 이미지를 업로드하는 경우 다음은 POST 본문에 포함해야 하는 양식 데이터를 보여 줍니다. 양식 데이터에는 Content-Disposition 헤더가 포함되어야 합니다. 해당 `name` 매개 변수를 "image"로 설정해야 하고 `filename` 매개 변수를 임의의 문자열 매개 변수로 설정할 수 있습니다. 양식의 콘텐츠는 이미지의 이진입니다. 업로드할 수는 최대 이미지 크기는 1MB입니다. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

이 문서에는 Bing Visual Search API 요청을 보내고 JSON 검색 결과를 표시하는 간단한 콘솔 응용 프로그램이 포함되어 있습니다. 이 응용 프로그램은 C#에서 작성되었지만 API는 RESTful 웹 서비스로, HTTP를 요청하고 JSON을 구문 분석할 수 있는 모든 프로그래밍 언어와 호환됩니다. 

예제 프로그램은 .NET Core 클래스만 사용하며 .NET CLR을 사용하여 Windows에서 또는 [Mono](http://www.mono-project.com/)를 사용하여 Linux 또는 macOS에서 실행됩니다.


## <a name="prerequisites"></a>필수 조건

Windows에서 실행되는 이 코드를 가져오려면 [Visual Studio 2017](https://www.visualstudio.com/downloads/)이 필요합니다. (무료 Community Edition이 작동됩니다.)

이 빠른 시작에서는 [평가판](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) 구독 키 또는 유료 구독 키를 사용할 수 있습니다.

## <a name="running-the-application"></a>응용 프로그램 실행

다음에서는 HttpWebRequest를 사용하여 메시지를 보내는 방법을 보여 줍니다. HttpClient, HttpRequestMessage 및 MultipartFormDataContent를 사용하는 예제를 보려면 [HttpClient 사용](#using-httpclient)을 참조하세요.

이 응용 프로그램을 실행하려면 다음 단계를 따릅니다.

1. Visual Studio에서 새 콘솔 솔루션을 만듭니다.
1. `Program.cs`의 내용을 이 빠른 시작에 표시되는 코드로 바꿉니다.
2. `accessKey` 값을 구독 키로 바꿉니다.
2. `imagePath` 값을 업로드할 이미지의 경로로 바꿉니다.
3. 프로그램을 실행합니다.


```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;

namespace VisualSearchUpload
{

    class Program
    {
        // **********************************************
        // *** Update and verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Set the path to the image that you want to get insights of. 
        static string imagePath = @"<pathtoimagegoeshere>";

        // Boundary strings for form data in body of POST.
        const string CRLF = "\r\n";
        static string BoundaryTemplate = "batch_{0}";
        static string StartBoundaryTemplate = "--{0}";
        static string EndBoundaryTemplate = "--{0}--";

        const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
        const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;


        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        // Set up POST body.
                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var startFormData = BuildFormDataStart(boundary, filename);
                        var endFormData = BuildFormDataEnd(boundary);
                        var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);

                        var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }



        /// <summary>
        /// Verify that imagePath exists.
        /// </summary>
        static Boolean IsImagePathSet(string path)
        {
            if (string.IsNullOrEmpty(path))
                throw new ArgumentException("Image path is null or empty.");

            if (!File.Exists(path))
                throw new ArgumentException("Image path does not exist.");

            var size = new FileInfo(path).Length;

            if (size > 1000000)
                throw new ArgumentException("Image is greater than the 1 MB maximum size.");

            return true;
        }



        /// <summary>
        /// Get the binary characters of an image.
        /// </summary>
        static byte[] GetImageBinary(string path)
        {
            return File.ReadAllBytes(path);
        }


        /// <summary>
        /// Get the image's filename.
        /// </summary>
        static string GetImageFileName(string path)
        {
            return new FileInfo(path).Name;
        }


        /// <summary>
        /// Build the beginning part of the form data.
        /// </summary>
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }


        /// <summary>
        /// Build the ending part of the form data.
        /// </summary>
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }



        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }


            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```


## <a name="using-httpclient"></a>HttpClient 사용

HttpClient를 사용하는 경우 MultipartFormDataContent를 사용하여 양식 데이터를 작성할 수 있습니다. 코드의 다음 섹션을 사용하여 이전 예제에 사용된 같은 이름의 메서드를 바꾸기만 하면 됩니다.

Main 메서드를 다음 코드로 바꿉니다.

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

BingImageSearch 메서드를 다음 코드로 바꿉니다.

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```




## <a name="next-steps"></a>다음 단계

[인사이트 토큰을 사용하여 이미지에 대한 중요 정보 얻기](../use-insights-token.md)  
[Bing Visual Search 단일 페이지 앱 자습서](../tutorial-bing-visual-search-single-page-app.md)
[Bing Visual Search 개요](../overview.md)  
[사용해보기](https://aka.ms/bingvisualsearchtryforfree)  
[평가판 액세스 키 받기](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API 참조](https://aka.ms/bingvisualsearchreferencedoc)
