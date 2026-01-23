---
source: https://docs.kentico.com/guides/development/get-started/use-abstraction-and-enterprise-level-architecture-in-xperience-projects
scrape_date: 2026-01-22
---

  * [Home](/guides)
  * [Development](/guides/development)
  * [Get started](/guides/development/get-started)
  * Use abstraction and enterprise-level architecture in Xperience projects 


# Use abstraction and enterprise-level architecture in Xperience projects
As enterprise projects grow over time, with several developers working together, having an organized architecture becomes increasingly important for automated testing and code maintenance. However, digital experience platforms like Xperience by Kentico offer a wide array of features spanning many parts of the project, making it difficult to decide where to draw the lines between layers of a project and minimize dependence on infrastructure.
This guide sheds light on this topic, demonstrating an approach similar to [_Ports and adapters_ or _Clean architecture_](https://www.youtube.com/watch?v=yF9SwL0p0Y0). It covers:
  * Benefits of abstraction in enterprise projects
  * Advantages of layering for code maintenance
  * Tradeoffs involved in enterprise architecture
  * Examples of displaying content with clean architecture in Xperience by Kentico


This guide is more conceptual and does NOT have a corresponding repository in the [Kentico GitHub](https://github.com/Kentico).  
If you are familiar with our other guides, e.g. [the _Activities and marketing_ series](/guides/development/activities-and-marketing), note that there are differences between the examples they reference and the architecture presented in this guide.
Enterprise architecture sacrifices initial development overhead and readability in exchange for easier code maintenance and automated testing. This makes it ideal for large, long-lived production projects, but not for proofs of concept that demonstrate how to use various Kentico features in isolation.
## Prerequisites
Familiarity with [dependency injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection)
Basic understanding of the [ASP.NET MVC pattern](https://dotnet.microsoft.com/en-us/apps/aspnet/mvc)
## Examine abstraction in the context of architecture
When defining the concept of abstraction in programming, most sources focus on simplification— discarding irrelevant information to work with only the data you need. Since modern software development is rarely from scratch, abstraction often manifests through new, lean classes that your business logic uses instead of more generalized, featureful types provided by whatever system or service you are working in or integrating with.
In such cases, the code that retrieves objects of a complex type from the external system is best kept in one place. This allows complex types to be converted to the simpler type in a centralized location, rather than everywhere data is accessed. Doing this for every object type in the external system results in a whole **layer of abstraction** between the business logic and data access.
This situation is often left out of simple definitions, because it is difficult to wrap into a concise description of one or two sentences, and relates to a specific scenario. However, it has significant benefits.
One central location for the data access and conversion of an external system’s type means that any breaking changes introduced by that external system only need to be fixed in one place, rather than everywhere your business logic accesses data from that system.
📕 For example
Imagine your application connects to a third-party commerce service to sell books and e-books. The service’s API may return _Product_ objects, which include a property _ProductSKUNumber_ and Product _Weight_.
One year after your site goes live, the commerce service decides to move _ProductSKUNumber_ to a class called _CommonProductProperties_ and _ProductWeight_ to a class called  _PhysicalProductProperties_.
If you have code sprinkled throughout your project that queries the commerce service’s APIs for Product objects and access their _ProductSKUNumber_ and _ProductWeight_ properties directly, you’ll have a major headache on your hands to update all of the references.
However, if you have an abstraction layer, with a class that queries the API and filters on a few pre-defined parameters, then converts the _Product_ objects to a custom _MyProduct_ class, then you only have to change a few methods, and how the resulting data is mapped to  _MyProduct_ objects.
This layer in between also makes it easier to swap in fake classes for automated testing, or even to change tools completely, especially if your business logic uses some kind of dependency inversion to dynamically get a class that provides it with abstracted objects. If the business layer is completely separated from data access in this way, it doesn’t need to worry about how the objects are populated.
### The repository pattern
The [repository pattern](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design#the-repository-pattern) is a specific example of the data access abstraction discussed above.
It involves creating **repository interfaces** , which define methods that return abstracted types. These classes are defined in your application and independent of any third-party system.
The method signatures of these interfaces specify the data and operations your business logic needs regarding that type.
**Repository classes** that implement these interfaces are resolved or injected dynamically, and can use any data access logic necessary to meet the requirements defined in the repository interface.
It is one of the most popular ways to implement this kind of layer of abstraction, and will be included in the example later in this guide.
### Drawbacks
While creating a layer of abstraction between business and data access logic may provide important benefits, it is not without drawbacks:
#### ⚠ Development overhead
  * If you’re not using the repository pattern, it’s fairly straightforward to create a page that retrieves and displays some data. With the repository pattern, though, this same process involves the following steps:  
1. Create a model with only the information you need  
2. Define a repository interface with several methods for working with objects  
3. Implement that interface with data access code, and convert the data to fit your model
Similar headaches can occur when requirements change in the future, and all of these things must be updated.


#### ⚠ Readability and onboarding
  * When a new developer looks at the solution, they may find it more complicated to jump between several files to accomplish the same task. They may get used to the pattern, and eventually be able to understand the abstracted code quickly, but this means your company may have to invest additional time and resources in getting the developer up to speed.


Depending on your team, project requirements, and budget, you may find it appropriate to compromise, and find some other way to reduce the impact of changes in your project.
**An example of a compromise**
The `ContentItemRetrieverService` class in the [Training guides repository](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/ContentItemRetrieverService.cs) is an example of somewhat-centralized data access, which works to mitigate some issues while not going quite as far as a full-blown abstraction layer.
In this case, the object types it returns are Xperience content types, whose properties are determined by the organization who owns the site rather than Kentico. Thanks to this, it avoids some of the typical risk, while centralizing as much of the _Content query_ logic as possible.
If the _Content query_ API has breaking changes in the future, you only need to refactor the service.
The fact that it is an implementation of an interface registered with the dependency injection container means it can still be easily swapped out with a fake class in testing scenarios.
## Understand clean architecture
Details vary slightly from place to place, but terms like _ports and adapters, clean architecture,_ and _domain-driven design_ all relate to a certain way of thinking about architecture in software projects.
Much like the separation between business logic and data access offered by the repository pattern, this type of architecture seeks to break the system into more modular, independent layers.
It takes the concept a few steps further by using project structure to enforce the separation, rather than relying on developers to follow rules. It separates projects in such a way that creating dependencies to the wrong layer is not possible. It also aims to put the application’s business logic and the objects it uses at the center of its design, with other layers being molded to meet its requirements.
### The layers
This type of architecture typically has three layers: _Domain_ , _Infrastructure_ , and _UI_.
[![](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/Layers.jpg)](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/Layers.jpg)
#### 🧱 Domain
  * The domain layer contains the types or **domain models** that your business logic works with. These are typically referred to as _Value objects, Entities,_ and _Aggregates_. These are all classes that are distinguished on a conceptual level.  
An _entity_ represents a thing, while a  _value object_ represents an attribute of a thing, and an _aggregate_ represents a collection of entities and value objects.
For each _Aggregate root_ , or aggregate that represent meaningful concepts in the domain, the domain layer contains a [repository interface](#the-repository-pattern). This emphasizes data consistency over efficiency in data retrieval, ensuring that all relevant data is present, and parent and child entities can each be updated based on changes to the other.
The domain layer also contains business logic, using interfaces that can be implemented by other layers. This includes things like events, event handlers, and domain services.
Domain layer project(s) should not reference projects from the other layers. The domain layer should be the central piece on which the others depend.
**Shared kernel**
If the application serves a wide enough variety of users to require [bounded contexts](https://martinfowler.com/bliki/BoundedContext.html) within the domain layer, pieces shared between these contexts can be separated into a shared kernel, but it is a best practice in .NET development to distribute this kernel as a NuGet package, rather than adding a project dependency to the domain projects.


#### 🧱 Infrastructure
  * The infrastructure layer is a little more self-explanatory, as it relates to the infrastructure on which your application is built. It holds implementations of the domain layer’s repositories, fulfilling their requirements by reaching out to the various platforms and services that your app is built upon. **It should be the only layer that deals directly with any of these platforms and services.** These include in-house resources like databases and email servers, as well as integrations with any microservices that don’t directly relate to the UI.
If you have services and other functionality that relates to the infrastructure and is only used within the infrastructure projects, both their interfaces and implementations should be housed in the infrastructure projects.
Depending on the breadth of platforms and services that your application touches, you might break this layer into multiple projects, but it is important that they only reference the domain project, and never any projects from the UI layer.


#### 🧱 UI
  * The UI layer is also somewhat self-explanatory, holding the code that renders and manages the user interface. It uses dependency injection or some other _inversion of control (IOC)_ solution to resolve the interfaces for repositories, services, etc. specified in the domain layer without having a project reference to the infrastructure layer, and uses them to retrieve and manipulate data according to the UI’s needs.
The basic implementation of dependency injection built into .NET does not handle this kind of resolution currently, so a custom-built or third-party IOC solution will be necessary to avoid referencing the infrastructure project(s) from the UI project(s) in .NET solutions.
Because there is no project reference, it is impossible for the UI layer to misuse classes from the infrastructure layer. If the infrastructure layer were to be swapped out for a test project full of fakes or even a completely different system, as long as it properly implements any necessary interfaces from the domain layer, there would be no need to change anything in the UI layer. This is one of the key benefits of this architecture.
This layer should also contain any integrations that relate specifically to the UI, for example, a web site’s integrations with web analytics services.


### Tradeoffs
When compared to projects where everything is mixed together, this type of architecture has some clear advantages:
  * **Organization**
    * If the project grows and expands over the course of several years, the structure makes it much easier to manage the growing number of files.
  * **Automated testing**
    * The modularity of the layers allows for easy inclusion of automated testing into the solution. Everything being based on interfaces with clearly defined requirements and resolved dynamically makes unit and integration tests easier, opening the door to things like full-fledged test-driven development.
  * **Resilience to change**
    * Clean architecture keeps the areas affected by major changes as confined as possible, as long as the underlying business needs and functionality do not change.  
The infrastructure layer, dealing with the various platforms and services your solution incorporates, is the only place affected by upgrades and migrations to different platforms.  
The UI layer, controlling the presentation and interactivity of your application, is the only place you need to make changes for visual overhauls and restyling of the application.


However, like any approach, clean architecture also has drawbacks. 
They are essentially the same as those mentioned in [the earlier section on abstraction of data access](#drawbacks).
The modular design of clean architecture projects requires a lot of additional development time at the start of a project, along with anytime you change domain objects and business logic.
🔤 For example
If you have a web project, and you decide to change which properties are in the domain model, you must also consider if these changes affect your repository interface. For example, if you decide to incorporate letters into formerly numerical IDs, then the signature of a `GetProductById` method in your repository interface will need to be updated.
You must also change the infrastructure layer’s repository, so that the values needed to populate the changed properties are retrieved from the CMS. Then you must update the function that maps the CMS API’s objects to the domain object type to account for this change. You also need to account for the changes in write methods, and any business logic implementations that exists in the repository and its related services.
After that, you need to update the UI layer, everywhere it retrieves objects of that type, anywhere it maps them to its own view models or other DTOs, and anywhere it displays or takes input for those properties.
The issues of readability from earlier also apply. The complicated architecture can be confusing for developers who are not familiar with the pattern, so your team will likely have to spend more resources training junior developers.
## Implement an example
Now let’s look over an example of these concepts in Xperience by Kentico. It demonstrates a three-layered approach to displaying a listing of university courses.
The guide includes a general overview of the following, with some supporting code snippets:
  * Defining the domain models and a repository interface
  * Implementing the interface with data access logic and translating the data to the domain models
  * Creating a web site to display the UI and translating from domain models to view models.


The sample scenario revolves around university courses. A course, like _Accounting 101_ , can be taught to several different groups of students by several different professors over the course of multiple terms. These specific instances of the course are called _sections_. There are also _professors,_ _lessons,_ and _materials_ , associated with the sections.
[![](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/course+class3.jpg)](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/course+class3.jpg)
This example provides an overview of an application that displays a listing page of courses as well as course details, following the clean architecture as closely as possible.
### Plan how projects will correspond to layers
Let’s break this example into projects that correspond to the different layers of enterprise architecture.
Each project will be discussed in greater detail in the next sections.
For the sake of this guide about abstraction, _TrainingGuides.Abstraction_ as the name of the solution, and as a prefix for the projects within:
  * [Domain layer](#create-the-domain-layer)
    * **TrainingGuides.Abstraction.Core** contains all of the domain models (e.g., Professor, Section) and the repository interface.
  * [Infrastructure layer](#implement-the-infrastructure-layer)
    * **TrainingGuides.Abstraction.Infrastructure** contains the implementation of the repository interface, along with a service it uses to retrieve content form Xperience.
    * **TrainingGuides.Abstraction.Generated** contains generated classes from Xperience that correspond to content types.
  * [UI layer](#add-the-ui-layer)
    * **TrainingGuides.Abstraction.Web** contains the display logic for the application’s web site.


### Create the domain layer
The domain layer of our sample has only one project -  _TrainingGuides.Abstraction.Core._
This project contains the classes of all the domain models, as well as the interface for the Course repository.
#### Decide the structure
[![](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-9_10-18-49.png)](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-9_10-18-49.png)
The project is organized first by business concern, then by technical concern. We recommend [alternating between the two](https://xperience-by-kentico-styleguide.netlify.app/guides/solution-setup/#use-feature-folders-vertical-slice-architecture) as you add deeper levels to your directories. It will help point developers in the right direction when they need to make changes.
Classes directly related to courses are in the _Courses_ folder.  
`Download`, which represents a downloadable file optionally used by the `Material` class _,_ can be used outside of the context of courses. (For instance, the admission department of the university might have a downloadable map of the university campus for prospective students taking tours.)  
For this reason, _Download_ is stored beneath the  _Shared_ folder.
#### Understand the relationships
Let’s go over the relationships between the models for this example:
The `Course` class contains a collection of `Section` objects, among other properties. `Section`, in turn, contains collections of `Professor` and `Lesson` objects. Multiple professors are allowed per section, in case any of them are co-taught. Each `Lesson` in a section has scheduling information, as well as properties describing what the lesson is about and a collection of `Material` objects, representing learning materials the students should look at before or during the lecture. `Material` contains properties for linking to an external source, in case the learning material is hosted elsewhere, as well as a `Download` object, in case it is a downloadable file.
[![](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/courseclassesdetailed2.jpg)](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/courseclassesdetailed2.jpg)
You can look over the code samples below to see these classes and their properties in full.
C#
**Course.cs**
Copy
```
namespace TrainingGuides.Abstraction.Core.Courses.Models;

public class Course
{
    public string? Title { get; set; }
    public string? Department { get; set; }
    public int Code { get; set; }
    public string? Description { get; set; }
    public string? Location { get; set; }
    public int Credits { get; set; }
    public IEnumerable<Section>? Sections { get; set; }
}
```

C#
**Section.cs**
Copy
```
namespace TrainingGuides.Abstraction.Core.Courses.Models;

public class Section
{
    public int Code { get; set; }
    public IEnumerable<Professor>? Professors { get; set; }
    public IEnumerable<Lesson>? Lessons { get; set; }
}
```

C#
**Professor.cs**
Copy
```
namespace TrainingGuides.Abstraction.Core.Courses.Models;

public class Professor
{
    public string? Title { get; set; }
    public string? GivenName { get; set; }
    public string? Surname { get; set; }
    public string? Photo { get; set; }
}
```

C#
**Lesson.cs**
Copy
```
namespace TrainingGuides.Abstraction.Core.Courses.Models;

public class Lesson
{
    public string? Title { get; set; }
    public string? Description { get; set; }
    public DateTime Start { get; set; }
    public DateTime End { get; set; }
    public IEnumerable<Material>? Materials { get; set; }
}
```

C#
**Material.cs**
Copy
```
using TrainingGuides.Abstraction.Core.Shared.Models.Downloads;

namespace TrainingGuides.Abstraction.Core.Courses.Models;

public class Material
{
    public string? ExternalLinkText { get; set; }
    public string? ExternalLinkAltText { get; set; }
    public string? ExternalLinkUrl { get; set; }
    public Download? Download { get; set; }
}
```

C#
**Download.cs**
Copy
```
namespace TrainingGuides.Abstraction.Core.Shared.Models.Downloads;

public class Download
{
    public string? Title { get; set; }
    public string? Description { get; set; }
    public string? FileName { get; set; }
    public string? Url { get; set; }
}
```

#### Define the functionality
Now that we have the classes we need to work with, let’s decide what functionality we need. For the sake of this example, let’s consider  _Course_ to be the aggregate root and create a repository for it.
During this part of the process, try not to think about Xperience by Kentico, or the SQL Server database, or the site that’s going to be displaying the courses. Focus solely on what makes sense from a business perspective.
To begin with, the Course repository should have a method for getting a specific course. Whatever infrastructure the application utilizes is bound to have some kind of numerical Id, so add the signature for a `GetById` method.
A method that returns a collection of all courses will come in handy for students seeing what the school has to offer, so add a method signature called `GetAll`. It can be left up to the infrastructure layer to decide which courses are valid and should be displayed in the “all” listing in the implementation that fulfills this method.
The Course repository should also be able to retrieve some kind of more specific listing. For example, all courses belonging to a specific department, all courses with open sections this semester, or all courses of the 300 level. Let’s say that these listings need a numerical Id, and let the infrastructure layer figure out how to fulfill that. Add a method signature called `GetListingById`.
C#
**ICourseRepository.cs**
Copy
```
using TrainingGuides.Abstraction.Core.Courses.Models;

namespace TrainingGuides.Abstraction.Core.Courses.Repositories;

public interface ICourseRepository
{
    Task<Course> GetById(int id);
    Task<IEnumerable<Course?>> GetAll();
    Task<IEnumerable<Course>> GetListingById(int id);
}
```

**Generic repositories**
This guide creates `ICourseRepository` and its implementation `CourseRepository` as a proof of concept.
However, you may encounter situations where several of your repositories, or all of them, have the same functionality, but for different object types.
In this case, you can use a generic type parameter to have several implementations based on one repository interface.
For example,
C#
**IRepository.cs**
Copy
```
using TrainingGuides.Abstraction.Core.Courses.Models;

namespace TrainingGuides.Abstraction.Core.Courses.Repositories;

public interface IRepository<T> where T : class
{
    Task<T> GetById(int id);
    Task<IEnumerable<T>> GetAll();
    Task<IEnumerable<T>> GetListingById(int id);
}
```

C#
**CourseRepository.cs**
Copy
```
...
public class CourseRepository : IRepository<Course>
{
    ...
    Task<Course> GetById(int id)
    {
        //Use the Xperience Content Query to get content of whatever content type(s) correspond to Course
        //Then map the data from Xperience to Course's properties
        ...
    }
    ...
}
```

It may even be possible, depending on your scenario, to make a generic implementation:
C#
**Repository.cs**
Copy
```
...
public class Repository<T> : IRepository<T>
{
    ...
    Task<T> GetById(int id)
    {
        //Use the Xperience API to get content
        //Reference some kind of mapping to figure out which content type(s) correspond to the provided domain model
        //Also reference a mapping to determine which of properties from the Xperience type correspond to the properties of the domain model.
        ...
    }
    ...
}
```

### Implement the infrastructure layer
The infrastructure layer has two projects:
  * **TrainingGuides.Abstraction.Generated**  
This project is holds classes that are created by Xperience by Kentico’s [code generation tool](/documentation/developers-and-admins/api/generate-code-files-for-system-objects), and any customizations made to them through partial classes.
**Generated files project**
It is not necessary to keep them in a separate project, but we recommend doing so. The extra level of separation reduces the chance of developers accidentally modifying the files, only to have their changes overwritten the next time the files are regenerated.
It also makes it possible for any other projects that use Xperience objects to do so without referencing _all_ of the infrastructure code.
  * **TrainingGuides.Abstraction.Infrastructure** This project contains the main infrastructure logic of the application. For this example, that means it contains the Xperience-specific implementation of the repository interface from the Core project, along with a service that it uses.  
In a full fledged project, it would contain implementations of all the interfaces from the domain layer.


#### Decide the structure
As with the _Core_ project, the _Infrastructure_ project is organized by business concern first, then technical concern.
[![](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-17_17-2-49.png)](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-17_17-2-49.png)
It has fewer files, as the generated classes that loosely correspond to the domain models are stored separately.
#### Examine the generated files
The files in the Generated project are sorted according to technical concern, which is most conducive to the command line parameters used to generate them. Xperience doesn’t know which content types relate to groups of features.
[![](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-17_17-5-56.png)](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-17_17-5-56.png)
**Differences from the domain**
The reusable content types that hold the structured data of the objects have the _Item_ suffix, so that code files that use domain models and generated content item classes do not have naming collisions.
Similarly, the corresponding web page types have the _Page_ suffix.
There are also some content types that don’t directly correspond to domain models, namely `CourseListingPage`, `DepartmentItem`, and `AssetItem`. 
You can look through the snipptes below to see the specifics of the generated classes.
C#
**CourseListingPage.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.Websites;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a page of type <see cref="CourseListingPage"/>.
    /// </summary>
    public partial class CourseListingPage : IWebPageFieldsSource
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Generated.CourseListingPage";

        /// <summary>
        /// Represents system properties for a web page item.
        /// </summary>
        public WebPageFields SystemFields { get; set; }

        /// <summary>
        /// CourseListingCourses.
        /// </summary>
        public IEnumerable<WebPageRelatedItem> CourseListingCourses { get; set; }
    }
}
```

C#
**CoursePage.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.Websites;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a page of type <see cref="CoursePage"/>.
    /// </summary>
    public partial class CoursePage : IWebPageFieldsSource
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Generated.CoursePage";

        /// <summary>
        /// Represents system properties for a web page item.
        /// </summary>
        public WebPageFields SystemFields { get; set; }

        /// <summary>
        /// CoursePageContent.
        /// </summary>
        public IEnumerable<CourseItem> CoursePageContent { get; set; }
    }
}
```

C#
**AssetItem.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.ContentEngine;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a content item of type <see cref="AssetItem"/>.
    /// </summary>
    public partial class AssetItem
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Abstraction.AssetItem";

        /// <summary>
        /// Represents system properties for a content item.
        /// </summary>
        public ContentItemFields SystemFields { get; set; }

        /// <summary>
        /// AssetItemFile.
        /// </summary>
        public ContentItemAsset AssetItemFile { get; set; }

        /// <summary>
        /// AssetItemTitle.
        /// </summary>
        public string AssetItemTitle { get; set; }

        /// <summary>
        /// AssetItemDescription.
        /// </summary>
        public string AssetItemDescription { get; set; }
    }
}
```

C#
**CourseItem.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.ContentEngine;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a content item of type <see cref="CourseItem"/>.
    /// </summary>
    public partial class CourseItem
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Generated.CourseItem";

        /// <summary>
        /// Represents system properties for a content item.
        /// </summary>
        public ContentItemFields SystemFields { get; set; }

        /// <summary>
        /// CourseItemTitle.
        /// </summary>
        public string CourseItemTitle { get; set; }

        /// <summary>
        /// CourseItemDepartment.
        /// </summary>
        public IEnumerable<DepartmentItem> CourseItemDepartment { get; set; }

        /// <summary>
        /// CourseItemCode.
        /// </summary>
        public int CourseItemCode { get; set; }

        /// <summary>
        /// CourseItemDescription.
        /// </summary>
        public string CourseItemDescription { get; set; }

        /// <summary>
        /// CourseItemSections.
        /// </summary>
        public IEnumerable<SectionItem> CourseItemSections { get; set; }

        /// <summary>
        /// CourseItemCredits.
        /// </summary>
        public int CourseItemCredits { get; set; }
    }
}
```

C#
**DepartmentItem.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.ContentEngine;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a content item of type <see cref="DepartmentItem"/>.
    /// </summary>
    public partial class DepartmentItem
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Generated.DepartmentItem";

        /// <summary>
        /// Represents system properties for a content item.
        /// </summary>
        public ContentItemFields SystemFields { get; set; }

        /// <summary>
        /// DepartmentItemTitle.
        /// </summary>
        public string DepartmentItemTitle { get; set; }

        /// <summary>
        /// DepartmentItemCode.
        /// </summary>
        public string DepartmentItemCode { get; set; }

        /// <summary>
        /// DepartmentItemHead.
        /// </summary>
        public IEnumerable<ProfessorItem> DepartmentItemHead { get; set; }
    }
}
```

C#
**LessonItem.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.ContentEngine;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a content item of type <see cref="LessonItem"/>.
    /// </summary>
    public partial class LessonItem
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Generated.LessonItem";

        /// <summary>
        /// Represents system properties for a content item.
        /// </summary>
        public ContentItemFields SystemFields { get; set; }

        /// <summary>
        /// LessonItemTitle.
        /// </summary>
        public string LessonItemTitle { get; set; }

        /// <summary>
        /// LessonItemDescription.
        /// </summary>
        public string LessonItemDescription { get; set; }

        /// <summary>
        /// LessonItemStart.
        /// </summary>
        public DateTime LessonItemStart { get; set; }

        /// <summary>
        /// LessonItemEnd.
        /// </summary>
        public DateTime LessonItemEnd { get; set; }

        /// <summary>
        /// LessonItemMaterials.
        /// </summary>
        public IEnumerable<MaterialItem> LessonItemMaterials { get; set; }
    }
}
```

C#
**MaterialItem.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.ContentEngine;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a content item of type <see cref="MaterialItem"/>.
    /// </summary>
    public partial class MaterialItem
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Generated.MaterialItem";

        /// <summary>
        /// Represents system properties for a content item.
        /// </summary>
        public ContentItemFields SystemFields { get; set; }

        /// <summary>
        /// MaterialItemExternalLinkText.
        /// </summary>
        public string MaterialItemExternalLinkText { get; set; }

        /// <summary>
        /// MaterialItemExternalLinkAltText.
        /// </summary>
        public string MaterialItemExternalLinkAltText { get; set; }

        /// <summary>
        /// MaterialItemExternalLinkUrl.
        /// </summary>
        public string MaterialItemExternalLinkUrl { get; set; }

        /// <summary>
        /// MaterialItemDownload.
        /// </summary>
        public IEnumerable<AssetItem> MaterialItemDownload { get; set; }
    }
}
```

C#
**ProfessorItem.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.ContentEngine;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a content item of type <see cref="ProfessorItem"/>.
    /// </summary>
    public partial class ProfessorItem
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Generated.ProfessorItem";

        /// <summary>
        /// Represents system properties for a content item.
        /// </summary>
        public ContentItemFields SystemFields { get; set; }

        /// <summary>
        /// ProfessorItemTitle.
        /// </summary>
        public string ProfessorItemTitle { get; set; }

        /// <summary>
        /// ProfessorItemGivenName.
        /// </summary>
        public string ProfessorItemGivenName { get; set; }

        /// <summary>
        /// ProfessorItemSurname.
        /// </summary>
        public string ProfessorItemSurname { get; set; }

        /// <summary>
        /// ProfessorItemPhoto.
        /// </summary>
        public ContentItemAsset ProfessorItemPhoto { get; set; }
    }
}
```

C#
**SectionItem.generated.cs**
Copy
```
//--------------------------------------------------------------------------------------------------
// <auto-generated>
//
//     This code was generated by code generator tool.
//
//     To customize the code use your own partial class. For more info about how to use and customize
//     the generated code see the documentation at https://docs.xperience.io/.
//
// </auto-generated>
//--------------------------------------------------------------------------------------------------

using System;
using System.Collections.Generic;
using CMS.ContentEngine;

namespace TrainingGuides.Abstraction.Generated
{
    /// <summary>
    /// Represents a content item of type <see cref="SectionItem"/>.
    /// </summary>
    public partial class SectionItem
    {
        /// <summary>
        /// Code name of the content type.
        /// </summary>
        public const string CONTENT_TYPE_NAME = "TrainingGuides_Generated.SectionItem";

        /// <summary>
        /// Represents system properties for a content item.
        /// </summary>
        public ContentItemFields SystemFields { get; set; }

        /// <summary>
        /// SectionItemProfessors.
        /// </summary>
        public IEnumerable<ProfessorItem> SectionItemProfessors { get; set; }

        /// <summary>
        /// SectionItemLessons.
        /// </summary>
        public IEnumerable<LessonItem> SectionItemLessons { get; set; }

        /// <summary>
        /// SectionItemCode.
        /// </summary>
        public int SectionItemCode { get; set; }
    }
}
```

#### Retrieve content items
By nature, following the repository pattern helps to centralize code so that maintenance is easier, but that doesn’t mean putting all your data access logic directly in the repository methods is the best approach. The pattern can be refined further with a reusable service that serves all of the repositories.
Let’s create a service that uses the content query API in Xperience. It can be used across multiple repositories in the _Infrastructure_ project, further centralizing the data access code.
C#
**IContentItemRetrieverService.cs**
Copy
```
using CMS.ContentEngine;
using CMS.Websites;

namespace TrainingGuides.Abstraction.Infrastructure.Shared.Services;

public interface IContentItemRetrieverService<T>
{
    public Task<T?> RetrieveWebPageById(int webPageItemId,
        string contentTypeName,
        int depth = 1);

    public Task<IEnumerable<T>> RetrieveWebPageContentItems(string contentTypeName,
        Func<ContentTypeQueryParameters, ContentTypeQueryParameters> queryFilter);
}
```

C#
**ContentItemRetrieverService.cs**
Copy
```
using CMS.ContentEngine;
using CMS.Websites;
using CMS.Websites.Routing;
using Kentico.Content.Web.Mvc.Routing;

namespace TrainingGuides.Abstraction.Infrastructure.Shared.Services;

public class ContentItemRetrieverService<T> : IContentItemRetrieverService<T>
{
    private readonly IContentQueryExecutor contentQueryExecutor;
    private readonly IWebsiteChannelContext webSiteChannelContext;
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;

    public ContentItemRetrieverService(
        IContentQueryExecutor contentQueryExecutor,
        IWebsiteChannelContext webSiteChannelContext,
        IPreferredLanguageRetriever preferredLanguageRetriever)
    {
        this.contentQueryExecutor = contentQueryExecutor;
        this.webSiteChannelContext = webSiteChannelContext;
        this.preferredLanguageRetriever = preferredLanguageRetriever;
    }


    /// <summary>
    /// Retrieves Web page content item by Id using ContentItemQueryBuilder
    /// </summary>
    /// <param name="webPageItemId">The Id of the Web page content item.</param>
    /// <param name="contentTypeName">Content type name of the Web page.</param>
    /// <param name="depth">The maximum level of recursively linked content items that should be included in the results. Default value is 1.</param>
    /// <returns>A Web page content item of specified type, with the specified Id</returns>
    public async Task<T?> RetrieveWebPageById(
        int webPageItemId,
        string contentTypeName,
        int depth = 1)
    {
        var pages = await RetrieveWebPageContentItems(
                contentTypeName,
                innerParams => innerParams
                    .WithLinkedItems(depth, options => options.IncludeWebPageData(true)),
                outerParams => outerParams
                    .Where(where => where.WhereEquals(nameof(WebPageFields.WebPageItemID), webPageItemId)),
                languageName: languageName);
        return pages.FirstOrDefault();
    }

    /// <summary>
    /// Retrieves web page content items using ContentItemQueryBuilder
    /// </summary>
    /// <param name="contentTypeName">Content type name of the Web page.</param>
    /// <param name="queryFilter">A delegate used to configure query for given contentTypeName</param>
    /// <returns>An enumerable set of items</returns>
    public async Task<IEnumerable<T>> RetrieveWebPageContentItems(
        string contentTypeName,
        Func<ContentTypeQueryParameters, ContentTypeQueryParameters> queryFilter)
    {
        var builder = new ContentItemQueryBuilder()
                            .ForContentType(
                                contentTypeName,
                                config => queryFilter(config)
                                .ForWebsite(webSiteChannelContext.WebsiteChannelName)
                            )
                            .InLanguage(preferredLanguageRetriever.Get());

        var queryExecutorOptions = new ContentQueryExecutionOptions
        {
            ForPreview = webSiteChannelContext.IsPreview
        };

        var pages = await contentQueryExecutor.GetMappedWebPageResult<T>(builder, queryExecutorOptions);

        return pages;
    }

}
```

Because the service deals with infrastructure-specific objects and does not need to be used in other projects, its interface is also in the infrastructure layer instead of the domain layer. Try to avoid the idea that interfaces of any kind must go in the domain layer while their implementations go in the infrastructure layer.
#### Implement the repository
With the content item retriever service in place, you can move on to implementing the repository.
The service makes retrieving content items from Xperience fairly straightforward, but you still have to worry about converting the generated class objects from Xperience into domain model objects.
Make sure to include a method that handles this mapping. You need to account for the differences between the layers.
While it is required to meet the needs defined by the domain, the infrastructure layer does not need to mirror its models exactly. It simply needs to find some way to fulfill the requirement. It’s okay to have structures that are not perfectly symmetrical.
  * To populate `Download` objects from the domain, you’ll need `AssetItem` objects from Xperience.
  * For the `Course`’s `Department` property, you’ll need a `DepartmentItem` object from Xperience, which has no analog in the domain.


The specifics of these methods will change based on how you model your content types in Xperience.
For example, depending on your requirements, it may be better to have listing pages hold collections of reusable content items, rather than web page content items. You could skip the extra step of querying specific course pages, in this case, but then you wouldn’t be able to link to the course pages from the listing page.
C#
**CourseRepository.cs**
Copy
```
using CMS.Websites;
using Kentico.Content.Web.Mvc.Routing;
using TrainingGuides.Abstraction.Core.Courses;
using TrainingGuides.Abstraction.Generated;
using TrainingGuides.Abstraction.Core.Shared.Models.Downloads;
using TrainingGuides.Abstraction.Core.Courses.Repositories;
using TrainingGuides.Abstraction.Core.Courses.Models;
using TrainingGuides.Abstraction.Infrastructure.Shared.Services;

namespace TrainingGuides.Abstraction.Infrastructure.Courses.Repositories;

public class CourseRepository : ICourseRepository
{
    private readonly IContentItemRetrieverService<CoursePage> courseContentItemRetrieverService;
    private readonly IContentItemRetrieverService<CourseListingPage> listingContentItemRetrieverService;
    private readonly IWebPageUrlRetriever webPageUrlRetriever;
    private readonly IPreferredLanguageRetriever preferredLanguageRetriever;

    public CourseRepository
        (IContentItemRetrieverService<CoursePage> courseContentItemRetrieverService,
        IContentItemRetrieverService<CourseListingPage> listingContentItemRetrieverService,
        IWebPageUrlRetriever webPageUrlRetriever,
        IPreferredLanguageRetriever preferredLanguageRetriever)
    {
        this.courseContentItemRetrieverService = courseContentItemRetrieverService;
        this.listingContentItemRetrieverService = listingContentItemRetrieverService;
        this.webPageUrlRetriever = webPageUrlRetriever;
        this.preferredLanguageRetriever = preferredLanguageRetriever;
    }

    public async Task<Course> GetById(int id)
    {
        CoursePage? coursePage = await courseContentItemRetrieverService.RetrieveWebPageById(
            webPageItemId: id,
            contentTypeName: CoursePage.CONTENT_TYPE_NAME,
            depth: 5);
        return await mapCourseFromWebPageItem(coursePage);
    }

    public async Task<IEnumerable<Course>> GetListingById(int id)
    {
        var courseListingPage = await listingContentItemRetrieverService.RetrieveWebPageById(
            webPageItemId: id,
            contentTypeName: CourseListingPage.CONTENT_TYPE_NAME,
            depth: 1);

        var webPageGuids = courseListingPage?.CourseListingCourses.Select(item => item.WebPageGuid).ToList();

        var coursePages = await courseContentItemRetrieverService.RetrieveWebPageContentItems(
            contentTypeName: CoursePage.CONTENT_TYPE_NAME,
            queryFilter: config => config
                .Where(where => where.WhereIn(nameof(WebPageFields.WebPageItemGUID), webPageGuids?.ToList()))
                .WithLinkedItems(5));

        return await Task.WhenAll(coursePages.Select(mapCourseFromWebPageItem));

    }

    public async Task<IEnumerable<Course?>> GetAll()
    {
        var coursePages = await courseContentItemRetrieverService.RetrieveWebPageContentItems(
            contentTypeName: CourseItem.CONTENT_TYPE_NAME,
            queryFilter: config => config.WithLinkedItems(5));

        return await Task.WhenAll(coursePages.Select(mapCourseFromWebPageItem));
    }

    private async Task<Course> mapCourseFromWebPageItem(CoursePage? coursePage)
    {
        if (coursePage == null || coursePage.CoursePageContent.Count() < 1)
            return new Course();

        var courseItem = coursePage.CoursePageContent.FirstOrDefault();

        string url = (await webPageUrlRetriever.Retrieve(coursePage, preferredLanguageRetriever.Get())).RelativePath;

        return new Course
        {
            Title = courseItem.CourseItemTitle,
            Department = courseItem.CourseItemDepartment?.FirstOrDefault()?.DepartmentItemCode,
            Code = courseItem.CourseItemCode,
            Description = courseItem.CourseItemDescription,
            Credits = courseItem.CourseItemCredits,
            Location = url,
            Sections = courseItem.CourseItemSections
                .Select(sectionItem => new Section
                {
                    Code = sectionItem.SectionItemCode,
                    Professors = sectionItem.SectionItemProfessors
                        .Select(professorItem => new Professor
                        {
                            Title = professorItem.ProfessorItemTitle,
                            GivenName = professorItem.ProfessorItemGivenName,
                            Surname = professorItem.ProfessorItemSurname,
                            Photo = professorItem.ProfessorItemPhoto.Url
                        }),
                    Lessons = sectionItem.SectionItemLessons
                        .Select(lessonItem => new Lesson
                        {
                            Title = lessonItem.LessonItemTitle,
                            Description = lessonItem.LessonItemDescription,
                            Start = lessonItem.LessonItemStart,
                            End = lessonItem.LessonItemEnd,
                            Materials = lessonItem.LessonItemMaterials
                                .Select(materialItem => new Material
                                {
                                    ExternalLinkText = materialItem.MaterialItemExternalLinkText,
                                    ExternalLinkAltText = materialItem.MaterialItemExternalLinkAltText,
                                    ExternalLinkUrl = materialItem.MaterialItemExternalLinkUrl,
                                    Download = materialItem.MaterialItemDownload
                                        .Select(assetItem => new Download
                                        {
                                            Title = assetItem.AssetItemTitle,
                                            Description = assetItem.AssetItemDescription,
                                            FileName = assetItem.AssetItemFile?.Metadata.Name,
                                            Url = assetItem.AssetItemFile?.Url
                                        }).FirstOrDefault()
                                })
                        })
                }),
        };
    }
}
```

The code samples in this guide rely on a [decorated version](https://github.com/Kentico/xperience-by-kentico-training-guides/blob/main/src/TrainingGuides.Web/Features/Shared/Services/TrainingGuidesWebPageUrlRetriever.cs) of `IWebPageUrlRetriever` that includes exception handling.
If you do not plan to use a similar customization, make sure to handle errors that the `Retrieve` method may throw when it cannot find a page.
### Add the UI layer
The UI layer of this sample contains a single project - _TrainingGuides.Abstraction.Web_.
It is where things get a little complicated when dealing with a digital experience platform (DXP) like Xperience by Kentico, because DXPs typically offer features to help integrate the UI into the content management experience.
This blurs the lines between the infrastructure and UI layers, as the UI will contain code specific to the platform.
It is technically possible to completely separate these layers, if you use only [Headless channels](/documentation/developers-and-admins/configuration/headless-channel-management) in Xperience, but this defeats the purpose of using a DXP platform in some ways. In order to utilize features like [content tree-based routing](/documentation/developers-and-admins/development/routing/content-tree-based-routing), [Page Builder](/documentation/developers-and-admins/development/builders/page-builder), and [page templates](/documentation/developers-and-admins/development/builders/page-builder/page-templates-for-page-builder), you need to include code from Kentico in the UI layer, and reference the project where the generated classes are stored.
This sample elects to use those features.
[![](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-10_9-24-6.png)](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-10_9-24-6.png)
The web project has a lot more going on in the root, so the files organized by business concern are kept in a folder called _Features_.
**Differences to the domain layer**
Like with the content items in the infrastructure layer, this example has some differences between the view models in the UI layer and the models in the domain layer. The `CourseListingViewModel` doesn’t directly correspond to any domain model, and the `LinkViewModel` exists, rather than anything directly corresponding to the `Download` domain model.
While the UI layer depends on the domain layer and uses its models, it does not need to mirror them one to one. Each layer is allowed to have its own requirements and concerns, as long as they correctly connect through the domain objects.
You can look over the code in the following snippets to see the details of the view model classes.
Pay attention to each class’s GetViewModel method, which converts between the domain objects and the view models, which at times have different properties.
For example, `MaterialViewModel` and `LinkViewModel`, which take a somewhat different approach than the domain.
C#
**CourseListingViewModel.cs**
Copy
```
using System.Collections.Generic;
using System.Linq;
using TrainingGuides.Abstraction.Core.Courses.Models;

namespace TrainingGuides.Abstraction.Web.Features.Courses.ViewModels;

public class CourseListingViewModel
{
    public IEnumerable<CourseViewModel> Courses { get; set; }

    public static CourseListingViewModel GetViewModel(IEnumerable<Course> courses) =>
        new CourseListingViewModel
        {
            Courses = courses.Select(course => CourseViewModel.GetViewModel(course))
        };
}
```

C#
**CourseViewModel.cs**
Copy
```
using System.Collections.Generic;
using System.Linq;
using TrainingGuides.Abstraction.Core.Courses.Models;

namespace TrainingGuides.Abstraction.Web.Features.Courses.ViewModels;

public class CourseViewModel
{
    public string Title { get; set; }
    public string Department { get; set; }
    public int Code { get; set; }
    public string Description { get; set; }
    public int Credits { get; set; }
    public string Location { get; set; }
    public IEnumerable<SectionViewModel> Sections { get; set; }

    public static CourseViewModel GetViewModel(Course course) =>
        new CourseViewModel
        {
            Title = course.Title,
            Department = course.Department,
            Description = course.Description,
            Code = course.Code,
            Credits = course.Credits,
            Location = course.Location,
            Sections = course.Sections.Select(section => SectionViewModel.GetViewModel(section))
        };
}
```

C#
**LessonViewModel.cs**
Copy
```
using System.Collections.Generic;
using System;
using System.Linq;
using TrainingGuides.Abstraction.Core.Courses.Models;

namespace TrainingGuides.Abstraction.Web.Features.Courses.ViewModels;

public class LessonViewModel
{
    public string Title { get; set; }
    public string Description { get; set; }
    public DateTime Start { get; set; }
    public DateTime End { get; set; }
    public IEnumerable<MaterialViewModel> Materials { get; set; }

    public static LessonViewModel GetViewModel(Lesson lesson) =>
        new LessonViewModel
        {
            Title = lesson.Title,
            Description = lesson.Description,
            Start = lesson.Start,
            End = lesson.End,
            Materials = lesson.Materials
                .Select(material => MaterialViewModel.GetViewModel(material))
        };
}
```

C#
**MaterialViewModel.cs**
Copy
```
using System.Collections.Generic;
using TrainingGuides.Abstraction.Core.Courses.Models;
using TrainingGuides.Abstraction.Web.Features.Shared.ViewModels;

namespace TrainingGuides.Abstraction.Web.Features.Courses.ViewModels;

public class MaterialViewModel
{
    public IEnumerable<LinkViewModel> Links { get; set; }

    public static MaterialViewModel GetViewModel(Material material)
    {
        var links = new List<LinkViewModel>();

        if (!string.IsNullOrEmpty(material.ExternalLinkUrl))
        {
            links.Add(LinkViewModel.GetViewModel(
                linkText: material.ExternalLinkText ?? material.ExternalLinkUrl,
                linkAltText: material.ExternalLinkAltText ?? "External link",
                linkUrl: material.ExternalLinkUrl,
                linkIsDownlaod: false

            ));
        }

        if (material.Download is not null)
        {
            links.Add(LinkViewModel.GetViewModel(material.Download));
        }

        return new MaterialViewModel
        {
            Links = links
        };
    }
}
```

C#
**ProfessorViewModel.cs**
Copy
```
using TrainingGuides.Abstraction.Core.Courses.Models;

namespace TrainingGuides.Abstraction.Web.Features.Courses.ViewModels;

public class ProfessorViewModel
{
    public string Title { get; set; }
    public string GivenName { get; set; }
    public string Surname { get; set; }
    public string Photo { get; set; }

    public static ProfessorViewModel GetViewModel(Professor professor) =>
        new ProfessorViewModel
        {
            Title = professor.Title,
            GivenName = professor.GivenName,
            Surname = professor.Surname,
            Photo = professor.Photo,
        };
}
```

C#
**SectionViewModel.cs**
Copy
```
using System.Collections.Generic;
using System.Linq;
using TrainingGuides.Abstraction.Core.Courses.Models;

namespace TrainingGuides.Abstraction.Web.Features.Courses.ViewModels;

public class SectionViewModel
{
    public int Code { get; set; }
    public IEnumerable<ProfessorViewModel> Professors { get; set; }
    public IEnumerable<LessonViewModel> Lessons { get; set; }

    public static SectionViewModel GetViewModel(Section section) =>
        new SectionViewModel
        {
            Code = section.Code,
            Professors = section.Professors
                .Select(professor => ProfessorViewModel.GetViewModel(professor)),
            Lessons = section.Lessons
                .Select(lesson => LessonViewModel.GetViewModel(lesson))
        };
}
```

C#
**LinkViewModel.cs**
Copy
```
using TrainingGuides.Abstraction.Core.Shared.Models.Downloads;

namespace TrainingGuides.Abstraction.Web.Features.Shared.ViewModels;

public class LinkViewModel
{
    public string LinkText { get; set; }
    public string LinkAltText { get; set; }
    public string LinkUrl { get; set; }
    public bool LinkIsDownload { get; set; }

    public static LinkViewModel GetViewModel(string linkText, string linkAltText, string linkUrl, bool linkIsDownlaod = false) =>
        new LinkViewModel
        {
            LinkText = linkText,
            LinkAltText = linkAltText,
            LinkUrl = linkUrl,
            LinkIsDownload = linkIsDownlaod
        };

    public static LinkViewModel GetViewModel(Download download) =>
        new LinkViewModel
        {
            LinkText = download.Title,
            LinkAltText = download.Description,
            LinkUrl = download.Url,
            LinkIsDownload = true
        };
}
```

#### Create the pages
Create and register page controllers for the `CourseListingPage` and `CoursePage` content types. The controllers should each resolve an `ICourseRepository` and use it to retrieve `Course` objects with the methods from earlier.
Then, use the view model methods to convert the domain model objects to view models. Pass the view models to page templates designed to work with them, and display the data.
Make sure the only places that you use the Xperience generated classes in your _Web_ project are the registration attributes of content tree-based routing controllers and page templates.
**Compiler-enforced compliance**
This is an example where having the generated classes separate comes in handy. You can have a project reference from  _Web_ to  _Generated_ without also referencing  _Infrastructure._
With an IOC controller that lets you resolve implementations from _Infrastructure_ without a project reference, then developers in your _Web_ project cannot directly reference the `CourseRepository` class implemented there.
If a developer tries to do this, the compiler will throw an exception when they try to build the project. This is more reliable than a set of rules and procedures, because it does not rely on someone else noticing the mistake and asking the developer to fix it.
C#
**CourseListingPageController.cs**
Copy
```
using Kentico.Content.Web.Mvc;
using Kentico.Content.Web.Mvc.Routing;
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;
using TrainingGuides.Abstraction.Core.Courses.Repositories;
using TrainingGuides.Abstraction.Generated;
using TrainingGuides.Abstraction.Web.Features.Courses.ViewModels;

[assembly: RegisterWebPageRoute(
    contentTypeName: CourseListingPage.CONTENT_TYPE_NAME,
    controllerType: typeof(TrainingGuides.Abstraction.Web.Features.Courses.CourseListingPageController))]
namespace TrainingGuides.Abstraction.Web.Features.Courses;

public class CourseListingPageController : Controller
{
    private readonly IWebPageDataContextRetriever webPageDataContextRetriever;
    private readonly ICourseRepository courseRepository;

    public CourseListingPageController(IWebPageDataContextRetriever webPageDataContextRetriever,
        ICourseRepository courseRepository)
    {
        this.webPageDataContextRetriever = webPageDataContextRetriever;
        this.courseRepository = courseRepository;
    }

    public async Task<IActionResult> Index()
    {
        var context = webPageDataContextRetriever.Retrieve();
        var courses = await courseRepository.GetListingById(context.WebPage.WebPageItemID);
        var model = CourseListingViewModel.GetViewModel(courses);

        return new TemplateResult(model);

    }

}
```

cshtml
**CourseListingPagePageTemplate.cshtml**
Copy
```
@using Kentico.Content.Web.Mvc.PageBuilder
@using TrainingGuides.Abstraction.Web.Features.Courses.ViewModels
@using TrainingGuides.Abstraction.Web.Features.Shared.ViewModels

@model TemplateViewModel

@{
    var templateModel = Model.GetTemplateModel<CourseListingViewModel>();
}
<ul>
    @foreach(CourseViewModel course in templateModel.Courses)
    {
        string name = $"{course.Department}-{course.Code} {course.Title}";
        <li>
            <a href = "@course.Location" alt="link to course @name">
                <div>
                    @name
                </div>
                <div>
                    @course.Description
                </div>
            </a>
        </li>
    }
</ul>
```

C#
**CourseListingPagePageTemplate.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using TrainingGuides.Abstraction.Generated;
using TrainingGuides.Abstraction.Web.Features.Courses;

[assembly: RegisterPageTemplate(
    identifier: CourseListingPagePageTemplate.IDENTIFIER,
    name: "Course listing page content type template",
    customViewName: "~/Features/Courses/CourseListingPagePageTemplate.cshtml",
    ContentTypeNames = [CourseListingPage.CONTENT_TYPE_NAME],
    IconClass = "xp-a-lowercase")]

namespace TrainingGuides.Abstraction.Web.Features.Courses;

public class CourseListingPagePageTemplate
{
    public const string IDENTIFIER = "TrainingGuides.Abstraction.CourseListingPagePageTemplate";
}
```

C#
**CoursePageController.cs**
Copy
```
using Kentico.Content.Web.Mvc;
using Kentico.Content.Web.Mvc.Routing;
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;
using TrainingGuides.Abstraction.Core.Courses.Repositories;
using TrainingGuides.Abstraction.Generated;
using TrainingGuides.Abstraction.Web.Features.Courses.ViewModels;

[assembly: RegisterWebPageRoute(
    contentTypeName: CoursePage.CONTENT_TYPE_NAME,
    controllerType: typeof(TrainingGuides.Abstraction.Web.Features.Courses.CoursePageController))]

namespace TrainingGuides.Abstraction.Web.Features.Courses;

public class CoursePageController : Controller
{
    private readonly IWebPageDataContextRetriever webPageDataContextRetriever;
    private readonly ICourseRepository courseRepository;

    public CoursePageController(
        ICourseRepository courseRepository,
        IWebPageDataContextRetriever webPageDataContextRetriever)
    {
        this.courseRepository = courseRepository;
        this.webPageDataContextRetriever = webPageDataContextRetriever;
    }

    public async Task<IActionResult> Index()
    {
        var context = webPageDataContextRetriever.Retrieve();
        var course = await courseRepository.GetById(context.WebPage.WebPageItemID);
        var model = CourseViewModel.GetViewModel(course);

        return new TemplateResult(model);
    }
}
```

cshtml
**CoursePagePageTemplate.cshtml**
Copy
```
@using Kentico.Content.Web.Mvc.PageBuilder
@using TrainingGuides.Abstraction.Web.Features.Courses.ViewModels
@using TrainingGuides.Abstraction.Web.Features.Shared.ViewModels

@model TemplateViewModel

@{
    var templateModel = Model.GetTemplateModel<CourseViewModel>();
    string departmentAndCourseCode = $"{templateModel.Department}-{templateModel.Code}";
    string heading = $"[{departmentAndCourseCode}] {templateModel.Title}";
}

<div>
    <h1>@heading</h1>
</div>
<div>
    <span>@departmentAndCourseCode</span>
</div>
<div>
    <span>Department: @templateModel.Department</span>
</div>
<div>
    <span>Credits: @templateModel.Credits</span>
</div>
<div>
    @templateModel.Description
</div>
<div>
    <h2>Sections</h2>
    <ul>
        @foreach(SectionViewModel courseSection in templateModel.Sections)
        {
            <li>
                <h3>@($"{departmentAndCourseCode}-{courseSection.Code}")</h3>
                <ul>
                    <li>
                        <div>
                            <h4>Professors</h4>
                            <ul>
                                @foreach(ProfessorViewModel professor in courseSection.Professors)
                                {
                                    string prof = $"{professor.Title} {professor.GivenName} {professor.Surname}";
                                    <li>
                                        <div>
                                            <img src="@professor.Photo" alt="photo for @prof" style="max-width:200px"/>
                                        </div>
                                        <div>
                                            @prof
                                        </div>
                                    </li>
                                }
                            </ul>
                        </div>
                    </li>
                    <li>
                        <div>
                            <h4>Lessons</h4>
                            <ul>
                                @foreach (LessonViewModel lesson in courseSection.Lessons)
                                {
                                    TimeSpan duration = lesson.Start.Subtract(lesson.End);
                                    string displayDuration = $"{Math.Abs(duration.Hours)}h {Math.Abs(duration.Minutes)}m";
                                    <li>
                                        <div>
                                            <span>title: @lesson.Title</span>
    persona: developer
                                        </div>
                                        <div>
                                            <span>Date: @lesson.Start.ToShortDateString()</span>
                                        </div>
                                        <div>
                                            <span>Length: @displayDuration</span>
                                        </div>
                                        <div>
                                            <span>Description: @lesson.Description</span>
                                        </div>
                                        Materials:
                                        <ul>
                                            @foreach (MaterialViewModel material in lesson.Materials)
                                            {
                                                foreach(LinkViewModel link in material.Links)
                                                {
                                                    <li>
                                                        <a href="@link.LinkUrl" alt="@link.LinkAltText" @(link.LinkIsDownload ? "download" : string.Empty)>@link.LinkText</a>
                                                    </li>
                                                }

                                            }
                                        </ul>
                                    </li>
                                }
                            </ul>
                        </div>
                    </li>
                </ul>
            </li>
        }
    </ul>
</div>
```

C#
**CoursePagePageTemplate.cs**
Copy
```
using Kentico.PageBuilder.Web.Mvc.PageTemplates;
using TrainingGuides.Abstraction.Generated;
using TrainingGuides.Abstraction.Web.Features.Courses;

[assembly: RegisterPageTemplate(
    identifier: CoursePagePageTemplate.IDENTIFIER,
    name: "Course page content type template",
    customViewName: "~/Features/Courses/CoursePagePageTemplate.cshtml",
    ContentTypeNames = [CoursePage.CONTENT_TYPE_NAME],
    IconClass = "xp-a-lowercase")]

namespace TrainingGuides.Abstraction.Web.Features.Courses;

public class CoursePagePageTemplate
{
    public const string IDENTIFIER = "TrainingGuides.Abstraction.CoursePagePageTemplate";
}
```

#### Finish the project.
With the controllers and page templates in place, only a few steps remain to get the project running:
  * When the application starts 
    * Set up the IOC container
    * Initialize Xperience with the proper parameters to allow content tree-based routing and Page Builder for these content types.
  * Create supporting display files like a layout view, view start file, and view imports.
  * Add data to Xperience.


We invite you to finish setting up this project as an exercise, following the principles you learned in this guide.
With that, you should be able to display any courses added to the Xperience instance.
[![](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-10_15-15-9.png)](/docsassets/guides/use-abstraction-and-enterprise-level-architecture-in-xperience-projects/image-2024-1-10_15-15-9.png)
## What’s next?
Now that you’ve seen an example of data retrieval with a repository in the context of an enterprise layered architecture, you can start thinking about how it could be expanded.
Perhaps you’d like to add methods for Create, Update, and Delete to the repository. Perhaps you’d like to expand the domain layer beyond just the repository. You can try adding new object types like `Student` and `CourseRegistration` and an event handler in the domain layer that sends an email though a service implemented in the infrastructure layer whenever the time of a lesson is updated.
Moving forward, try to consider what degree of layering and abstraction will benefit your projects and strike the proper balance to fit your scenario.
![]()
[]()[]()
