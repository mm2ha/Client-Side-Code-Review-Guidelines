# Alarm.com Client Side Code Review Guidelines

*Things to look for and check when developing and code reviewing client side code*

*Reference examples of good code from the Customer Site include:*
- **UpdateBrowserAlert** (*CustomerDotNet/App_UserControls/UpdateBrowserAlert/...*)
  - UpdateBrowserAlert.ascx
  - UpdateBrowserAlert.css
  - UpdateBrowserAlert.js
- **RaygunControl** (*CustomerDotNet/App_UserControls/RaygunControl/...*)
  - RaygunControl.ascx
  - RaygunControl.css
  - RaygunControl.js
- **PropertyInformation** (*CustomerDotNet/Profile/PropertyInformation/...*)
  - PropertyInformation.aspx
  - PropertyInformation.css
  - PropertyInformation.js
- **LoginInformation** (*CustomerDotNet/Profile/LoginInformation/...*)
  - LoginInformation.aspx
  - LoginInformation.css
  - LoginInformation.js

###### If you have any questions, feel free to email mmiklo@alarm.com

## Javascript
- Make sure to adhere to the [JavaScript Style Guide](https://github.com/mm2ha/javascript). 
  - Good working examples from the Customer Site that adhere to these styles are:
    - CustomerDotNet/App_UserControls/UpdateBrowserAlert/...
      - UpdateBrowserAlert.ascx
      - UpdateBrowserAlert.css
      - UpdateBrowserAlert.js
    - CustomerDotNet/App_UserControls/RaygunControl/RaygunControl.js
    - CustomerDotNet/Profile/PropertyInformation/PropertyInformation.js 
    - CustomerDotNet/Profile/LoginInformation/LoginInformation.js
  - Especially look for proper function names
    - "public" functions: `myPublicFunctionName`
    - "private" functions: `_myPrivateFunctionsName`
    - Saving context: use `_this = this;` instead of `that = this`
      - We figured this makes more sense
  - Comparisons, hoisting, objects, etc
  - Take advantage of linters in the repository for WebEssentials and ReSharper
  - Comma at the end of the line if you are splitting object or function definitions
  - Operator at the beginning of the line if you have a multi-line statement
- Always place JavaScript code into a separate JavaScript file, never inside html, aspx or ascx files
  - Use pods structure for placing files
- Never put JavaScript into Code Behind or any server side code. Never. Just don't do it. Ever.
  - If you see it somewhere, please fix it.
- Use of jQuery `.data` function instead of `.attr` for any `data-property-name` attribute
- Cache all jQuery form elements in `this.controls` object; predefine it in `_init` function as `this.controls = {};`
  - then later on you would do: `this.controls.mySelect = this.$element.find('select.my-select');`
- Write JavaScript code as prototypes classes. Look at UsersControl.js, SystemInfo.js or PropertyInformation.js for help. Eventually we will move to ES6 modules, but this will help us with the transition.
- Do not add global variables into the `DOM`. Add them as data properties to the root element of the prototype.
- Use function `AddResource` in both `BaseUserControl` and `BaseCustomer` page to add resources (translations) to the client side. They will be available in `$.systemDefaults.resources.<nameSpace>.resourceName`
  - `nameSpace` is usually the name of the class with initial lower case letter. It can be overridden via `ResourceNameSpace`; all non-alphanumeric characters will always be removed and the first letter will be made lowercase
  - See [Resources / Translations / Localizations](https://github.com/mm2ha/javascript/blob/master/README.md#resourcesTranslations) section for a bit more detail
- Make sure it passes JSHint and JSCS testing and does not throw any errors
- Make sure functions are properly commented in the [JSDoc style](http://usejsdoc.org)
- Use [ADC Components](http://alrm-web1-dev/adc-framework/adc.html) page when working with ADC specific components

<br/>
## CSS

- Use [ADC Bootstrap Style](http://alrm-web1-dev/adc-framework/layout.html) (the whole guide) for building HTML
- Always place CSS code into separate CSS file, never inside `html`, `aspx` or `ascx` files
  - Use pods structure for placing files
- Use `my-class-name` for class declarations
- Do not use `id` identifiers (#) while we are still using code behind generation; asp.net generates random ids for elements; it is ok to use them in .hbs templates once we have them
- Order CSS elements within a definition alphabetically. This improves visual recognition of what is there and is much easier to maintain. WebEssentials has a shortcut to do this for you
- Use [ADC Components](http://alrm-web1-dev/adc-framework/adc.html) page when working with ADC specific components
- Make sure your CSS cannot interfere and affect any other elements on the page
  - For example, `label {color: red;}` is very bad. It is going to affect all labels on the page, and it does not matter that you put this into separate file
  - Try to put a defining class around the element you are working with. For example, for 
    `UsersControl` we have `users-control-wrap`. Then we can define any CSS for `UserControl` as `.users-control-wrap <identifier>` and it will only affect the elements that we want
- Do not create one-off colors or styles. If it is not worth adding to the whole style and elements guide, then it should not be its own separate thing. Stay consistent with what we have and what we do. If you are not sure, ask.
- Do not try to reinvent the wheel, [use helper classes instead](http://alrm-web1-dev/adc-framework/adc.html#helper-classes)
  - This will also help when we change the overall style as everything will get altered; if you are defining your own styles for the same CSS, that code will look very out of place

<br/>
## Ajax Web Services (Customer site)

- Extend ```BaseAjaxWebservice``` class
- Check if there is a session if the web service needs it
- Check if the context has sufficient permissions for the action
- Never assume anything about the input, always make sure you clean and validate
  - Always use DBHelper.ConvertDbSafe on any input parameter of type string
  - Always validate all data against what we expect to receive
- Good structure for webservices for one instance:
  - Get data webservice
    - Returns data to be displayed on the page
    - For example ```GetPropertyData()``` or ```GetTimezone()```
  - Validate data webservice
    - Performs validation on all inputs and returns parameter errors if necessary
    - For example ```ValidatePropertyData()``` or ```ValidateTimezone()```
  - Set data webservice
    - Sets data back to the database through business objects based on the parameters
    - Can utilize validation by calling the validate data webservice to avoid code duplication
    - For example ```SetPropertyData()``` or ```SetTimezone()```
- Always write unit tests for webservices. There is really no reason not to.
  - We have a super simple way to unit test web services
  - For example ```PropertyDataTest``` in AlarmUnitDbTest
