# mirage_darks
Create Linearized Dark files for MIRAGE

***Source: Bryan Hilbert (STScI)***

**Process:**
- ***Convert from Raw to DMS Format:***
Use convert_to_DMS.py, which calls sci2ssb.py and nircam2ssb.py
First, update the glob call in convert_to_DMS.py so that it is pointing to your files.
Then from the command line:
`python convert_to_DMS.py`

- **Tweak the DMS file to be in the Level1B format (this will save disk space by removing unnecessary extensions):**
You can either:
  * A) make convert_to_Level1b.py executable, and then use a separate call for each uncal file: `> ./convert_to_Level1b.py your_dark_file_uncal.fits`
  * B) update the glob call in run_conversion.py to point to your uncal files, and run: `> python run_conversion.py`
  
- **Run MIRAGE's dark_prep step on the DMS Darks in order to Linearize:**
Create a Mirage yaml file for each dark - easiest is probably to use the attached yaml file as a template and update the file name in the 'dark:' field, as well as the value in 'array_name:', which should be 'NRCXX_FULL' where XX is the detector number (e.g. 'A1'). For the A5 and B5 detectors, you may also need to update the filter value to be something in the LW channel. I don't think dark_prep looks at this info, but if you get an error, that's one thing to do to solve it.
Update the glob call in create_linearized_darks.py to use your yaml files.
Then call create_linearized_darks.py from the command line: `python create_linearized_darks.py`

- **Finally, put the resulting Linearized Darks into the proper sub-directory in your MIRAGE_DATA location:**
E.g. `/home/anadkarni/JWST_Programs/mirage_reference_files/mirage_data/nircam/darks/linearized/<detector_name>/`

