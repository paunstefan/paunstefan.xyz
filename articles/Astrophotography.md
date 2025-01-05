# Astrophotography

## Setting up Pixinsight

Download and install the latest Pixinsight version from: <https://pixinsight.com/dist/>. 
After installing, activate it with your UID and activation code. For plate solving, from the
same distribution page download the first 3 or 4 files from Gaia DR3 and Gaia DR3/SP(small set).
To install them, go the Gaia process, and click on the small wrench.

For Multiscale Gradient Correction, download all MARS-DR1 files.

To install additional scripts, including the XTerminator suite, go to Resources->Updates->Manage Repositories
and add the following repos:

* https://www.rc-astro.com/BlurXTerminator/PixInsight
* https://www.rc-astro.com/NoiseXTerminator/PixInsight
* https://www.rc-astro.com/StarXTerminator/PixInsight
* https://elveteek.ch/pixinsight-updates/ez-processing-suite/
* https://www.cosmicphotons.com/pi-modules/colourmask/
* https://www.cosmicphotons.com/pi-scripts/imageblend/
* https://www.cosmicphotons.com/pi-modules/narrowbandnormalization/
* https://www.cosmicphotons.com/pi-scripts/drawannotation/
* https://www.ideviceapps.de/PixInsight/Utilities/
* https://raw.githubusercontent.com/setiastro/pixinsight-updates/main/

Install GraXpert from: <https://graxpert.com/>

### XTerminator suite GPU acceleration on Linux

First you should have `build-essential` and the CUDA compatible NVidia drivers installed.

* Install Nvidia CUDA (<https://developer.nvidia.com/cuda-downloads>): 
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-6
```

* Install cuDDN (<https://developer.nvidia.com/cudnn-downloads>):
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cudnn
sudo apt-get -y install cudnn-cuda-12
```

* Install Tensorflow:

Download the library files from <https://www.tensorflow.org/install/lang_c>, choose
the `Linux GPU support` one. Create a `tensorflow` directory somewhere on your system
(such as $HOME), extract the downloaded archive there.

* Set the Pixinsight library path:

Go to the Pixinsight install directory (default is `/opt/PixInsight`) and in the 
`bin` directory edit the `PixInsight.sh` file by adding the Tensorflow directory to the `LD_LIBRARY_PATH` variable:

```bash
LD_LIBRARY_PATH=$HOME/tensorflow/lib:$dirname/lib:$dirname
```

## Setting up control computer

### TODO!

## Pixinsight processing

### OSC broadband (and narrowband single filter)

* Stack the exposures using the Weighted Batch Preprocessing. Default options should be good, except maybe the Pixel Interpolation under Image Registation
which you may want to set to Bilinear, as the others can introduce artifacts. The resulting image should be in the `master` directory as a `masterLight`.

* Run Spectrophotometric Color Calibration. Image should be plate solved, this can be done using the Image Solver script under Astrometry.
* Crop the image if needed.
* Background extraction. For me the best tool is the Dynamic Background Extraction with the following settings: tolerance 2.0, shadows relaxation: 6.0,
smoothing factor 0.25, default sample radius 120. You can do first a subtraction, then a division. Other good tools are GraXpert, SetiAstro's Automatic DBE and maybe the
new Multiscale Gradient Correction, for which you should first run Spectrophotometric Flux Calibration.
* If needed, run SCNR to remove green noise.
* BlurXterminator.
* NoiseXTerminator, preferably with small values.
* EZSoftStretch
* StarXTerminator
* ! Alternatively, you can remove stars before stretching and stretch the stars with
SetiAstro Star Stretch.
* Curves Transformation until you like how the image looks. Color masking can help for
some targets.
* If it is a narrowband image, you can use Narrowband Normalization to use
a different color pallete.
* Use Image Blend to combine starless with stars.

## Sources

* <https://pixinsight.com/forum/index.php?threads/procedure-to-enable-gpu-acceleration-for-bxt-starnet-etc-within-linux-mint-with-a-supported-nvidia-graphics-card.23356/>