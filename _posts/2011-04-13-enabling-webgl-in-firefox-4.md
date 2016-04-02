---
title: Enabling WebGL in Firefox 4
author: Simon Campbell
layout: post
categories:
  - WWW
tags:
  - body
  - firefox 4
  - fx4
  - google
  - tip
  - webgl
---
If you go to [Google Body][1] in Firefox 4, do you get an error stating that WebGL is not initializing properly? If so then keep on reading and I'll explain a very quick switch that you can toggle in order to access the Body Browser and other WebGL applications:

  1. Fire up Firefox 4 and type “about:config” into your address bar then press Enter on your keyboard.
  2. You may get a **warning about dragons.** Tell Firefox you are aware of them and that you'll be careful by clicking the “I'll be careful, I promise!” button.
  3. Type into the “Filter: box” below the address bar the word “webgl”. You should now see most (if not all) of the **WebGL options** in Firefox 4.
  4. Scan the list for “webgl.force-enabled” under “Preference Name” (or type it in the filter, whichever is fastest for you), if the “Value” is set to false then right-click it and select “Toggle”; it should change to true.
  5. Re-open the [Google Body][1] page and it should be working

Now you can browse the anatomy of a human female or maybe you can view some other WebGL content. **If you have problems with Firefox 4 after enabling this then you should disable it**; one would assume it's in beta (since not enabled by default) or you may have actually **awoken a dragon**.

 [1]: http://bodybrowser.googlelabs.com/