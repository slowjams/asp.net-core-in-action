Chapter 8-Building Forms with Tag Helpers
==============================

-It is interesting to know that a tag helper has different context when using in different control. e.g. `<label asp-for="Input.CurrencyFrom"></label>`. if you mouse hover on `asp-for`, the visual studio intellisense shows `LabelTagHelper` class, while if you mouse over `<input class="form-control" asp-for="Input.Quantity" />`, then visual studio intellisense shows `InputTagHelper` class

and

same tag helpers (actually different classes, it is just the same look on cshtml markup) do different things in different html tag as below shows:
```C#
// binding model
public class InputModel
{
   [Required]
   [Display(Name = "Your name")]
   public string FirstName { get; set; }
   
   public string SelectedValue { get; set; }
   ...
}
```
```C#
// asp-for on Labels generates the caption for labels based on the view model, it also generate a soon-to-be id(Input_FirstName) for other tag e.g. input
<label asp-for="Input.FirstName"></label> 
```
to
```C#
<label for="Input_FirstName">Your name</label>
```

```C#
// asp-for on Inputs generate the correct type, value, name, id and validation attributes for the model
<input class="form-control" asp-for="Input.FirstName" />
```
to
```C#
<input class="form-control" type="text" data-val="true" data-val-length="Maximum length is 100" data-val-length-max="100" data-val-required="The Your name field is required." id="Input_FirstName" maxlength="100" name="Input.FirstName" value="" />
```

Note that Model binding starts by looking through the sources for the key e.g "InputModel.FirstName". If that isn't found, it looks for ID without a prefix ("FirstName").

```C#
// // asp-for on Selects specifies the property in your binding model to bind to.
<select class="form-control dropdown" asp-for="Input.SelectedValue1" asp-items="Model.Items"></select>  // Model.Items has to be Microsoft.AspNetCore.Mvc.Rendering.SelectListItem instance
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