---
layout: post
title:  "SBT Update"
date:   2015-01-19 20:15:26
categories: build-tools update
tldr: Jumping to new SBT version is pain. Make sure there is enough time to do it.
---
Recently I had "pleasure" to update SBT to newest version in our projects. You may think this is simple process but that is not true. So why bothering with that? Well it is always good to be up to date, especially when SBT is rapidly evolving and there are a lot improvements across the versions, it is faster and easier to use.

The problems I have encountered when working on updating SBT (if you planning to do something like that here will be the list you may potentially encounter!): 

* Plugin incompabilities. The old plugins may be not compatible with new version of SBT - the one I struggled a bit was sbt-assembly plugin. New SBT introduced concept which is called auto plugins - which simplifies creation of build script. But well it didn't make it easier for old configuration - basically the old configuration had to be removed. In my opinion it is bad the build got broken, but I had to live with it.
* Dependencency incompabilities - the way Scala handles compilation and dependency management makes it problematic for Scala dependencies. The Scala version is part of dependency path (because of compability issues between different Scala versions) so if your dependency wasn't compiled in given Scala version, thus prepared for SBT you are using you might be out of luck. For example Play framework is problematic. You couldn't update to newer SBT without updating Play. Other way around is true actually as well - if you needed to update Play framework you would have to update SBT version as well! Madness - the build tool shouldn't break your dependencies. Causing a lot of pain. I believe if one is working on Scala projects this may be even bigger problem than it was for me.
* Changes in ops scripts. Updating Play to newer version has another impact of the update process (not counting the required code changes and testing) - Play distro is placed in another folder in the project structured. This means the scripts on the server will have to change and if the same scripts are being used for dev and prod pipelines it can be nasty.
* Weird multi project behaviour. We had multi project build files. Unfortunately there was no "root" project configured. By "root" project I mean project attached to "." path. If this is the case, new SBT creates artificial "root" project on its own. For some reason it caused issues with detecting classes to run. And made "root" variable reserved :(. Why changing something which works?
* Test running. Simply the JUnit tests weren't properly ran - there were issues with tests runners. It was fixed in newer version, but could be misleading. Possible to miss those couple of Spring tests from whole suite.
* Last but not least, my favourite - class loader issue, breaking Mockito. When tests are invoked in new SBT during the tests class loader of test is different than one used to load the classes. It makes spying on the anonymous inner classes not possible - the Java complains when new dynamic class is loaded. Strange thing spying on non anonymous inner classes works fine, so there has to be subtle difference. I have even opened the [issue on Mockito's GitHub](https://github.com/mockito/mockito/issues/133), but looks my solution is not good enough. Crazy stuff, caused by SBT upgrade, had to be find and fixed.

Probably there were some other minor issues I already forgot, but those are most important problems which will take you some time to pinpoint and fix. So be aware when planning update to newest SBT (especially from 0.12.x version!). It won't be straightforward.

[mockito-issue]: https://github.com/mockito/mockito/issues/133
