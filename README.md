# mirage_darks
Create Linearized Dark files for MIRAGE using the Darks from the JWST/NIRCam Cryo-Vacuum Campaigns (ISIM/CV)

***Author: Arsh Nadkarni (UArizona)***

***Source: Bryan Hilbert (STScI)***

**Process:**
- ***Get the essential Darks from FENRIR:***
Copy the required darks from `/surtrdata/External/ISIMCV3_unzipped`.
For example, if you want to get AX Darks, say `cp -r /surtrdata/External/ISIMCV3_unzipped/NRCNRCAX*.fits AX/`. 
This will copy the AX Darks from FENRIR to a directory `AX` on your local machine. 
Create a directory `AX_Darks` inside `AX` to copy only the .fits dark files.

- ***Copy .fits Darks to their sub-directories:***
Use the copy_darks_to_subdirectory.sh code to copy the .fits dark files in `AX` to a sub-directory `AX_Darks` which will host all the .fits Dark files.
This bash file should be inside the `A1` directory. Then, from the command-line: `bash copy_darks_to_subdirectory.sh`. You will see all required darks in the `AX_Darks` directory subsequently.

- ***Sort the Darks based on the ISIM/CVX performance:***
Manually sort the acquired dark files based on your ISIM/CVX needs.

- ***Convert from Raw to DMS Format:***
Use convert_to_DMS.py, which calls sci2ssb.py and nircam2ssb.py
First, update the glob call in convert_to_DMS.py so that it is pointing to your files (`AX_Darks\*.fits`).
Then from the command-line:`python convert_to_DMS.py`. You will see all required darks in the `AX_DMS_Darks` directory subsequently.

- **Tweak the DMS Darks to be in the Level1B format (this will save disk space by removing unnecessary extensions):**
You can either:
  * A) make convert_to_Level1b.py executable, and then use a separate call for each uncal file: `> ./convert_to_Level1b.py your_dark_file_uncal.fits`
  * B) update the glob call in run_conversion.py to point to your DMS uncal files (`AX_DMS_Darks\*uncal.fits`), and run: `> python run_conversion.py`\
You will see all required darks in the `AX_DMS_Darks` directory subsequently. Now, move the Level-1B Darks to another directory in `AX`, say `AX_Level1b_Darks` using `mv *level1b_uncal.fits /AX_Level1b_Darks`.
  
- **Run MIRAGE's dark_prep step on the DMS Darks in order to Linearize:**
Create a MIRAGE yaml file for each Level1b dark - easiest is probably to use the attached yaml file as a template and update the file name in the 'dark:' field, as well as the value in 'array_name:', which should be 'NRCXX_FULL' where XX is the detector number (e.g. 'A1'). For the A5 and B5 detectors, you may also need to update the filter value to be something in the LW channel. I don't think dark_prep looks at this info, but if you get an error, that's one thing to do to solve it. Update the glob call in create_linearized_darks.py to use your yaml files.
E.g `yaml_files = sorted(glob('/home/anadkarni/mirage_darks/A1/A1_yaml_files/*.yaml'))`.
Then call create_linearized_darks.py from the command-line: `python create_linearized_darks.py`.
You will see all required Linearized Darks in the `mirage_darks` directory subsequently. Now, move the Linearized Darks to another directory in `AX`, say `AX_Linearized_Darks` using `mv *_uncal.fits AX/AX_Linearized_Darks`.

- **Finally, put the resulting Linearized Darks into the proper sub-directory in your MIRAGE_DATA location:**
E.g. `/home/anadkarni/JWST_Programs/mirage_reference_files/mirage_data/nircam/darks/linearized/<detector_name>/`

**The list of Darks to be converted from ISIM/CV3 can be found in:** `darks_notes_CV3.txt`
