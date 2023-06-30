# The XNAT Toolbox
These simple shell scripts enable a XNAT user to perform batch operations, such as creating Subjects and Experiments, on a XNAT server. The advantage of these scripts is that they can be pasted together into other shell scripts to perform tasks. Currently, these scripts require a user token (see [requestXNAToken](#requestXNAToken)) to run. Future versions will allow both batch operations and single use calls. 

[![Github All Releases](https://img.shields.io/github/downloads/shaneyflores/xnat_toolbox/total.svg)]()
[![GPLv3 License](https://img.shields.io/badge/License-GPL%20v3-yellow.svg)](https://opensource.org/licenses/)

_____
### requestXNAToken
Request a secret and alias token from XNAT to perform batch operations. Output will be a string containing the **alias:secret** tokens. In any larger shell script in which you are calling these commmands, this script should be run **FIRST**.

```
requestXNAToken [-u|-user] username [-s|-site] xnat_site
```
| argument | | description |
| ----- | ----- | ----- |
| `-u\|-user` | **REQUIRED** | `username` is user's account name for the XNAT server |
| `-s\|-site` | **REQUIRED** | `xnat_site` is address of the XNAT server (e.g., fornix.wustl.edu) |

An example call would be: 
```bash
requestXNAToken -u sflores -s fornix.wustl.edu
```
_____
### createSubject 
Create a single subject within an existing XNAT project. Return code will be 201 if request was successful. Otherwise, the HTML error will be returned.
```
createSubject [-i|-id] project_id subject_id [-t|-token] alias secret [-s|-site] xnat_site
```

| argument | | description |
| ----- | ----- | ----- |
| `-i\|-id` | **REQUIRED** | `project_id` is existing XNAT project name <br> `subject_id` is desired subject name |
| `-t\|-token` | **REQUIRED** | `alias` and `secret` tokens (see [requestXNAToken](#requestXNAToken)) |
| `-s\|-site` | **REQUIRED** | `xnat_site` is address of the XNAT server (e.g., fornix.wustl.edu) |


An example call would be: 
```bash
createSubject -id Test_Project Test_Subject001 -t ABCDEF123456 STUVWXYZ9876543 -s fornix.wustl.edu
```
_____
### createExperiment
Create a single experiment within an existing subject. Return code will be 201 if request was successful. Otherwise, the HTML error will be returned.
```
createExperiment [-i|-id] project_id subject_id experiment_id [-t|-token] alias secret [-m] modality [-s|-site] xnat_site
```
| argument | | description |
| ----- | ----- | ----- |
| `-i\|-id` | **REQUIRED** | `project_id` is existing XNAT project name <br> `subject_id` is existing subject name <br> `experiment_id` is desired experiment name |
| `-t\|-token` | **REQUIRED** | `alias` and `secret` tokens (see [requestXNAToken](#requestXNAToken)) |
| `-s\|-site` | **REQUIRED** | `xnat_site` is address of the XNAT server (e.g., fornix.wustl.edu) |
| `-m` | **REQUIRED** | `modality` of XNAT experiment (must be **mr**, **pet**, **ct**) |

An example call would be: 
```bash
createExperiment -id Test_Project Test_Subject001 Subject_PIB_v1 -t ABCDEF123456 STUVWXYZ9876543 -m pet -s fornix.wustl.edu
```
_____
### createScan
Create an single scan within an existing experiment. 
```
createScan [-i|-id] project_id subject_id experiment_id [-n|-name] scan_id [-s|-site] xnat_site [-t|-token] alias secret [-m] modality [-p|-params] params
```
| argument | | description |
| ----- | ----- | ----- |
| `-i\|-id` | **REQUIRED** | `project_id` is existing XNAT project name <br> `subject_id` is existing subject name <br> `experiment_id` is exissting experiment name |
| `-t\|-token` | **REQUIRED** | `alias` and `secret` tokens (see [requestXNAToken](#requestXNAToken)) |
| `-s\|-site` | **REQUIRED** | `xnat_site` is address of the XNAT server (e.g., fornix.wustl.edu) |
| `-m` | **REQUIRED** | `modality` of XNAT experiment (must be **mr**, **pet**, **ct**) |
| `-n\|-name` | **REQUIRED** | `scanid` is desired scan number (e.g., 1, 2, 3, 4_E7, etc.) |
| `-p\|-params` | **OPTIONAL** | `params` are additional parameters for curl call <br> (e.g., "&xnat:mrScanData/type=T1&xnat:mrScanData/series_description=T1" |

Guidance on available parameters to pass can be found in the [XNAT REST API documentation](https://wiki.xnat.org/display/XAPI/Scan+Data+REST+XML+Path+Shortcuts).

An example call would be: 
```bash
createScan -id Test_Project Test_Subject001 Subject_PIB_v1 -name 100 -t ABCDEF123456 STUVWXYZ9876543 -m pet -s fornix.wustl.edu
```
_____
### createScanResource
Create a resource folder under a single scan.  
```
createScanResource [-i|-id] project_id subject_id experiment_id scan_id [-t|-token] alias secret [-r] resource [-s|-site] xnat_site [-p|-params] params
```
| argument | | description |
| ----- | ----- | ----- |
| `-i\|-id` | **REQUIRED** |  `project_id` is existing XNAT project name <br> `subject_id` is existing subject name <br> `experiment_id` is exissting experiment name <br> `scan_id` is the number of scan |
| `-t\|-token` | **REQUIRED** | `alias` and `secret` tokens (see [requestXNAToken](#requestXNAToken)) |
| `-s\|-site` | **REQUIRED** | `xnat_site` is address of the XNAT server (e.g., fornix.wustl.edu) |
| `-r` | **REQUIRED** | `resource` is the name of the resource folder to create (e.g., NIFTI) |
| `-p\|-params` | **OPTIONAL** | `params` are additional parameters for curl call <br> (e.g., "content=NIFTI&format=NIFTI") |

An example call would be: 
```bash
createScanResource -id Test_Project Test_Subject001 Subject_PIB_v1 100 -t ABCDEF123456 STUVWXYZ9876543 -m pet -s fornix.wustl.edu
```

Guidance on available parameters to pass can be found in the [XNAT REST API documentation](https://wiki.xnat.org/display/XAPI/Scan+Data+REST+XML+Path+Shortcuts).

_____
### uploadScanResource
Upload a file (e.g., a NIFTI) to an individual scan's resource folder. This is most commonly done for adding NIFTIs to XNAT. 
```
uploadScanResource [-i|-id] project_id subject_id experiment_id scan_id [-t|-token] alias secret [-f|-file] session_file [-r] resource [-s|-site] xnat_site
```
| argument | | description |
| ----- | ----- | ----- |
| `-i\|-id` | **REQUIRED** |  `project_id` is existing XNAT project name <br> `subject_id` is existing subject name <br> `experiment_id` is exissting experiment name <br> `scan_id` is the number of scan |
| `-t\|-token` | **REQUIRED** | `alias` and `secret` tokens (see [requestXNAToken](#requestXNAToken)) |
| `-s\|-site` | **REQUIRED** | `xnat_site` is address of the XNAT server (e.g., fornix.wustl.edu) |
| `-r` | **REQUIRED** | `resource` is the name of the resource folder to create (e.g., NIFTI) |
| `-f` | **REQUIRED** | `session_file` is the file to be uploaded (e.g., T1_MRI.nii.gz) |

An example call would be: 
```bash
uploadScanResource -id Test_Project Test_Subject001 Subject_PIB_v1 100 -t ABCDEF123456 STUVWXYZ9876543 -r NIFTI -f PIB.nii.gz -s fornix.wustl.edu
```

_____
### uploadRawImg
Upload a file (e.g., a zip file containing DICOMs) to an existing experiment in XNAT. This is most commonly done for adding DICOM files to XNAT. The `-f` argument must be provided a zip file containing all the DICOMs to be uploaded to an experiment. 
```
uploadRawImg [-i|-id] project_id subject_id experiment_id [-f|-file] session_file [-s|-site] xnat_site [-t|-token] alias secret
```
| argument | | description |
| ----- | ----- | ----- |
| `-i\|-id` | **REQUIRED** |  `project_id` is existing XNAT project name <br> `subject_id` is existing subject name <br> `experiment_id` is exissting experiment name |
| `-t\|-token` | **REQUIRED** | `alias` and `secret` tokens (see [requestXNAToken](#requestXNAToken)) |
| `-s\|-site` | **REQUIRED** | `xnat_site` is address of the XNAT server (e.g., fornix.wustl.edu) |
| `-f` | **REQUIRED** | `session_file` is the file to be uploaded (e.g., MR_DICOMS.zip) |

An example call would be: 
```bash
uploadRawImg -id Test_Project Test_Subject001 Subject_PIB_v1 -t ABCDEF123456 STUVWXYZ9876543 -f PIB_DICOMS.zip -s fornix.wustl.edu
```

Once the upload is complete, XNAT will begin parsing the DICOMs to extract session information, such as scan date/time and scanner model. Current version of this program skips the prearchive. 

_____
### createResource
Create a resource folder in an existing experiment on XNAT. This is typically done to include files such as raw data files for PET or screening forms for MRI. This only *creates* a resource folder; it does not upload anything to the folder. 
```
createResource [-i|-id] experiment_id [-t|-token] alias secret [-r] resource [-s|-site] xnat_site [-p|-params] filename
```
| argument | | description |
| ----- | ----- | ----- |
| `-i\|-id` | **REQUIRED** |  `experiment_id` is exissting experiment accession ID  |
| `-t\|-token` | **REQUIRED** | `alias` and `secret` tokens (see [requestXNAToken](#requestXNAToken)) |
| `-s\|-site` | **REQUIRED** | `xnat_site` is address of the XNAT server (e.g., fornix.wustl.edu) |
| `-r` | **REQUIRED** | `resource` is the name of the resource folder to create (e.g., RawData) |
| `-p\|-params` | **OPTIONAL** | `params` are additional parameters for curl call <br> (e.g., "content=RAW&format=RawData") |

An example call would be: 
```bash
createScanResource -id Test_Project Test_Subject001 Subject_PIB_v1 -t ABCDEF123456 STUVWXYZ9876543 -s fornix.wustl.edu -r RawData -p "content=RAW&format=RawData"
```
