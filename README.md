# Quantifying Alzheimer's Disease Progression Through Automated Measurement of Hippocampal Volume

Alzheimer's disease is a progressive neurodegenerative disorder that results in impaired neuronal function and eventually, cell death. It is, in fact, the most common cause of dementia and is clinically characterized by memory loss, inability to learn new material, loss of language function, and other manifestations.

For patients exhibiting early symptoms, quantifying disease progression over time can help direct therapy and disease management.

A radiological study via MRI exam is currently one of the most advanced methods to quantify the disease. In particular, the measurement of hippocampal volume has proven useful to diagnose and track progression. Research has shown reduced volume of the hippocampus in patients.

The hippocampus is a critical structure of the human brain that plays an important role in information consolidation, memory and learning. 

Humans have two hippocampi, one in each hemishpere of the brain. They are located in the medial temporal lobe of the brain. The word "hippocampus" is roughly translated from Greek as "horselike" because of the similarity to a seahorse, a peculiarity observed by one of the first anatomists to illustrate the structure.

According to https://www.sciencedirect.com/science/article/pii/S2213158219302542, the volume of the hippocampus varies in a population, depending on various parameters, within certain boundaries, and it is possible to identify a "normal" range when taking into account age, sex and brain hemisphere.

But the technical challenge with the measurement of the volume of hippocampus using 3D MRI scans is that every slice needs to be analyzed and the non-uniform shape is hard to quantify. There is hence a need to develop algorithms that could help clinicians perform this task faster and more consistently.

In this project, we will focus on the technical aspects of building a segmentation model and integrating it into the clinician's workflow, leaving the dataset curation and model validation questions largely outside the scope of this project.

## Dataset

We are using the "Hippocampus" dataset from the Medical Decathlon competition(http://medicaldecathlon.com/) which is a collection of NIFTI files, with one file per volume, and one file per corresponding segmentation mask. 

In order to realistically emulate the clinical network, we configure the following tools and frameworks in our workspace interface. 

    Orthanc server for PACS emulation
    OHIF zero-footprint web viewer for viewing images. 
    OHIF to read data from server(https://docs.ohif.org/configuring/data-source.html)
    We also configure Orthanc for auto-routing of studies to automatically direct them to the AI algorithm (check .lua script). Ref: https://book.orthanc-server.com/users/lua.html
    DCMTK tools for testing and emulating modalities. 
  
In order to understand specifics of installations and configurations, refer to my medium post here: https://medium.com/@GaganaB/clinical-workflow-integration-with-orthanc-and-ohif-viewer-38930a3e94de

Executing section 2:
```python3 run_ml_pipeline.py```

Launching tensorflow (from /section2/):
```tensorboard --logdir runs --bind_all```

Logs are written to /runs/ and default port is 6006.

Integrating into clinical network is important because: While hospital integrations are typically handled by hospital IT staff, it will help tremendously to communicate in the same language.

Specifically, we have the following software in this setup:

    MRI scanner is represented by a script section3/src/deploy_scripts/send_volume.sh. When you run this script it will simulate what happens after a radiological exam is complete, and send a volume to the clinical PACS. Note that scanners typically send entire studies to archives.
    PACS server is represented by Orthanc deployment that is listening to DICOM DIMSE requests on port 4242. Orthanc also has a DicomWeb interface that is exposed at port 8042, prefix /dicom-web. There is no authentication and you are welcome to explore either one of the mechanisms of access using a tool like curl or Postman. Our PACS server is also running an auto-routing module that sends a copy of everything it receives to an AI server. See instructions ad the end of this page on how to launch if you are using the Udacity Workspace.
    Viewer system is represented by OHIF. It is connecting to the Orthanc server using DicomWeb and is serving a web application on port 3000. Again, see instructions at the end of this page if you are using the Udacity Workspace.
    AI server is represented by a couple of scripts. section3/src/deploy_scripts/start_listener.sh brings up a DCMTK's storescp and configures it to just copy everything it receives into a directory that you will need to specify by editing this script, organizing studies as one folder per study. 
    
Once you complete the code, you can test it by running

    deploy_scripts/send_volume.sh

which will simulate a completion of MRI study and sending of patient data to our PACS, and then following that by running inference_dcm.py in /section3/

```python3 inference_dcm.py``` 

If you experience any problems or code reproducibility concerns, feel free to open an issue here. 





