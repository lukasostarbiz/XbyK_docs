---
source: https://docs.kentico.com/documentation/business-users/digital-marketing/customer-journeys
scrape_date: 2026-01-22
---

  * [Home](/documentation)
  * [Business users](/documentation/business-users)
  * [Digital marketing](/documentation/business-users/digital-marketing)
  * Customer journeys 


# Customer journeys
**Advanced license required**   
  
Features described on this page require the Xperience by Kentico **Advanced** license tier. 
To learn more about the concept of customer journeys in depth, see our dedicated [series of guides](/guides/customer-journeys).
Customer journeys offer a unified view of [contact](/documentation/business-users/digital-marketing/contact-management) interactions with your content. You can define key stages in a journey, representing the sequence of steps a contact must complete to achieve a successful conversion and set conditions for their progression through the stages. The [resulting visualization](#customer-journey-results) allows you to gain insights into how contacts move through the journey. For example, you can connect these interactions from an initial visit of a landing page to submitting a particular [form](/documentation/business-users/digital-marketing/forms).
## Create a customer journey
A customer journey is a sequence of steps performed by [contacts](/documentation/business-users/digital-marketing/contact-management), where each step is represented by a stage.
Stages represent key steps in the customer journey, such as visiting a specific page or submitting a form. They help you track contact progression, which enables you to analyze contact interactions with your content.
  1. Open the **Customer journeys** application in Xperience.
  2. Select **New journey**.
  3. _(Optional)_ Edit (  
[![Edit customer journey display name](/docsassets/documentation/customer-journeys/CJ_Display_name.png)](/docsassets/documentation/customer-journeys/CJ_Display_name.png)
  4. In the **Journey Builder** view mode, select **New stage** to create a stage: 
    1. Fill in the following properties: 
       * **Stage name** – the name of the stage displayed in Xperience.
       * _(Optional)_ **Stage KPI** – the target number of contacts that you expect to reach this specific stage. The KPI (key performance indicator) can be used to assess the success of a particular stage in the customer journey. The KPI for the final stage can serve as a measure of overall success, reflecting how many contacts completed the full process.
       * **Conditions** – determines the conditions that contacts must meet to advance to this stage. See [conditions](#conditions) to learn more.  
[![Creating a new stage](/docsassets/documentation/customer-journeys/CJ_Stage.png)](/docsassets/documentation/customer-journeys/CJ_Stage.png)
    2. **Apply** the stage configuration.
  5. You can now iteratively add more stages to your customer journey. The maximum number of stages in a customer journey is 10.
  6. Once you have created all stages of the journey, select **Save** to create the customer journey.


The journey will begin calculating once you save it. It may take up to a few minutes, depending on the number of contacts being evaluated.
When you edit an existing customer journey, it automatically begins [recalculating](#customer-journey-results) once you save the changes.
### Conditions
For a [contact](/documentation/business-users/digital-marketing/contact-management) to complete the entire customer journey and achieve a successful conversion, they must meet the conditions of each stage in the exact order defined by the journey. Since stage conditions are based on specific activities performed by the contact at a given time (such as visiting a page or submitting a form), the journey progresses sequentially. A contact can only reach a stage if they have fulfilled its condition as well as all conditions of the previous stages in the correct sequence.
When building the conditions which contacts must meet to advance to a particular stage:
  * Use the search or select categories to find the appropriate condition.
  * For the set of conditions, you can select if **Any** or **All** of the conditions need to be fulfilled.


To ensure high accuracy of your customer journey, make sure your developers set up detailed [activity logging](/documentation/developers-and-admins/digital-marketing-setup/set-up-activities). Insufficient logging can lead to incomplete data, misinterpretations, and skewed calculations, affecting future decision-making and performance insights.
## Customer journey results
To view the results of a customer journey:
  1. Open the **Customer journeys** application in Xperience.
  2. Select the journey whose results you want to view.
  3. Select the **Journey** view mode to see the results.


For each stage in the graph, you can view the number of [contacts](/documentation/business-users/digital-marketing/contact-management) who _converted_ and met the conditions to advance to the stage. Between two stages, you can see the number of contacts who _dropped off_ as they didn’t meet the conditions to continue to the next stage.
[![Journey graph and the list of stages](/docsassets/documentation/customer-journeys/CJ_Journey.png)](/docsassets/documentation/customer-journeys/CJ_Journey.png)
Additionally, you can find the following information in the list of stages below the graph:
  * **Stage** – the display name of the stage
  * **Converted** – the number and percentage of contacts from the previous stage who met the conditions to advance to this stage
  * **Dropped off** – the number and percentage of the contacts who did not meet the conditions to advance to the next stage
  * **Stage KPI** – the number and percentage of the contacts who reached the stage compared to the set [KPI](#create-a-customer-journey)
  * **Conditions** – the list of conditions that contacts must meet to advance to this stage


### View converted and dropped off contacts
To gain insights from a journey manually, you can view which [contacts](/documentation/business-users/digital-marketing/contact-management) converted or dropped off at each stage by selecting the corresponding numbers in the [journey results](#customer-journey-results).
To view more information about a specific contact from the converted or dropped off list, open (
[![Number of converted and dropped off contact for a stage](/docsassets/documentation/customer-journeys/CJ_Journey_converted_droppedoff.png)](/docsassets/documentation/customer-journeys/CJ_Journey_converted_droppedoff.png)
[![Contacts converted in stage](/docsassets/documentation/customer-journeys/CJ_converted_contacts.png)](/docsassets/documentation/customer-journeys/CJ_converted_contacts.png)
### Customer journey insights
You can use [AIRA in-product guidance](/documentation/business-users/aira#aira-in-product-guidance) directly in the Xperience by Kentico administration interface to explore your journeys further. [Customer journey insights](/documentation/business-users/aira#customer-journey-insights) offer you information on which [contact groups](/documentation/business-users/digital-marketing/contact-groups) are present in journeys and how they progress through each stage. This helps you compare the performance of contact groups, explore audience behavior, and identify opportunities to improve engagement strategies. See [AIRA documentation](/documentation/business-users/aira#customer-journey-insights) for more information.
### Recalculate a customer journey
Customer journey results are calculated upon saving stages in the **Journey Builder** , reflecting the state of [contacts](/documentation/business-users/digital-marketing/contact-management) at the time of calculation. The data is not automatically updated according to contact activity that occurs after the calculation.
You can manually update the results at any time by selecting the **Recalculate** button in the **Journey** view mode to incorporate new contacts or those who have progressed to later stages of the journey. The calculation of a journey may take up to a few minutes, depending on the number of contacts being evaluated.
[![Position of the Recalculate button](/docsassets/documentation/customer-journeys/CJ_Journey_Recalculate.png)](/docsassets/documentation/customer-journeys/CJ_Journey_Recalculate.png)
**Time the recalculation**
If your application is hosting a live website, do not run the recalculation during the site’s peak traffic hours, as recalculation of the data can be a resource-intensive process that may reduce the application’s performance.
**Schedule automatic recalculation**
If you want to keep the journey results up to date, you can schedule an automatic recalculation of a journey.
First, your administrators need to configure and enable the _Scheduled customer journey recalculation_ [scheduled task](/documentation/developers-and-admins/customization/scheduled-tasks).
Then, to enable the automatic recalculation for a customer journey:
  1. Open the **Customer journeys** application.
  2. Select the journey whose results you want to recalculate automatically.
  3. In the **Journey Builder** view mode, enable the **Scheduled recalculation** property.
  4. **Save** the changes.


The system will now periodically recalculate the journey according to the configuration of the scheduled task.
All newly [created](#create-a-customer-journey) journeys will now have the **Scheduled recalculation** property selected by default as long as the _Scheduled customer journey recalculation_ scheduled task is enabled.
Once you no longer need to keep the results of a journey automatically up to date, we recommend disabling the **Scheduled recalculation** property. Having too many journeys scheduled for recalculation may impact system performance.
Upon recalculation, contacts which no longer meet the [conditions](#conditions) are removed from the corresponding stages. Recalculating the customer journey keeps the data up to date, reflecting only those contacts who currently match the inclusion criteria.
Between journey calculations, [contact merging](/documentation/business-users/digital-marketing/contact-management#contact-merging) may cause minor inconsistencies in the results. These inconsistencies are resolved when the journey is recalculated, ensuring the most accurate data.
![]()
[]()[]()
