AEM Screens component template
==============================

This project is meant as a template for customer AEM Screens component development.

Supported versions: 6.3, 6.4

Usage
-----

1. Copy the template to your own project
0. Rename the `my_component.html` to the name of your component so Sling knows how to render the component
0. Replace all `My Component` and `myComponent` references with your component name
0. Implement the dialog properties, and the edit and production markup for your component by following the comments in each file

That's it. Your component is ready to be used.


Why a special template for Screens components?
----------------------------------------------

AEM Screens has some interesting constraints that are not necessarily true for other parts of the product:
- Most Screens components need to run in fullscreen on the target digital signage devices
- Most Screens components need to be embeddable in the sequence channels to generate slideshows
- Authoring should allow editing individual components in a sequence channel, so rendering them fullscreen is out of the question


The template explained
----------------------

The template follows the usual structure for AEM Sites components that are meant to be embedded into paragraph systems, so the initial structure should be familiar:

```
my_component/
    - _cq_dialog/.content.xml
    - .content.xml
    - my_component.html
```

There are 2 important points though:

1. Your component must extend the `foundation/components/parbase` component so it can properly be used inside a sequence channel (which is just a custom parsys).
0. Your component must use the `parbase` CSS class, and it is recommended to also set your own class for easier styling.
This is done in `_cq_htmlTag/.content.xml`[[2]]:
```xml
<jcr:root xmlns:cq="http://www.day.com/jcr/cq/1.0"
          xmlns:jcr="http://www.jcp.org/jcr/1.0"
          xmlns:nt="http://www.jcp.org/jcr/nt/1.0"
          jcr:primaryType="nt:unstructured"
          class="parbase cq-Screens-myComponent"/>
```


### Edit vs. production renderer

Screens components require 2 different renderings depending on which authoring mode[[3]] is being used:

1. Production: used for preview mode, or when no mode is active (such as on a publish server)
0. Edit: used for all other authoring modes, such as edit, design, scaffolding, developer, etc.

Since we need a different renderer in _edit_ and in _production_ mode, the main renderer is actually a switch checking which authoring mode is currently active and redirecting to either `edit.html` or `production.html`.

#### Edit renderer

The default markup looks like:
```html
<div class="aem-Screens-editWrapper" data-emptytext="${'My Component' @ i18n, locale=request.locale}">

</div>
```

- `data-emptytext`[[2]]: defines the label for the component when it is not yet configured in the authoring view. If you skip that, you'll just have an empty rectangle in the UI for that component. The empty text makes sure you have at least some textual information in there to be able to properly identify the component.
- `aem-Screens-editWrapper`: a suggested CSS class that makes it easier to target the wrapper element in edit mode to apply specific styling.

#### Production renderer

The default markup looks like:
```html
<div data-duration="${properties.duration}">

</div>
```

Since most components will probably end up being included in a sequence channel, it is recommended to also add support for the `data-duration` attribute that will allow to set a specific play duration for that particular component. If missing, the play duration will just fall back to the default duration set in the sequence channel.


### Clientlibs

Since we have different renderers for both edit and production modes, chances are high we'll also need different CSS and scripts running for each mode. To make this easier, we introduced 3 client-side library categories that you can leverage in your component:

| Clientlib category                | Usage
|-----------------------------------|-------------------------------------------
| `cq.screens.components`           | Styles and scripts that are shared between both edit and production modes
| `cq.screens.components.edit`      | Styles and scripts that are only used in edit mode
| `cq.screens.components.production`| Styles and scripts that are only used in production mode


Documentation
-------------

1. https://docs.adobe.com/docs/en/aem/6-3/develop/components.html  
0. https://docs.adobe.com/docs/en/aem/6-3/develop/components/components-basics.html  
0. https://docs.adobe.com/docs/en/aem/6-3/author/page-authoring/author-environment-tools.html#Page%20Modes

[1]: https://docs.adobe.com/docs/en/aem/6-3/develop/components.html  
[2]: https://docs.adobe.com/docs/en/aem/6-3/develop/components/components-basics.html  
[3]: https://docs.adobe.com/docs/en/aem/6-3/author/page-authoring/author-environment-tools.html#Page%20Modes
