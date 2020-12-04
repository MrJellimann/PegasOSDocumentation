## 0.1.1 Preface

The purpose of separating the design document into two separate documents is two-fold: one is to preserve the nature of the original design document and its intentions for PegasOS, and two is to contain and manage the changes that have been made in design and development since the creation of the original design document.


This document will serve as both an addendum to the original document, as well as its own document regarding the current state of the system as of the end of the Fall 2020 semester. As we are sure you can imagine, the scope of the original project was… large. Because it was rather large, we have not been able to achieve some of the finer details that were discussed in the Spring 2020 design document. However we feel that despite that, we’ve achieved our goal in creating a new operating system for the Raspberry Pi.


For the original design document as well as all other documentation and research created over the course of this project, go here:


https://github.com/MrJellimann/PegasOSDocumentation


For the project source code and release, go here:


https://github.com/MrJellimann/PegasOS

## 0.1.2 Overview

This document describes the changes and new decisions that affect the design and implementation of the final system. Here, we’ll briefly discuss these changes by breaking them down into more manageable sections that can be explored at will. You can also use the Table of Contents to jump to the section of your choosing.


By far the biggest change was the adoption of the Circle Driver Library, made by rsta2 on GitHub, for interfacing with the hardware components on the Raspberry Pi. At the end of the Spring 2020 semester we had achieved a bare-metal boot environment on the Pi 4, and we then proceeded to tackle our next problem which would be keyboard input. This was not a problem that we could solve ourselves - not in the given time frame at the very least. In our search for open-source drivers, we found the Circle library which contains not just keyboard drivers, but gamepad drivers, sound drivers, other USB device drivers, and much more. Adopting Circle early-on in development saved us a lot of time in the later months, but it has changed the nature of the project quite substantially. We will discuss the features and capabilities of Circle, as well as how Circle is being used in PegasOS in the first major section: Circle x PegasOS.


We have underestimated the time that components on the project would take to develop, and as such we’ve fallen behind when it comes to the more ambitious features of PegasOS. While not all features have been cut, some have been reworked to better accommodate the new structure of the system. Though as development will be continuing until the deadline, assume that the cut and modified feature list is not final unless stated otherwise. We will be discussing these cut and modified features, and what the plans are for dealing with these features in the second major section: PegasOS Cuts and Edits.


After discussing the cut and modified features and components of PegasOS, we can discuss the features that were preserved and have been implemented in the final system. Some details will be sacrificed here, though if you wish to have more detail on a particular component that information can be found in the Spring 2020 Design Document for PegasOS. This section will outline the implemented features of PegasOS, and describe the current capabilities of the system as of Fall 2020 in the third major section: PegasOS First Release.


As the project of PegasOS has become part-development and part-research, it also has potential for expansion for future senior design projects as well as for educational purposes in systems development (which was an additional benefit discussed in the original document). This section will discuss the research-side of the project and the project’s utility and potential in other projects in the fourth major section: PegasOS Future.


The final section will contain some administrative information, such as the final budget of the project and legal information. This section will also contain the closing remarks from the members of the team. This section will be called: PegasOS Team.

[Back - Addendum Design Document Home](ADD_DESIGN_DOCUMENT.md) | [Next - 1.0.0 Circle x PegasOS](1_CIRCLE_X_PEGASOS.md) | 
[Addendum Design Document Home](ADD_DESIGN_DOCUMENT.md) | [Documentation Home](../README.md)