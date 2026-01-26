---
source: https://docs.kentico.com/documentation/developers-and-admins/customization/customize-system-providers/register-custom-providers
scrape_date: 2026-01-26
---

  * [Home](/documentation)
  * [Developers and admins](/documentation/developers-and-admins)
  * [Customization](/documentation/developers-and-admins/customization)
  * [Customize system providers](/documentation/developers-and-admins/customization/customize-system-providers)
  * Register custom providers 


# Register custom providers
To replace the system implementations of [provider or helper classes](/documentation/developers-and-admins/customization/customize-system-providers) with your custom ones, use assembly attributes:
  1. Edit your provider class.
  2. Add a using statement for the **CMS** namespace:
C#
Copy
```
using CMS;
```

  3. Register your custom providers by adding assembly attributes above the class declaration:
     * `RegisterCustomProvider`
     * `RegisterCustomHelper`


The parameter of each attribute must contain the exact type of the custom class extending the given object (obtained using the `typeof` notation).
The customization automatically targets the provider object that matches the parent of the specified custom class.
C#
**Example**
Copy
```
// Registers custom providers
[assembly: RegisterCustomProvider(typeof(CustomUserInfoProvider))]
[assembly: RegisterCustomProvider(typeof(CustomMemberInfoProvider))]
```

The system now uses your customized providers instead of the default functionality.
**Enabling class discovery**
If your custom class is located in its own assembly (_Class Library_ project), you need to make sure class discovery is enabled for the project. Otherwise, the _RegisterCustom_ attributes cannot work. See [Integrate custom code](/documentation/developers-and-admins/customization/integrate-custom-code).
![]()
[]()[]()
