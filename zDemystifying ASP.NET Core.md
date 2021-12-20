Demystifying ASP.NET Core
==============================

-first iterate each chapter's to work in the future.

-how does each middleware modify response and short circuit pipeline?

-figure it out how Razor Engine proudces html response, how it run ExecuteResultAsync and how the response pass back to the next middleware and becomes its context object etc.

-How ConfigureServices use Options as
```C#
public void ConfigureServices(IServiceCollection services) {
   ...
   services.Configure<RouteOptions>(options => {
      options.AppendTrailingSlash = true;
      options.LowercaseUrls = true;
      options.LowercaseQueryStrings = true;
   });
}

public void ConfigureServices2(IServiceCollection services) {
   services.AddRazorPages()
   .AddRazorPagesOptions(opts => {
      opts.Conventions.Add(new PageRouteTransformerConvention(new KebabCaseParameterTransformer()));
      opts.Conventions.AddPageRoute(
      "/Search/Products/StartSearch", "/search-products");
   });
}
```
-why this method is in ConfigureService method not in Configure method, isn't it more suitable to configure application, why it related to DI? more important, the design pattern: 
```C#
public void ConfigureServices(IServiceCollection services) {
    services.AddControllers().ConfigureApiBehaviorOptions(options => {
        options.SuppressModelStateInvalidFilter = true;
    });
}
```
-How does IServiceProvider work, this related to DI, probably better to put it to the DI walkthrough
```C#
public void ConfigureServices(IServiceCollection services) {
    services.AddSingleton(provider => {
       ...
    });
}

public interface IServiceProvider {
   object? GetService(Type serviceType);
}
```

- Provider pattern
```C#
builder.ConfigureAppConfiguration((hostingContext, config) => {
            config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true);  // Add JsonConfigurationProvider
         };
```
-what's IHostedService?






/*   
----------------------------------------------------------------------------------------------------------------------------------------------------------
*/  DI Part:

-Chapter 13 ResponseCacheAttribute, figure it out how CreateInstance is implemented, and how `public interface IFilterMetadata {} ` realated to DI

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