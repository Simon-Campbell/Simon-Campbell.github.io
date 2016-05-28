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
Major software systems can be intimidating to release to the public, perhaps due to the potentially complex nature of a software release. One pitfall I have noted during my career is that people can fall into the trap of releasing too much functionality at one time. Larger releases of software generally leads to a complex release and a release process that is not frequently followed. This release process is generally not refined due to the infrequency of it being followed. In order to counteract that I recommend the the Release Early, Release Often (RERO)[^1] software development philosophy.

A larger set of functionality released at once makes the impact of a failed deployment much greater: rollback becomes more difficult and if rollback has to occur, then it is likely **everything** is rolled back [^2]. Contrast this to the scenario of rolling out a single feature: if that fails, then you only have to rollback that feature. The impact of having to roll back several features is less than that of rolling back several.

An unrefined software release process is expensive and potentially misunderstood. The more frequently a release can be done, then the tighter a release process can become. Tightening up the release process with each release also makes servicing the application at a future date more accessible to other developers. New developers can release bug fixes and functionality upgrades with less resistance. This makes maintenance cheaper and also helps to ensure that the knowledge of releasing the software is not tied to a group of *system experts* (e.g. original developers). Decoupling system experts from the release means they are free to add value elsewhere instead of managing releases. In the scenario that they leave [^3], then they are taking less tacit knowledge with them.

If you are considering building a software system, then you should also consider baking modularity into the architecture. It is likely that this will likely increase the cost of individual features by some margin due to the software developers needing to create sensible interfaces between modules. This cost will enable RERO and with that, will enable a tighter feedback loop between your business and the software users. Enabling feedback earlier can help prioritise the next functionalities to be developed. Further to this, a modular system becomes easier to test: only updated modules need to be released and tested. Also consider RERO as a way to enable market testing, instead of developing a full feature set and releasing with a "big bang" approach, your business has been given an opt-out opportunity. Further development can be ceased[^4] if the important features are released earlier and do not have the expected user uptake.

Where possible, you should strive to release the simplest version of a software system you can have. Follow the RERO philosophy. Larger releases carry higher risk and higher stresses. Any failure in a release has a higher potential loss.  A failed release may also carry deadline slippage with it: a second, complex release will be carried out along with a higher testing effort (due to change complexity). Any last minute code fixes introduced in a re-release may also be of questionable quality due to being implemented under stressful conditions.

[^1]: [Release Early, Release Often](http://haacked.com/archive/2011/04/20/release-early-and-often.aspx/) by Phil Haack elaborates on RERO further
[^2]: There may be some cowboy that will be able to get you to production. I hope that cowboy does not do some magical, undocumented hack which haunts the next software release
[^3]: Why would they do that!? Employing a philosophy like RERO is sure to improve morale and keep your developers around
[^4]: Be vigilant with your decision and ensure that you are not blinded by the [sunk cost fallacy](http://dictionary.cambridge.org/dictionary/english/sunk-cost-fallacy)