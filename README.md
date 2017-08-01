# spm12-dartel

Code for preprocessing of functional and structural MRI data into standardized MNI space using SPM12 and DARTEL. 

* Code in <b>/spm12_dartel_1struct</b> is for datasets that include only one structural scan (e.g. either T1 MPRAGE or T2 matched-bandwidth)
* Code in <b>/spm12_dartel_mprageMBW</b> is for datasets that include two structural scans (e.g. T1 MPRAGE *and* T2 matched-bandwidth)

<b>Instructions:</b>

Within each folder there is a <b>wrapper</b> script and a <b>run</b> function. All user-editable parameters are in an the epynomous section of the wrapper. Other sections of the wrapper script and run function shouldn't be edited unless you know what you're doing. Call only the wrapper as the wrapper will call the run function in a parfor loop.
A "runStatus" struct containg each subject's pre-dartel status will be saved in the folder specified in "batchDir". The matlab workspace after pre-dartel will also be saved in "batchDir", you can use this to re-run DARTEL without re-running pre-dartel. 
A text log of the matlab console output will be saved for predartel & dartel in the "batchDir" folder

Final image ouputs for further analyses:
* swrBOLD[run name].nii = resliced, warped-to-MNI & smoothed BOLD images
* rp_BOLD[run name].nii = motion parameters to include in level1 model as nuisance regressors
* wm[MPRAGE/structural name].nii = bias-corrected warped-to-MNI structural image
* wc1[MPRAGE/structural name].nii = warped-to-MNI grey matter segmentation image (can be used as mask/ROI; signal from which typically used for functional connectivity analyses, etc)
* wc1[MPRAGE/structural name].nii = warped-to-MNI white matter segmentation image (can be used as mask/ROI; signal from which typically regressed out during functional connectivity analyses, etc)
* wc1[MPRAGE/structural name].nii = warped-to-MNI cerebrospinal fluid (also eyes/sinuses maybe) segmentation image (can be used as mask/ROI; signal from which typically regressed out during functional connectivity analyses, etc)

<b>1struct algorithm:</b>
1) Realign & reslice functionals to mean functional (pre-dartel; parfor parallelization)
2) Co-register structural to mean functional (pre-dartel; parfor parallelization)
3) Segment & bias-correct structural, generate segment params for DARTEL  (pre-dartel; implicitly parallaleized)
4) Create DARTEL templates & generate deformation fields for MNI normalization
5) Normalize functionals to MNI space via DARTEL
6) Smooth functionals with FWHM kernel via DARTEL (implicit parallelization from here)
7) Normalize bias-corrected structural to MNI space via DARTEL
8) Normalize grey matter (C1) segmentation to MNI space via DARTEL
9) Normalize white matter (C2) segmentation to MNI space via DARTEL
10) Normalize CSF (C3) segmentation to MNI space via DARTEL

<b>mprageMBW algorithm:</b>
1) Realign & reslice functionals to mean functional (pre-dartel; parfor parallelization)
2) Co-register MBW to mean functional (pre-dartel; parfor parallelization)
3) Co-register MPRAGE to MBW (pre-dartel; parfor parallelization)
4) Segment & bias-correct structural, generate segment params for DARTEL  (pre-dartel; implicitly parallaleized)
5) Create DARTEL templates & generate deformation fields for MNI normalization
6) Normalize functionals to MNI space via DARTEL (implicit parallelization from here)
7) Smooth functionals with FWHM kernel via DARTEL
8) Normalize bias-corrected MPRAGE to MNI space via DARTEL
9) Normalize grey matter (C1) segmentation to MNI space via DARTEL
10) Normalize white matter (C2) segmentation to MNI space via DARTEL
11) Normalize CSF (C3) segmentation to MNI space via DARTEL
