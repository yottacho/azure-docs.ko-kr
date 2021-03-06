---
title: 자습서 - Service Fabric Mesh에 다중 서비스 웹 응용 프로그램 만들기, 디버그 및 배포 | Microsoft Docs
description: 이 자습서에서는 백 엔드 웹 서비스와 통신하고, 로컬로 디버그하고, Azure에 게시하는 ASP.NET Core 웹 사이트로 구성된 다중 서비스 Azure Service Fabric Mesh 응용 프로그램을 만듭니다.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 692a384c3695001677b7a3d6161b77f3f015ad7c
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125872"
---
# <a name="tutorial-create-debug-and-deploy-a-multi-service-web-application-to-service-fabric-mesh"></a>자습서 - Service Fabric Mesh에 다중 서비스 웹 응용 프로그램 만들기, 디버그 및 배포

이 자습서는 시리즈의 1부입니다. ASP.NET 웹 프런트 엔드 및 ASP.NET Core 웹 API 백 엔드 서비스가 있는 Azure Service Fabric Mesh 응용 프로그램을 만드는 방법에 대해 알아봅니다. 그런 다음, 로컬 개발 클러스터에서 응용 프로그램을 디버그하고 Azure에 응용 프로그램을 게시합니다. 작업이 완료되면 Azure Service Fabric Mesh에서 실행되는 Service Fabric Mesh 응용 프로그램에서 서비스 간 호출을 보여 주는 간단한 할 일 앱이 만들어집니다.

할 일 응용 프로그램을 수동으로 만들지 않으려면 완성된 응용 프로그램에 대한 [소스 코드를 다운로드](https://github.com/azure-samples/service-fabric-mesh)하고 [로컬로 응용 프로그램을 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)하도록 건너뛸 수 있습니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * ASP.NET 웹 프런트 엔드로 구성된 Service Fabric Mesh 응용 프로그램을 만듭니다.
> * 할 일 항목을 나타내는 모델을 만듭니다.
> * 백 엔드 서비스를 만들고 이 서비스에서 데이터를 검색합니다.
> * 백 엔드 서비스에 대한 모델 뷰 컨트롤러 패턴의 일부로 컨트롤러와 DataContext를 추가합니다.
> * 할 일 항목을 표시할 웹 페이지를 만듭니다.
> * 백 엔드 서비스를 식별하는 환경 변수를 만듭니다.

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Service Fabric Mesh 응용 프로그램 빌드
> * [로컬로 앱 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Azure에 앱 게시](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만들 수 있습니다.

* Service Fabric 런타임, SDK, Docker 및 Visual Studio 2017 설치가 포함된 [개발 환경을 설정](service-fabric-mesh-howto-setup-developer-environment-sdk.md)했는지 확인합니다.

## <a name="create-a-service-fabric-mesh-project"></a>Service Fabric Mesh 프로젝트 만들기

Visual Studio를 열고 **파일** > **새로 만들기** > **프로젝트...** 를 차례로 선택합니다.

**새 프로젝트** 대화 상자 위쪽의 **검색** 상자에서 `mesh`를 입력합니다. **Service Fabric Mesh 응용 프로그램** 템플릿을 선택합니다. 템플릿이 표시되지 않으면 [개발 환경 설정](service-fabric-mesh-howto-setup-developer-environment-sdk.md)에서 설명한 대로 Mesh SDK 및 VS 도구 미리 보기를 설치했는지 확인합니다.  

**이름** 상자에서 `todolistapp`을 입력하고, **위치** 상자에서 폴더 경로를 프로젝트에 대한 파일을 저장하려는 위치로 설정합니다.

**솔루션용 디렉터리 만들기**가 선택되어 있는지 확인하고, **확인**을 클릭하여 Service Fabric Mesh 프로젝트를 만듭니다.

![Visual Studio의 새 Service Fabric Mesh 프로젝트 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

그러면 **새 Service Fabric 서비스** 대화 상자가 표시됩니다.

### <a name="create-the-web-front-end-service"></a>웹 프런트 엔드 서비스 만들기

**새 Service Fabric 서비스** 대화 상자에서 **ASP.NET Core** 프로젝트 형식을 선택하고, **컨테이너 OS**가 **Windows**로 설정되어 있는지 확인합니다.

**서비스 이름**을 **WebFrontEnd**로 설정합니다. **확인**을 눌러 ASP.NET Core 서비스를 만듭니다.

![Visual Studio의 새 Service Fabric Mesh 프로젝트 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

그러면, **새 ASP.NET Core 웹 응용 프로그램** 대화 상자가 표시됩니다. **새 ASP.NET Core 웹 응용 프로그램** 대화 상자에서 **웹 응용 프로그램**을 선택한 다음, **확인**을 클릭합니다.

![Visual Studio의 새 ASP.NET Core 응용 프로그램](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

이제 Service Fabric Mesh 응용 프로그램이 만들어졌습니다. 다음으로, 할 일 정보에 대한 모델을 만들겠습니다.

## <a name="create-the-to-do-items-model"></a>할 일 항목 모델 만들기

간단히 하기 위해 할 일 항목은 메모리의 목록에 저장됩니다. 할 일 항목에 대한 클래스 라이브러리와 이러한 항목을 보관할 목록을 만듭니다. 현재 **todolistapp**이 로드되어 있는 Visual Studio에서 **파일** > **추가** > **새 프로젝트**를 차례로 선택합니다.

**새 프로젝트** 대화 상자 위쪽의 **검색** 상자에서 `C# .net core class`를 입력합니다. **클래스 라이브러리(.NET Core)** 템플릿을 선택합니다.

**이름** 상자에서 `Model`을 입력합니다. **확인**을 클릭하여 클래스 라이브러리를 만듭니다.

[솔루션 탐색기]의 **모델** 아래에서 **Class1.cs**를 마우스 오른쪽 단추로 클릭하고 **이름 바꾸기**를 선택합니다. **ToDoItem.cs** 클래스의 이름을 바꿉니다. 모든 참조의 이름을 바꿀 것인지 묻는 메시지가 표시되면 **예**를 클릭합니다.

비어 있는 `class ToDoItem`의 내용을 다음으로 바꿉니다.

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

이 클래스는 개별 할 일 항목을 나타냅니다.

Visual Studio에서 **모델** 클래스 라이브러리를 마우스 오른쪽 단추로 클릭하고, 할 일 항목을 보관할 목록을 만들기 위해 **추가** > **클래스...** 를 차례로 선택합니다. **새 항목 추가** 대화 상자가 표시됩니다. **이름**을 `ToDoList.cs`로 설정하고 **추가**를 클릭합니다.

**ToDoList.cs**에서 비어 있는 `class ToDoList`를 다음으로 바꿉니다.

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

다음으로, 할 일 항목을 추적할 서비스 패브릭 서비스를 만들겠습니다.

## <a name="create-the-back-end-service"></a>백 엔드 서비스 만들기

Visual Studio **솔루션 탐색기** 창에서 **todolistapp**을 마우스 오른쪽 단추로 클릭하고, **추가** > **새 Service Fabric 서비스...** 를 차례로 클릭합니다.

**새 Service Fabric 서비스** 대화 상자가 표시됩니다. **ASP.NET Core** 프로젝트 형식을 선택하고, **컨테이너 OS**가 **Windows**로 설정되어 있는지 확인합니다.

**서비스 이름**을 **ToDoService**로 설정합니다. **확인**을 클릭하여 ASP.NET Core 서비스를 만듭니다. 그러면 **새 ASP.NET Core 웹 응용 프로그램** 대화 상자가 표시됩니다. 이 대화 상자에서 **API**, **확인**을 차례로 선택하면 서비스 프로젝트가 솔루션에 추가됩니다.

![Visual Studio의 새 ASP.NET Core 응용 프로그램](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

백 엔드 서비스는 UI를 제공하지 않으므로 서비스가 시작되면 브라우저 시작을 해제합니다. **솔루션 탐색기**에서 **ToDoService**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 표시되는 속성 창에서 왼쪽에 있는 **디버그** 탭을 선택하고 **브라우저 시작**의 선택을 취소합니다. **Ctrl+S** 를 눌러 변경 내용을 저장합니다.

이 서비스는 할 일 정보를 유지 관리하므로 Model 클래스 라이브러리에 대한 참조를 추가합니다. [솔루션 탐색기]에서 **ToDoService**를 마우스 오른쪽 단추로 클릭한 다음, **추가** > **참조...** 를 차례로 선택합니다. **참조 관리자** 대화 상자가 표시됩니다.

**참조 관리자**에서 **모델**에 대한 확인란을 선택하고 **확인**을 클릭합니다.

### <a name="add-a-data-context"></a>데이터 컨텍스트 추가

다음으로, 데이터 모델의 데이터 처리를 조정하는 데이터 컨텍스트를 만듭니다.

데이터 컨텍스트 클래스를 추가하려면 [솔루션 탐색기]에서 **ToDoService**를 마우스 오른쪽 단추로 클릭한 다음, **추가** > **클래스**를 차례로 클릭합니다.
표시되는 **새 항목 추가** 대화 상자에서 **클래스**가 선택되어 있는지 확인하고, **이름**을 `DataContext`로 설정하고, **추가**를 클릭합니다.

**DataContext.cs**에서 비어 있는 `class DataContext`의 내용을 다음으로 바꿉니다.

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        ToDoList = new Model.ToDoList("Main List");

        // Seed to-do list

        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

최소한의 이 데이터 컨텍스트는 일부 할 일 항목 샘플을 채우고 이러한 항목에 대한 액세스를 제공합니다.

### <a name="add-a-controller"></a>컨트롤러 추가

**ToDoService** 프로젝트를 만들 때 HTPP 요청을 처리하고 HTTP 응답을 만드는 기본 컨트롤러가 템플릿에서 제공되었습니다. **솔루션 탐색기**의 **ToDoService** 아래에서 **컨트롤러** 폴더를 열어 **ValuesController.cs** 파일을 확인합니다. 

**ValuesController.cs**를 마우스 오른쪽 단추로 클릭한 다음, **이름 바꾸기**를 클릭합니다. 파일의 이름을 `ToDoController.cs`로 변경합니다. 모든 참조의 이름을 바꾸라는 메시지가 표시되면 **예**를 클릭합니다.

**ToDoController.cs** 파일을 열고 `class ToDoController`의 내용을 다음으로 바꿉니다.

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

이 자습서에서는 다른 서비스와의 통신에 집중하기 위해 추가, 삭제 등은 구현하지 않습니다.

## <a name="create-the-web-page-that-displays-to-do-items"></a>할 일 항목을 표시하는 웹 페이지 만들기

백 엔드 서비스가 구현된 상태에서 제공하는 할 일 항목을 표시할 웹 사이트를 코딩합니다. 다음에 나오는 단계는 **WebFrontEnd** 프로젝트 내에서 수행됩니다.

할 일 항목을 표시하는 웹 페이지에는 **ToDoItem** 클래스 및 목록에 대한 액세스가 필요합니다.
**솔루션 탐색기**에서 **WebFrontEnd**를 마우스 오른쪽 단추로 클릭하고 **추가** > **참조...** 를 차례로 선택하여 모델 프로젝트에 대한 참조를 추가합니다. **참조 관리자** 대화 상자가 표시됩니다.

**참조 관리자**에서 **모델**에 대한 확인란, **확인**을 차례로 클릭합니다.

**솔루션 탐색기**에서 **WebFrontEnd** > **페이지** > **Index.cshtml**로 차례로 이동하여 Index.cshtml 페이지를 엽니다. **Index.cshtml**이 열립니다.

파일 전체의 내용을 할 일 항목이 표시될 간단한 테이블을 정의하는 다음 HTML로 바꿉니다.

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

**솔루션 탐색기**에서 **Index.cshtml**을 연 다음, **Index.cshtml.cs**를 열어 인덱스 페이지의 코드를 엽니다. **Index.cshtml.cs**의 맨 위쪽에 `using System.Net.Http;`를 추가합니다.

`public class IndexModel`의 내용을 다음으로 바꿉니다.

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>환경 변수 만들기

백 엔드 서비스에 대한 URL은 해당 서비스와 통신하는 데 필요합니다. 이 자습서의 목적을 위해, IndexModel의 일부로 위에서 정의된 코드에서 발췌한 다음 부분은 환경 변수를 읽어 URL을 작성합니다.

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

URL은 서비스 이름과 포트로 구성됩니다. 이 모든 정보는 **ToDoService** 프로젝트의 service.yaml 파일에 있습니다.

**솔루션 탐색기**에서 **ToDoService** 프로젝트로 이동하고 **서비스 리소스** > **service.yaml**을 차례로 엽니다.

![그림 1 - ToDoService service.yaml 파일](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

* `ToDoService` 서비스 이름은 `services:` 아래의 `name:` 뒤에 있습니다(위 그림 (1) 참조).
* `20008` 포트는 `endpoints:` 아래의 `port:` 뒤에 있습니다(위 그림 (2) 참조). 프로젝트의 포트 번호가 다를 수 있습니다.

다음으로, 서비스 이름과 포트 번호를 나타내는 환경 변수가 백 엔드 서비스를 호출할 수 있도록 WebFrontEnd 프로젝트에 정의됩니다.

**솔루션 탐색기**에서 **WebFrontEnd** > **서비스 리소스** > **service.yaml**로 차례로 이동하여 백 엔드 서비스 주소를 지정하는 변수를 정의합니다.

service.yaml 파일에서 `environmentVariables` 아래에 다음 변수를 추가합니다. 간격이 중요하므로 추가하는 변수를 `environmentVariables:` 아래의 다른 변수와 맞춥니다.

> [!IMPORTANT]
> service.yaml 파일에서 탭이 아닌 공백은 변수 들여쓰기에 사용해야 합니다. 그렇지 않으면 컴파일되지 않습니다. 환경 변수를 만들 때 Visual Studio에서 탭을 삽입할 수 있습니다. 모든 탭을 공백으로 바꾸세요. **빌드** 디버그 출력에 오류가 표시되지만 앱은 계속 실행됩니다. 그러나 탭을 공백으로 변환할 때까지는 작동하지 않습니다. service.yaml 파일에 탭이 없는지 확인하려면 Visual Studio 편집기에서 **편집**  > **고급**  > **공백 보기**를 차례로 사용하여 공백이 표시되도록 할 수 있습니다.

`ApiHostPort` 값이 다를 수도 있지만 **WebFrontEnd** 프로젝트의 **service.yaml** 파일은 다음과 같을 수 있습니다.

![WebFrontEnd 프로젝트의 Service.yaml](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)

이제 백 엔드 웹 서비스와 함께 Service Fabric Mesh 응용 프로그램의 이미지를 로컬 클러스터에 빌드하고 배포할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * ASP.NET 웹 프런트 엔드로 구성된 Service Fabric Mesh 응용 프로그램을 만듭니다.
> * 할 일 항목을 나타내는 모델을 만듭니다.
> * 백 엔드 서비스를 만들고 이 서비스에서 데이터를 검색합니다.
> * 백 엔드 서비스에 대한 모델 뷰 컨트롤러 패턴의 일부로 컨트롤러와 DataContext를 추가합니다.
> * 할 일 항목을 표시할 웹 페이지를 만듭니다.
> * 백 엔드 서비스를 식별하는 환경 변수를 만듭니다.

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [로컬로 실행되는 Service Fabric Mesh 응용 프로그램 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
