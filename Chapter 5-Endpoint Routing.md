Chapter 5-Endpoint Routing
==============================

The EndpointMiddleware stores the registered routes and endpoints in a dictionary, which it shares with the RoutingMiddleware

```C#
// a Custom Middlware
public async Task Invoke(HttpContext context) {
   Microsoft.AspNetCore.Http.Endpoint test = context.Features.Get<IEndpointFeature>()?.Endpoint;   // <--- test is null here before await next(context);

   await next(context);
   var test2 = EndpointHttpContextExtensions.GetEndpoint(context);
   if (test2.Metadata.FirstOrDefault(i => i is PageActionDescriptor) is PageActionDescriptor pageDescriptor2) {
      string pagePath = pageDescriptor2.RelativePath;   // pagePath is '/Pages/Product.cshtml'
   }
}

// Startup.cs
app.UseMiddleware<QueryStringMiddleWare>();   // this middleware is used beofre Routing Middleware, so test is null,
                                              // but after await next(context); test2 is the endpoint
app.UseRouting();
app.UseEndpoints(endpoints => {
   endpoints.MapRazorPages();
});
```

Note that it is RoutingMiddleware's job to select an endpoint (razor page e.g. index.cshtml), and it is EndpointMiddleware's job to select which page hander (method) to execute based on `On{verb}[handler][async]` pattern.

RoutingMiddleware also retrieve and store route values in `HttpContext` object's request. So if you put the `app.UseMiddleware<QueryStringMiddleWare>()` after `app.UseRouting();` and put a debugger on that, you will see `HttpContext.HttpRequest.RouteValues` (which is `RouteValueDictionary`) has been filled with value:
```C#
// request: https://localhost:44387/category/Simple whose page cstml has @page "{category}"
Keys[0] = "page"
keys[1] = "category"

Values[0] = "/Category"
Values[1] = "Simple"
```

## Generating URLs for a Razor Page

```C#
public class IndexModel : PageModel 
{
   public void OnGet() {
      var url = Url.Page("Currency/View", new { code = "USD" }); // Url property is from PageModel, and Page is an extension method on IUrlHelper
   }
}

public interface IUrlHelper {
   ActionContext ActionContext { get; }
   string? Action(UrlActionContext actionContext);
   string? Content(string? contentPath);
   bool IsLocalUrl(string? url);
   string? Link(string? routeName, object? values);
   string? RouteUrl(UrlRouteContext routeContext);
}

public static class UrlHelperExtensions {
   ...
   public static string Page(this IUrlHelper urlHelper, string pageName, object values) => Page(urlHelper, pageName, pageHandler: null, values: values);
   
   public static string Page(this IUrlHelper urlHelper, string pageName, string pageHandler, object values, string protocol, string host, string fragment) {
      ... 
      return urlHelper.RouteUrl(...);
   }

   public static string Action(this IUrlHelper helper, string action, string controller, object values) {
      ...
      return helper.Action(new UrlActionContext(...));
   }
}
```
Similar approach for MVC controllers:
```C#
public class CurrencyController : Controller {
   [HttpGet("currency/index")
   public IActionResult Index() {
      var url = Url.Action("View", "Currency", new { code = "USD" }); 
      return Content(${"The URL is {url}");
   }

   [HttpGet("currency/view/{code}")
   public IActionResult View(string code) {
      ...
   }
}
```
You can also generate URLs with `ActionResult`:
```C#
public class CurrencyModel : PageModel {
   public IActionResult OnGetRedirectToPage() {
      return RedirectToPage("Currency/View", new { id = 5 });
   }
}
```
Note that if you need to generate URLs from parts of your application outside of the Razor Page or MVC infrastructure, then you won’t be able to use the IUrlHelper helper or an ActionResult. Instead, you can use the `LinkGenerator` class:
```C#
namespace Microsoft.AspNetCore.Routing {
   public abstract class LinkGenerator {
      public abstract string? GetPathByAddress<TAddress>(HttpContext httpContext, TAddress address, RouteValueDictionary values, RouteValueDictionary? ambientValues = null, PathString? pathBase = null, FragmentString fragment = default, LinkOptions? options = null);
      ...
   }
}
```


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