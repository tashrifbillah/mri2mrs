# MRI to MRS project

# Project flowchart
(Implemented rightmost column)
![Flowchart](mri2mrs_flowchart.jpg)


# Program description

```
Calculates brain and white matter volumes given an MRS (Magnetic Resonance Spectroscopy)
label map and a T1 image registered in the space MRS.
Dependencies on bet, fslmaths, fast, fslswapdim, fslstats, ConvertBetweenFileFormats, matlab, Slice.
Uses Ofer's MATLAB code (SIEMENS machine) for creating MRS region mask.

Usage:
    mri2mrs.py [SWITCHES] 

Meta-switches:
    -h, --help                             Prints this help message and quits
    --help-all                             Print help messages of all subcommands and quit
    -v, --version                          Prints the program's version and quits

Switches:
    -b, --betThreshold VALUE:str           Threshold for bet mask creation; the default is 0.3
    -c, --caseID VALUE:str                 A T1 image registered in the space of magnetic resonance
                                           spectroscopy (nrrd or nifti); required
    -i, --input VALUE:ExistingFile         A T1 image registered in the space of magnetic resonance
                                           spectroscopy (nrrd or nifti); required
    -l, --labelMap VALUE:ExistingFile      An rda file from the scanner defining label map of a region
                                           (acg, agm, ltemp, pcg, pwm etc.); required
    -m, --mask                             turn on this flag to mask the T1 image before running the
                                           pipeline. When turned on, a mask is created and the T1 image is
                                           multiplied by the mask before running further steps
    -o, --out VALUE:str                    output directory where files created during the pipeline are
                                           saved (if already exist, it will be deleted and recreated);
                                           required
    -r, --region VALUE:str                 Region name (acg, agm, ltemp, pcg, pwm etc.); required


```


# Example execution

```
./mri2mrs.py \
-i /rfanfs/pnl-zorro/projects/VA_AcuteTBI/MRIData/RegisteredT1/BIO_0002-registeredT1.nrrd \
-r pcg -c BIO_0002 -m \
-l /rfanfs/pnl-zorro/projects/VA_AcuteTBI/MRIData/MRS_VA_AcuteTBI/BIO_0002/BIO_0002_pcg_press.rda \
-o ./mri2mrsOutput
```


# Parsing output:

In python3:
with open('logFileName') as f:
    volume= [line.split(':')[1] for line in f.read().split('\n') if "attr" in line]

You can also do float(volume) if necessary.    

The attr you want to grab are ROI, GM, WM, CSF while the logFileName is your log text file.
    
    
In shell:

volume=`grep "attr" logFileName | awk -F ":" '{print $2}'`



# Run test:

```
cd test_data
../mri2mrs.py -i Test_SAG_MPRAGE_1mm_isoa.nii -l MRS_tha_press.rda -c Test -r tha -o ./mri2mrs.test -m
```

You should see the following output:

```
ROI volume:4200.0
CSF volume:0.0
WM volume:4172.0
GM volume:28.0
```


