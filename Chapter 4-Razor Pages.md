Chapter 4-Razor Pages
==============================

## Exploring a typical Razor Page
```C#
// setup
public void ConfigureServices(IServiceCollection services)
{
   services.AddRazorPages();
} 

public void Configure(IApplicationBuilder app, IWebHostEnvironment env) {
   app.UseRouting();
   ...
   app.UseEndpoints(endpoints => {
      endpoints.MapRazorPages();
   });
}
```
```C#
// Category.cshtml.cs
public class CategoryModel : PageModel
{
   private readonly ToDoService _service;
   public List<ToDoListModel> Items { get; set; }

   public CategoryModel(ToDoService service) {
      _service = service;
   }

   // Note that when OnGet return void or Task, it is the same as calling Page() as below shows
   public void OnGet(string category) {
      Items = _service.GetItemsForCategory(category);
   }
   
   // just uncomment this snippet for demo purppose, of course it can't have two versions of get handlers
   public ActionResult OnGet(string category) {
      Items = _service.GetItemsForCategory(category);
      return Page();   // calling Page() causes Razor engine to use the associated view
   }
   
}
```
You can also use async http verb and custom handler as:
```C#
public class CategoryModel : PageModel {
   ...
   // async get version
   public Task OnGetAsync(string category) {
      ...
   }

   public Task OnGetRelatedAsync(string category) {
      ...
   }
}
```
Note that if you have both `OnGet` and `OnGetAsync`, asp.net core will produces a 500 error. 
`OnGetRelatedAsync` is used when the name of the handler method is specified without the `On[method]` prefix and without the Async suffix so that the
OnGetRelatedAsync method is selected using a handler value of related. This alternative handler method includes related data in its query. e.g. when users click `<a href="/handlerselector?handler=related" class="btn btn-primary">Related</a>`, it doesn't need to be query string, routing segment also applies, e.g. `@page ".../{handler}"`.
When there is a request that doesn't match, the EndpointMiddleware executes an implicit page handler instead. Implicit page handlers contain no logic, they just render the Razor view. E.g. if you sent a DELETE request to the Razor Page, an implicit handler would be executed. The implicit page handler is equivalent to `public void OnDelete() { }` 

The assocaited cshtml
```C#
@page "{category}"
@model ATypicalRazorPage.CategoryModel
@{
    Layout = null;
}

<!DOCTYPE html>

<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>ViewCategory</title>
</head>
<body>
    <h1>ToDo items:</h1>
    @if (Model.Items.Any())
    {
        <ul>
            @foreach (var item in Model.Items)
            {
                <li>@item.Title</li>
            }
        </ul>
    }
    else
    {
        <p>No items found</p>
    }
</body>
</html>
```
Note that the biggest difference bewtween Razor Pages and MVC is, Razor Pages which breaks the MVC model and associates the code with its view file. For example, Razor Pages use the PageModel derived class itself as the view model for the Razor view, by exposing the required data as properties. Conversely MVC normally uses a separate view model. 

```C#
public abstract class PageModel : IAsyncPageFilter, IFilterMetadata, IPageFilter {
   ...
   public IUrlHelper Url { get; set; }
   public virtual PageResult Page() => new PageResult();
   public virtual RedirectToActionResult RedirectToAction(string actionName) => RedirectToAction(actionName, routeValues: null);
   public virtual RedirectToActionResult RedirectToAction(string actionName, object routeValues) => RedirectToAction(actionName, controllerName: null, routeValues: routeValues);

   // you can mix MVC controllers and views together with razor Pages in the same ASP.NET Core application, so it makes sense that you should be able to 
   // redirect to Razor pages from controllers, and to controller actions from Razor pages.
   public virtual RedirectToActionResult RedirectToAction(string actionName, string controllerName, object routeValues, string fragment) {
      return new RedirectToActionResult(actionName, controllerName, routeValues, fragment) {
         UrlHelper = Url,
      };
   }
   public virtual RedirectToPageResult RedirectToPage(string pageName, string pageHandler, object routeValues, string fragment);
   public virtual StatusCodeResult StatusCode(int statusCode) => new StatusCodeResult(statusCode);
   ...
}

public class PageResult : ActionResult {   // note that HTML MVC uses ViewResult
   public PageResult();

   public string ContentType { get; set; }
   public object Model { get; }
   public PageBase Page { get; set; }
   public ViewDataDictionary ViewData { get; set; }
   public int? StatusCode { get; set; }

   public override Task ExecuteResultAsync(ActionContext context);
}

public abstract class ActionResult : IActionResult {
   public virtual void ExecuteResult(ActionContext context);
   public virtual Task ExecuteResultAsync(ActionContext context);
}

public interface IActionResult {
   Task ExecuteResultAsync(ActionContext context);
}

public class RedirectToActionResult : ActionResult, IKeepTempDataResult, IActionResult {
   ...
   public RedirectToActionResult(string actionName, string controllerName, object routeValues, bool permanent, bool preserveMethod, string fragment);

   public IUrlHelper UrlHelper { get; set; }
   public string ActionName { get; set; }
   public string ControllerName { get; set; }
   public RouteValueDictionary RouteValues { get; set; }
   ...
   public override Task ExecuteResultAsync(ActionContext context);
}
```
Note that most ActionResult classes have a helper method on the base PageModel class. They're typically named Type, and the result generated is called TypeResult. For example, the StatusCode method returns a StatusCodeResult instance

<!-- <code>&lt;T&gt;<code> -->

<!-- <div class="alert alert-info p-1" role="alert">
    
</div> -->

<!-- <div class="alert alert-info pt-2 pb-0" role="alert">
    <ul class="pl-1">
      <li></li>
      <li></li>
    </ul>  
</div> -->

<!-- <ul>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
</ul>  -->

<!-- <ul>
  <li><b></b></li>
  <li><b></b></li>
  <li><b></b></li>
  <li><b></b></li>
</ul>  -->

<!-- ![alt text](./zImages/16-1.png "Title") -->

<!-- <span style="color:red">hurt</span> -->

<style type="text/css">
.markdown-body {
  max-width: 1800px;
  margin-left: auto;
  margin-right: auto;
}
</style>

<link rel="stylesheet" href="./zCSS/bootstrap.min.css">
<script src="./zCSS/jquery-3.3.1.slim.min.js"></script>
<script src="./zCSS/popper.min.js"></script>
<script src="./zCSS/bootstrap.min.js"></script>