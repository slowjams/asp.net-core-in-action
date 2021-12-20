Chapter 10-DI
==============================

some source code:
```C#
public static class MvcServiceCollectionExtensions {
   public static IMvcBuilder AddControllers(this IServiceCollection services);  // for WebApi project
   public static IMvcBuilder AddControllers(this IServiceCollection services, Action<MvcOptions> configure);   // overload pattern for methods below

   public static IMvcBuilder AddRazorPages(this IServiceCollection services);  // for Razor Page project
   public static IMvcBuilder AddControllersWithViews(this IServiceCollection services);   // for MVC HTML view project
   public static IMvcBuilder AddMvc(this IServiceCollection services) {        //  for both MVC HTML view and Razor Page project
      ...
      services.AddControllersWithViews();
      return services.AddRazorPages();
   }    
}

public class MvcOptions : IEnumerable<ICompatibilitySwitch>, IEnumerable {
   public MvcOptions();

   public FormatterCollection<IOutputFormatter> OutputFormatters { get; }
   ...
}
```
```C#
public static class ServiceCollectionServiceExtensions {
   // notice this is only AddSingleton method let you pass an object (TService), there is no AddScoped or AddTransient counterpart
   public static IServiceCollection AddSingleton<TService>(this IServiceCollection services, TService implementationInstance);

   public static IServiceCollection AddSingleton<TService>(this IServiceCollection services, Func<IServiceProvider, TService> implementationFactory);
   public static IServiceCollection AddSingleton(this IServiceCollection services, Type serviceType, Type implementationType);

   public static IServiceCollection AddTransient<TService>(this IServiceCollection services, Func<IServiceProvider, TService> implementationFactory);
   ...
   public static IServiceCollection AddScoped<TService>(this IServiceCollection services, Func<IServiceProvider, TService> implementationFactory)
   ...
}
```

Some examples:
```C#
public void ConfigureServices(IServiceCollection services) {
   services.AddMvc();
   services.AddSingleton(new EmailServerSettings(   // You can only pass an already-constructed object to AddSingleton
      host: "smtp.server.com",
      port: 25
   ));

   services.AddScoped(provider => (
      new EmailServerSettings (
         host: "smtp.server.com",
         port: 25
      )
   ));

   services.AddTransient<Repository>();
   services.AddScoped(typeof(IRespository<>), typeof(DbRepository<>));
}
```
Register multiple implementations of a service:
```C#
public void ConfigureServices(IServiceCollection services) {
   services.AddControllers();
   services.AddScoped<IMessageSender, EmailSender>();
   services.AddScoped<IMessageSender, SmsSender>();
   services.AddScoped<IMessageSender, FacebookSender>();  
} 

public class UserController : ControllerBase {
   private readonly IEnumerable<IMessageSender> _messageSenders; 
   
   public UserController(IEnumerable<IMessageSender> messageSenders) { 
      _messageSenders = messageSenders; 
   }
   
   [HttpPost("register")]
   public IActionResult RegisterUser(string username) {
      foreach (var messageSender in _messageSenders) {
         messageSender.SendMessage(username);
      }

      return Ok();
   }
}
```
Note that you must use `IEnumerable<T>` as the constructor argument to inject all the registered types of a service, T. Even though this will be injected as a `T[]` array, you can’t use `T[]` , `List<T>` or `ICollection<T>` as your constructor argument.
If only one implementations is needed e.g :
```C#
public class UserController : ControllerBase {
   private readonly IMessageSender _messageSender; 
   
   public UserController(IMessageSender messageSender) { 
      _messageSender = messageSender; 
   }
   
   [HttpPost("register")]
   public IActionResult RegisterUser(string username) {
         _messageSender.SendMessage(username);
      }
      ...
   }
}
```
Then messageSender is FacebookSender since The DI container will use the last registered implementation of a service when resolving a single instance of the service.
 
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