# Add a website channel
  * [ Copy page link ](modules/developer-kickstart/add-a-website-channel#) | [Get HelpService ID](modules/developer-kickstart/add-a-website-channel#)
Core MVC 5


[✖](modules/developer-kickstart/add-a-website-channel# "Close page link panel") [Copy to clipboard](modules/developer-kickstart/add-a-website-channel#)
The next few steps in the Kickstart series will cover the process of displaying your reusable content on a website page. Before we can create pages, we need a [website channel](documentation/developers-and-admins/configuration/website-channel-management) to store them. One website channel corresponds to one site, and stores pages and their references to reusable items.
**Channels and reusable content**
Xperience by Kentico supports multiple channels in one Xperience instance. There are currently three types of channels to choose from: [Website](documentation/developers-and-admins/configuration/website-channel-management), [Headless](documentation/developers-and-admins/configuration/headless-channel-management) and [Email](documentation/developers-and-admins/digital-marketing-setup/email-channel-management). You can find more details in their respective documentation pages.
As you saw in the [previous step](modules/developer-kickstart/create-a-reusable-content-type), reusable items in the [Content hub](documentation/business-users/content-hub) exist independently of channels. Editors can reference this reusable content from multiple channels at once, for example, a website and a headless channel that feeds a mobile application.
## Define the channel in the admin UI
To start, sign in to your Xperience administration and navigate to **Configuration → Channel Management**.
Then create a new channel with the following properties:
  * **Channel name:** Kickstart pages
  * **Identifiers → Pre-fill code name automatically:** True (Enabled)
  * **Channel type:** Website
  * **Channel size:** Standard channel
  * **Website domain:** localhost
  * **Primary language:** English


[![screenshot of channel creation dialog](docsassets/guides/add-a-website-channel/kickstart-pages-channel-create.png)](https://docs.kentico.com/docsassets/guides/add-a-website-channel/kickstart-pages-channel-create.png)
**Website domain configuration**
If you need more specialized domain configurations for your future projects, check out our [documentation page](documentation/developers-and-admins/configuration/website-channel-management#website-channels-in-private-cloud-environments) about website channel management for more info.
## Check your progress
After saving the channel, when you navigate to the dashboard, you will see your **Kickstart pages** channel under the **Channels** category in the dashboard. Additionally, a new **Channels** item appears on top of the left menu.
[![Screen recording of the admin UI dashboard, containing the Kickstart pages channel](docsassets/guides/add-a-website-channel/kickstart-pages-channel-dashboard.gif)](https://docs.kentico.com/docsassets/guides/add-a-website-channel/kickstart-pages-channel-dashboard.gif)
[ Previous page ](modules/developer-kickstart/create-a-reusable-content-type)
4 of 13
[ Mark complete and continue ](modules/developer-kickstart/define-a-page-content-type)
  * [Community Questions & Answers](https://community.kentico.com/q-and-a)
  * [Contact support](https://community.kentico.com/support)


### Cookie consent
We use necessary [cookies](https://www.kentico.com/cookies-policy) to run our website and improve your experience while browsing. Additional cookies are only used with your consent. You may revoke your consent on the [Cookies Policy](https://www.kentico.com/cookies-policy) page or in your browser at any time. 
ACCEPT ALL  [Configure](https://www.kentico.com/cookies-policy)
USE ONLY NECESSARY 
![](https://docs.kentico.com/modules/developer-kickstart/add-a-website-channel)
[](https://docs.kentico.com/modules/developer-kickstart/add-a-website-channel)[](https://docs.kentico.com/modules/developer-kickstart/add-a-website-channel)