# OMTCentralLineTraining
Inexpensive open-source system for practicing hand-hand coordination for US guided needle insertions.

## Hardware Dependencies
- [ ] A PLUS toolkit compatible ultrasound machine or a frame grabber to get a video output from a non-PLUS compatible US. [See the PLUS toolkit devices list](http://perk-software.cs.queensu.ca/plus/doc/nightly/user/Configuration.html).
- [ ] A standard webcam. This can be built into a laptop, but an external webcam is preferable because it can be positioned independently of the screen.
- [ ] A reasonably modern computer (laptop, or desktop) capable of running the [3D Slicer](https://www.slicer.org/) application.

## Software Dependencies
This system requires a Window's PC to serve as a hardware interface since many device companies do not release Linux or macOS drivers. However, using PLUS's OpenIGTLink functionality tracking & US image data can be sent over the network allowing you to run 3D Slicer on a different computer than PLUS.

You will need to install the PLUS toolkit appropriate for your US machine and webcam, as well as the 3D Slicer application for medical image computing.

- Please select and install the appropriate PLUS version from the [PLUS version device matrix](https://plustoolkit.github.io/download). The downloads are under the "Latest stable release" and "Latest development snapshot" (recommended for developers).
- Please download and install the [3D Slicer application](https://download.slicer.org/).

*Note that there is a bug preventing the running of a PLUS Server with both Telemed US and Mmf Video Source. See [PlusToolkit/PlusLib#365](https://github.com/PlusToolkit/PlusLib/issues/365) The workaround is to run the Telemed device on one PLUS server, and everything else on another - as shown in the ./config directory.*
## Directory Structure
```
OMTCentralLineTraining
│   README.md  (you're reading this now)
|
└───config (PLUS configuration files)
│   │   intel_RS_d415_calibration.yml (sample ArUco calibration file)
│   │   markers.pdf (printable ArUco markers)
│   │   PlusServer_OMTCentralLine_Telemed.xml
│   │   PlusServer_OMTCentralLine_Tracking.xml
│   │   PlusServer_OMTCentralLine_Tracking_Calibration.xml
│   
└───models (stl models for 3D printing)
|   │   CalibrationMarkerPlane.stl
|   │   CalibrationStylus.stl
|   │   PivotBlock.stl
|   │   SyringeClip.stl
|   │   TelemedClip.stl
|   │   TelemedMarkerBracket.stl
|   │   TelemedMarkerPlane.stl
|
└───scene (Slicer scene)
    │   OMT_CentralLineTraining_Scene.mrb
```

## Setup Instructions
You will need to modify the config files in ./config if you use any device other than a Mmf video source compatible webcam and a Telemed US machine with L12 linear probe. Sample config files for any PLUS device can be found in the [PLUS user manual](http://perk-software.cs.queensu.ca/plus/doc/nightly/user/Configuration.html).

If you are new to using Slicer and the PLUS toolkit, please explore some of the tutorials on the [SlicerIGT user tutorial page](http://www.slicerigt.org/wp/user-tutorial/). Particularly useful tutorials for setting up this system include:
- U-01 Software Installation
- U-03 Hardware connections
- U-04 Coordinate transformations
- U-11 Pivot calibration
- U-31 Tracked ultrasound calibration

Instructions:
1. 3D print the models in ./models. You may need to re-design the TelemedClip to fit your particular US probe.
1. Print the markers.pdf file in ./config. Use Acrobat Reader and ensure the print scale is set to 100%. These markers should have a side length of 45mm.
1. Assemble the hardware tools using standard M3 bolts and nuts.
1. Connect your US and webcam to the hardware interface PC (which may, or may not also run Slicer). Start two PLUS Server applications, one running ./config/PlusServer_OMTCentralLine_Telemed.xml and the other running ./config/PlusServer_OMTCentralLine_Tracking_Calibration.xml.
1. Open 3D Slicer and connect to both PLUS servers. In Slicer, open the data module and ensure you can see:
  - Video_Video (containing the webcam video)
  - Image_Image (the US video feed)
  - StylusToUSProbe (the transform between the ArUco markers on the Stylus and USProbe)
  - If you don't see StylusToUSProbe, make sure both markers are visible in Video_Video.

## Calibration Instructions
### Computing ImageToUSProbe
ImageToUSProbe is the transform between the US image, and the ArUco marker placed on the US probe.

1. Follow the SlicerIGT [pivot calibration tutorial](https://onedrive.live.com/view.aspx?resid=7230D4DEC6058018!3128&ithint=file%2cpptx&app=PowerPoint&authkey=!AMy-wgNHStEKsPU) to compute the StylusTipToStylus transform.
1.  Follow the SlicerIGT [US calibration tutorial](https://onedrive.live.com/view.aspx?resid=7230D4DEC6058018!3712&ithint=file%2cpptx&app=PowerPoint&authkey=!ACNGX3PqH0BLg74) to compute the ImageToUSProbe transform.
1. Go to the Transforms module of 3D Slicer, and choose ImageToUSProbe. Press the copy button located under the rotation dropdown.

### Setting up the training scene
1. Change the PLUS server sending ./config/PlusServer_OMTCentralLine_Tracking_Calibration to use the configuration specified in ./config/PlusServer_OMTCentralLine_Tracking.xml.
1. Open the ./scene/OMT_CentralLineTraining_Scene.mrb file in a new instance of Slicer. In the data module you should see:
  - Video_Video
  - Image_Image
  - NeedleToUSProbe (transfrom from the ArUco marker on the syringe to the US probe clip)
1. In transforms choose ImageToUSProbe and paste the transform you computed above.
1. Follow the Slicer IGT [pivot calibration tutorial](https://onedrive.live.com/view.aspx?resid=7230D4DEC6058018!3128&ithint=file%2cpptx&app=PowerPoint&authkey=!AMy-wgNHStEKsPU) once again to find the NeedleTipToNeedle transform. This time you must select NeedleTipToNeedle instead of creating a new transform, as it has already been created in the OMT_CentralLineTraining_Scene.mrb scene.

You should now have a fully functional OpticalMarkerTracking based system for practicing hand-coordination for central line insertions.

## Getting Help
Please feel free to open an issue in this repository if you need help or if there is something in the instructions which needs clarification.
