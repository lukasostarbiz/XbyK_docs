---
source: https://docs.kentico.com/modules/work-with-assets/manual-cropping-and-focal-points
scrape_date: 2026-01-22
---

Module: Work with assets
17 of 21 Pages
# Manual cropping and focal point
When you upload an image to the _Content hub_ , Xperience automatically **optimizes** it. This means a file variant is compressed to reduce its size, which helps your **pages load faster** while still keeping the **image sharp and clear**.
At the same time, Xperience generates the predefined _image variants_. To decide how to crop them, the system uses an **automatic focal point** :
  * By default, _AIRA_ places the focal point in the center of the image, but if it recognizes some important area, it moves the focal point accordingly.
  * The focal point tells the system which part of the image should remain visible when cropping.  

  * If the subject doesn’t look right in a particular variant, you can move the focal point yourself.  

  * All automatically generated variants update instantly when you adjust the focal point (except for those you’ve cropped manually).


Behind the scenes, _AIRA_ helps detect the **most crucial area of the image** when you upload it. This usually means people’s faces or central objects, so you won’t often need to change anything. But when you do, it only takes a few clicks.
## Adjusting the focal point
You’ll want to change the focal point if:
  * AIRA’s automatic detection doesn’t focus on the main subject.
  * Certain image variants cut off a person or object you want to feature.
  * The specific part of the image that _AIRA_ selected as a focal point does not match your intentions.


Think of the focal point as telling the system, _“This is the part of the image that must stay visible.”_
As an example, imagine that you’re working on an ad for a Kbank-sponsored sports club, where the most important person will be the player who scored several goals in a row.
Some images you received include the whole team, but the star player is always standing at the far end of the group photo. Because AIRA automatically chooses the **middle of the Primary image** as the focal point, the star player is always **cropped out in most image variants**.
What can you do about it? Since you can easily manually shift the focal point until you’re sure that the star player’s face is visible in all the cropped variants, AIRA’s automatic focal point detection still **saves you time while giving you the flexibility to adjust the images to your needs**.
## Manual cropping
Sometimes, adjusting the focal point isn’t enough. You may want more control over how a variant looks, for example, when a product needs to be perfectly centered in a thumbnail, or when you want to emphasize a specific detail – like our featured star player waving at the camera. In those cases, you can **manually crop a specific variant**.
When cropping manually, keep in mind:
  * You always keep the aspect ratio and dimensions set for the variant.  

  * You can drag or resize the crop box to fine-tune what’s visible and what isn’t.  

  * Once generated, manually cropped variants don’t change even if you adjust the global focal point later.
  * They’re marked with a crop icon so you can tell them apart from the automatically optimized variants.


[![Manually cropped variants](/docsassets/guides/work-with-image-variants-and-focal-point/manually-cropped-image-variant.png)](/docsassets/guides/work-with-image-variants-and-focal-point/manually-cropped-image-variant.png)
You may have noticed that each of the variants in our example is named in a way that relates to how the variant behaves. A “wide” variant retains “wide” dimensions, and a “vertical” always remains “vertical”, no matter where you move the crop box. The developers set these dimensions and the variant names during the project implementation.
While your project setting might look different from ours – your variants may have other dimensions and different names – one thing will always remain the same. An image variant will stay within the predefined limits of its dimensions.
[ Previous page ](/modules/work-with-assets/understanding-image-variants)
17 of 21
[ Mark complete and continue ](/modules/work-with-assets/exercise-upload-and-adjust-image)
![]()
[]()[]()
