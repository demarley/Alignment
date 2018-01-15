30 October 2017  
Luca Pernie  
Dan Marley  


# Alignment

## Setup your environment:

```
# Build CMSSW Environment
SCRAM_ARCH=slc6_amd64_gcc530; export SCRAM_ARCH;
export RELEASE=CMSSW_9_2_6

cmsrel $RELEASE
cd $RELEASE/src/
cmsenv

# Add packages for alignment
git cms-addpkg Alignment/CommonAlignmentMonitor
cd Alignment
git clone https://github.com/demarley/Alignment.git -b $RELEASE

# To obtain the actual code, you will lose the git repo in this step, needs to be revisited...
mv Alignment/MuonAlignmentAlgorithms/ .
rm -rf Alignment
cd ../
git clone https://github.com/cms-mual/TrackingTools.git -b $RELEASE
git clone https://github.com/cms-mual/MuAlSupplementaryFiles.git -b CMSSW_9_0_X

# link files
ln -s Alignment/MuonAlignmentAlgorithms/scripts/createJobs.py
ln -s Alignment/MuonAlignmentAlgorithms/scripts/submitJobs.py
ln -s Alignment/MuonAlignmentAlgorithms/scripts/runBatchJobMonitor.py
ln -s Alignment/MuonAlignmentAlgorithms/python/gather_cfg.py
ln -s Alignment/MuonAlignmentAlgorithms/python/align_cfg.py
scram b -j6
```

### Additional folders (single branch)
```
git clone https://github.com/cms-mual/MuAlPhysicsValidation.git
git clone https://github.com/cms-mual/PlottingTools.git
```



## Data Alignment

_Note: for CSC, the `--T0` option is not needed_

```
./createJobs.py config.txt
./submitJobs.py config.txt
```

where `config.txt` is the configuration file that maintains the list of 
options you need when running the alignment.


### Add validation plots to the validation browser:

```
cd /afs/cern.ch/cms/CAF/CMSALCA/ALCA_MUONALIGN/www/browser_plots/validation    
./add_tarballs.sh <PATH_TO_TARBALL_MADE_BY_ALIGNMENT_JOB>
```

### JSON File location:**   
```
ls /afs/cern.ch/cms/CAF/CMSCOMM/COMM_DQM/certification/Collisions17/13TeV/PromptReco/Cert_294927-297723_13TeV_PromptReco_Collisions17_JSON.txt #json files
ls /afs/cern.ch/cms/CAF/CMSALCA/ALCA_MUONALIGN/www/muonGeometries/ #official muon geoemtries
```

**Notes for the changing release**
1. Get Correct architecture and download the relase:
```
SCRAM_ARCH=slc6_amd64_gcc630; export SCRAM_ARCH; cmsrel CMSSW_9_3_0_pre5; cd CMSSW_9_3_0_pre5/src/; cmsenv;
```
2. Check the differences between you code and the one in the release.
3. If you are not sure about what should be different, check the difference in the old release and the code you have locally there. Those should be the the only difference you also have with the following command.
```
diff -r ../../CMSSW_9_2_6/src/TrackingTools/TrackRefitter/ $CMSSW_RELEASE_BASE/src/TrackingTools/TrackRefitter/ > diff_TrackingTools
```
4. It should have only one file, where hits in muon system are neglected, and you do the fit 3 times.   
```
diff -r ../../CMSSW_9_2_6/src/Alignment/CommonAlignmentMonitor/ $CMSSW_RELEASE_BASE/src/Alignment/CommonAlignmentMonitor/ > diff_CommonAlignmentMonitor   
```
5. Should be identical. For some reason you connot remove this package, so just check your version is identical to the one in the repository.
```
 diff -r ../../CMSSW_9_2_6/src/Alignment/MuonAlignmentAlgorithms/ /cvmfs/cms.cern.ch/slc6_amd64_gcc530/cms/cmssw-patch/CMSSW_9_2_5_patch2/src/Alignment/MuonAlignmentAlgorithms/ > diff_MuonAlignmentAlgorithms     
```
6. Many dfferences here. You can check one by one if they are expected, or you can compare olde release and new release, and make the changes in your code    
```
diff -r $CMSSW_RELEASE_BASE/src/Alignment/MuonAlignmentAlgorithms/  /cvmfs/cms.cern.ch/slc6_amd64_gcc530/cms/cmssw-patch/CMSSW_9_2_6/src/Alignment/MuonAlignmentAlgorithms/ > diff_MuonAlignmentAlgorithms
```

