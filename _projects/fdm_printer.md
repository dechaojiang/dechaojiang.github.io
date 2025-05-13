---
layout: page
title: 3D FDM Printer
description: For advanced functional soft material fabrication
img: assets/img/FDM_Printer/cover.gif
importance: 1
category: Projects
related_publications: false
---

My gratitude to [Prof. Iek Man LEI](https://www.fst.um.edu.mo/people/ieklei/), who has been instrumental in guiding and supporting this independent research project.

The frontier of 3D printing technology is advancing rapidly, especially in fabricating soft materials essential for biomedical engineering, soft robotics, wearable devices, and more. This new platform offers a revolutionary approach to 3D printing, outperforming conventional systems in material adaptability, printing speed, precision, and extendability. Additionally, this platform is cost-effective, priced under 100,000 MOP, making it significantly more affordable than comparable systems.

<!-- To give your project a background in the portfolio page, just add the img tag to the front matter like so:

    ---
    layout: page
    title: project
    description: a project with a background image
    img: /assets/img/12.jpg
    --- -->

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/p.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
    </div>
</div>
<div class="caption">

</div>

### Printing samples

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FDM_Printer/heart.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    3D Embedded Printing of a bird-size heart made of 10% Polyvinyl alcohol colored solution in Carbopol supportive bath
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FDM_Printer/hc.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FDM_Printer/UM_LOGO.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FDM_Printer/printing.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    Direct ink writing of the HC logo, UM logo made of Pluronic F127 (LCST* Hydrogel) and a image of Embedded printing in process
</div>

***

## System overview

The printing platform consists of two subsystems: a high-performance gantry stage `positioning system` and a pneumatic-based `material extrusion system`. Each subsystem has its own power supply and embedded MCU. These subsystems are synchronized and collaborate via a desktop-level `customized program`.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FDM_Printer/sys.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Printing setup (free of pressure tubes and printhead),   (PWM servo drive and control board are not shown in this photo)
</div>

***

### Modularized mechanical design
<div class="row justify-content-sm-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/FDM_Printer/mech.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Details of mechanical design, drivechain calculation, control of ABB servo system is presented in [another post](). 

Additional components of this system, not shown in here, include a compressed air pressure source and multiple pressure tubes and printheads of various diameters.

***

### Software design

This software is designed to control the entire system, perform calibration, and execute 3D printing by reading `.gcode` files. It runs on Windows, is debugged using VS C++, and employs the concept of a finite state machine for logic design.
<div class="row justify-content-sm-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/FDM_Printer/soft.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
More about `code`, `user guide` and `demo video` can be fount in the [repository of this project](https://github.com/dechaojiang/Softmatter_Printer/tree/main).

The program architecture of printer is inherited the concept of the Printer’s FSM. It is with this kind of conceptual model, further development of Printer’s program can be easily updated by merging new states, conditions for state entry, tasks, and outputs associated with these states. A conceptual representation is shown above

<div class="row justify-content-sm-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/FDM_Printer/infoflow.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Information flow in 3D-printing process
</div>
***


    
    Key info
    Project: a blend of Research Experiences for Students of Honours College Programme and the Honours Project
    Project supervisor: Dr. Iek Man LEI
    Start/End date: June 20, 2023 – June 1, 2024
    Operation site: Soft Matter Engineering Laboratory, E11 – 2031, FST, University of Macau