---
title: "Interactive Whiteboard System"
published: true
---


# Basic Information

#### Project name: 
Kinect-based Interactive Whiteboard System

#### Mentor:
Tao Ren

#### Group Member:
Xinran Tang *(leader)*

Hongsheng Ma

Ziqi Wei

Mohan Feng

Fushuai Zhang

# Introduction

This work not only realizes the free writing function of traditional blackboard teaching, but also realizes the rich display function of multimedia teaching, creating an innovative teaching demonstration mode. 

In traditional multimedia teaching, teachers need to move frequently between the blackboard and the computer, which brings great inconvenience to teaching and affects the teaching effect. 

In response to the above-mentioned problems, this work uses Kinect with Bluetooth transmission function to capture depth signals, and develops application software to enable users to freely write multiple points on the projection screen and manipulate multimedia files using only the depth pen. 

![](/assets/kinect1.jpg)


This work does not need to change the hardware equipment of the existing projection system and is easy to promote.

# Breakthrough

1. The system has increased the control function of PPT and PDF files, which can realize the functions of opening PPT and PDF files, full-screen playback, turning pages, saving modified handwriting, and free writing on PPT, PDF files and drawing boards;
   
2. The system is designed with a small window function, which effectively avoids the restrictions on the operation of the entire screen due to the high suspension of the electronic whiteboard screen or the large screen;
   
3. This system adopts multi-point interactive technology, which can allow multi-point touch, which further enhances the interaction between the presenter and the audience;
   
4. The system has added multiple gesture recognition functions, and the presenter can use gestures to realize operations such as calling the toolbar and turning pages of PPT and PDF files;
   
5. Through this system, the user can use the deep pen to directly write formulas, text or graphic annotations on the drawing board, PPT, or PDF document.
   
6. This system can use Kinect to capture depth signals in different directions, and compare and select two signal points by calculating the captured signals, and get more practical handwriting points. And the system provides two camera erection methods: for temporary erection, the system is designed with a laser positioning device, so that the system can be deployed quickly; for permanent erection, only one calibration and debugging can be performed at the Kinect position. In case, use it many times for a long time.

![](/assets/kinect2.jpg)

# Outcome:

The team completed the design and implementation of the Kinect-based electronic whiteboard touch control system, which solved the shortcomings of traditional blackboard chalk teaching and multimedia teaching, and made up for the whiteboard as the specificity of the teaching field. In the remote automatic calibration module of the electronic whiteboard touch system, combining the characteristics of the system and the advantages of the existing calibration methods, a specific matrix mapping algorithm is researched. In the depth data processing module, the depth of the touch point relative to the Kinect in the actual operation is collected, and the data is transmitted to the module for analysis and processing inside the system. The system uses the background difference method to process and calculate the background depth model to obtain the depth threshold, and then compare the current depth with the background depth model when the depth data is passed in, and filter out the pixel blocks within the depth range. By identifying the contour area of ​​the pixel block and determining whether the pixel block is a touch point according to related parameters. In the contact area data processing module, it is used to determine the contact area of ​​the plane touched by the finger in actual operation, and transmit the data to the module for analysis and processing inside the system.

![](/assets/6.png)

This project has applied for a chinese national patent and a software copyright, and won one silver prize in the 2020 Russian Archimedes International Invention Exhibition, two gold prizes and one first prize in various Chinese student innovation and entrepreneurship competitions.

