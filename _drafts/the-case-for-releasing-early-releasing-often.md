---
title: 'The Case for Releasing Early and Releasing Often'
author: Simon Campbell
layout: post
categories:
  - Development
tags:
  - programming
  - releasing
  - agile
---
Major software systems can be intimidating to release to the public, perhaps due to the potentially complex nature of a software release. One pitfall I have noted during my career is that people can fall into the trap of releasing too much functionality at one time. Large releases of functionality generally leads to complex releases and a release process that is not frequently followed, and therefore becomes a process that is not refined. In order to counteract that I recommend the the Release Early, Release Often (RERO)[^1] software development philosophy.

A larger set of functionality released at once also makes the impact of a failed deployment much greater: rollback becomes more difficult and if rollback occurs, **everything** is rolled back. Contrast this to the scenario of rolling out a single feature: if that fails, then you only have to rollback that feature. The impact of having to roll back several features is less than that of rolling back several.

An unrefined software release process is expensive and potentially misunderstood. The more frequently a release can be done, then the tighter the process to get new functionality out the door can become. Tightening up the release process with each release also makes servicing the application at a future date more accessible to other developers. New developers to the software system can release bug fixes and functionality upgrades with less resistance. This makes maintenance cheaper and also helps to ensure that the knowledge of releasing the software is not tied to a group of *system experts*. Those who may be considered system experts (e.g. the original developers) are free to add value elsewhere instead of managing releases.

If you are considering building a software system, then you should also consider baking modularity into the architecture. It is likely that this will likely increase the cost of individual features by some margin, as the software developers will need to create sensible interfaces between modules. This cost provides a with a tighter feedback loop between your business and the software users, where user feedback can help you prioritise the next functionalities to be developed. Further to this, a modular system becomes easier to test: only updated modules need to be released and tested. In the worst case scenario, your businesss has an opt-out opportunity for further development if the important, released features do not have the expected user uptake (i.e. a failed market test). That is better than developing the full feature set only to find out that the expected market did not exist. 

Where possible, you should strive to release the simplest version of the system you can have. Follow the RERO philosophy. Larger releases carry higher risk and higher stresses. Any failure in a release has a higher potential loss.  A failed release may also carry deadline slippage with it: a second, complex release will be carried out along with a higher testing effort (due to change complexity). Any last minute code fixes introduced in a re-release may also be of questionable quality due to being implemented under stressful conditions.

[^1]: [Release Early, Release Often](http://haacked.com/archive/2011/04/20/release-early-and-often.aspx/) by Phil Haack elaborates on RERO further