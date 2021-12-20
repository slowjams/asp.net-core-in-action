Chapter 7-Rendering HTML using Razor
==============================

```C#
@page
@model IndexModel
@{
    ViewData["Title2"] = "Home page";  // ViewData property is from XXX_Home_Index : RazorPage<string[]>
}

<h4>Number of users: @Model.ExistingUsers.Count</h4>  // Model property is from XXX_Home_Index : RazorPage<string[]>
...
}
``` 
Note that the cshtml file will get compilered into XXX_Index.cs or _Pages_XXX (asp.net core 3):
```C#
public class XXX_Home_Index : RazorPage<string[]> {
   public IUrlHelper Url { get; private set; }

   public IViewComponentHelper Component { get; private set; }

   public IJsonHelper Json { get; private set; }

   public IHtmlHelper<string[]> Html { get; private set; }

   public override async Task ExecuteAsync() {
      Layout = null;
      WriteLiteral(@"<!DOCTYPE html><html><head>...");
      ...
   }
}

public abstract class RazorPage<TModel> : RazorPage {
   public TModel Model { get; }   // This is how you can use Model in Razor view
   public ViewDataDictionary<TModel> ViewData { get; set; } // This is how you can use ViewData in Razor view, but it looks like the same counterpart from PageModel will be combined with it
}

public abstract class RazorPage : RazorPageBase {
   public HttpContext Context { get; }
   public HtmlString RenderSection(string name, bool required);
   public Task<HtmlString> RenderSectionAsync(string name, bool required);
   protected virtual IHtmlContent RenderBody();
   ...
}

public abstract class RazorPageBase {
   public ITempDataDictionary TempData { get; }
   public dynamic ViewBag { get; }
   public string Layout { get; set; }
   public virtual ViewContext ViewContext { get; set; }
   public virtual ClaimsPrincipal User { get; }
   public virtual void Write(string value);
   public virtual void WriteLiteral(string value);
   public abstract Task ExecuteAsync();
   ...
}
```
-To work in the future, combine all this knowledge

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