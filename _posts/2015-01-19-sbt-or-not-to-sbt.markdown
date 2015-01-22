---
layout: post
title:  "To SBT or not to SBT"
date:   2015-01-19 18:15:26
categories: build-tools update
tldr: SBT is nice build tool alternative. Google it!
---
In my current project at work we use SBT as our build tool for Java projects. 

I think it is nice tool to know, it is quite specific so maybe there is chance you will like it. It is not world changing, but can be really usefull at times. 

For me it is pretty good - at my previous big project I used ANT (with no dependency management!) and it was so annoying at times! For some convention is required, but it is pretty nice and clean - the build file is in fact Scala class (if you shiver when you hear Scala it won't be for you!) and it is quite powerful. If you are interested go [read about it](http://www.scala-sbt.org). We need couple types of build processes and there are no issues with most of them in SBT. They are handled out-of-the-box (like publishing artifacts) or can be easily added by plugins (like creating fat JARs). So if you don't need anything fancy for your build process, SBT is great. Probably it can handle some fancyness as well- I bet someone wrote the plugin you might need! It is good intro to Scala as well - SBT is de facto standard build tool in Scala world - <i>sbt console</i> launches REPL with your project classpath which is pretty handy. 

The pretty nice feature is incremental recompilation and monitoring the changes in the classes - i.e. you can start process which will invoke tests as soon as something is edited.
 
I think it is pretty nice alternative to verbose Maven. Unfortunately I don't have experience with Gradle, so I can't compare it. But in general I think it is worth trying this one out!

[sbt-homepage]: http://www.scala-sbt.org  
