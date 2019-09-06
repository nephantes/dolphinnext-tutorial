Introduction
========

DolphinNext is an easy-to-use platform for creating, deploying, and executing complex nextflow pipelines for high throughput data processing.

DolphinNext provides: 
--------

1. A drag and drop user interface to build nextflow pipelines
2. Reproducible pipelines with version tracking 
3. Seamless portability to different computing environments with containarization
4. Simplified pipeline sharing using GitHub (github.com)
5. Support for continues integration and tests (travis-ci.org)
6. Easy re-execution of pipelines by copying previous runs
7. Integrated data analysis and reporting interface with R markdown support

Summary
========

Our aim is;
--------

1. Reusability
2. Reproducibililty
3. Shareability
4. Easy execution
5. Easy monitoring
6. Easy reporting


Expected learning outcome
========

To understand the basics of DolphinNext, how to use pipeline builder for different objectives and to familiarize yourself with some standard software packages for such analysis.


Before you start
========

DolphinNext docker version
--------

DolphinNext can be run standalone using a docker container.
First docker image need to be build unless you want to use prebuild from dockerhub. So, any change in the Dockerfile requires to build the image. But in this tutorial we will pull it and start the container.
*Note: If you don't have docker installed, please go to http://dolphinnext.umassmed.edu and let us know about it (biocore@umassmed.edu). We will set an account for you.

Pull the docker image
---------

1. Pull DolphinNext-studio

  docker pull ummsbiocore/dolphinnext-studio


Start the container
---------

1. We move database outside of the container to be able to keep the changes in the database everytime you start the container.
Please choose a directory in your machine to mount. For example, I will use ~/export directory for this purpose.

sudo mkdir -p ~/export

2. While running the container;

docker run --privileged -m 10G -p 8080:80 -v ~/export:/export -ti ummsbiocore/dolphinnext-studio /bin/bash

3. After you start the container, you need to start the mysql and apache server usign the command below;

startup

4. Now, you can open your browser to access DolphinNext using the url below.

http://localhost:8080/dolphinnext

Tutorial guide
==============

This guide will walk you through how to start using DolphinNext pipelines and creating new pipelines.

Short Overview
==============

[![Watch the video](dolphinnext_images/0.jpg)](https://youtu.be/1ak1m5pvkw4)

Getting Started
===============

First, you need to access DolphinNext web page: https://localhost:8080/dolphinnext and click **Sign Up** or **Sign in with Google** buttons. You will be asked to enter some information about your institution, username, etc. 


<img src="dolphinnext_images/sign_in.png">

Once you login, you will be the administrator of this mirror. You can add more users to your system and manage them from profile/admin section.


Excercise 1 (Creating processes)
===============

[![Watch the video](dolphinnext_images/0.jpg)](https://youtu.be/d6jkg1l7FgA)

Once logged in, click on the pipeline tab in the top left of the screen to access pipeline builder page. 

<img src="dolphinnext_images/build1-builderpage.png">

The process we will create in this excercise;
---------------
1. FastQC process
2. Hisat2 process
3. RSeQC process

You’ll notice several buttons at the left menu. New processes are created by clicking green “New process” button.

1. FastQC process:
---------------
a. Please enter FastQC for the process name and define a new "Menu Group". We will add the processes into this group in the sidebar.

<img src="dolphinnext_images/build2-fastqc-addmenugroup.png">

b. In the FastQC process, we have an input, an output and a line of a command we are going to use to execute the fastqc process. 

```
Inputs: reads(fastq,set) name: val(name),file(reads)
Outputs: outputFileHTML(html,file) name: "*.html"
Script:
   fastqc ${reads}
```

c. Let's add input and output parameters using "+" button in "Parameters" section;

<img src="dolphinnext_images/build3-fastqc-addnewparam-reads.png">   
<img src="dolphinnext_images/build4-fastqc-addnewparam-outputFileHTML.png">   

d. After both parameters created. Lets select them and define their "Input Names" that we are going to use in the script section

<img src="dolphinnext_images/build5-fastqc-parameters.png">
 
e. Let's enter the script section

<img src="dolphinnext_images/build6-fastqc-script.png">

f. Press "Save changes" button at the bottom of the modal to create the process. Now this process is ready to use. We will use it in the Excercise 2.

2. Hisat2 process:
---------------
Let's create Hisat2 process. 
a. First, please click, green “New process” button to open "Add New Process" modal.

b. Enter process name "Hisat2"

c. Select "Menu Group"; Tutorial.

d. Select "reads" for the first "Input Parameter" and enter  val(name),file(reads) for its "Input Name"

e. Second we need to define another input parameter to get index prefix for Hisat2 aligner. To do that, press "+" button next to "Parameters" section and enter "genomeIndexPath" 

<img src="dolphinnext_images/build7-hisat2-addnewparam-hisat2Index-prefix.png">

The input parameters should look like below;

<img src="dolphinnext_images/build8-hisat2-inputs.png">

f. For output parameters, we should define "mapped_reads" by adding a new parameter.

<img src="dolphinnext_images/build9-hisat2-addnewparams-mapped_reads.png">

and select this parameter for "Output Parameter" section and enter val(name), file("${name}.bam") for its "Ouput Name".

e. We will add one more parameter for hisat2 alignment summary as an example output in txt format. To be able to reuse parameters we try to choose generic names. In this case we will use "outputFileTxt".

<img src="dolphinnext_images/build10-outputFileTxt.png">

f. Inputs, outputs and scripts should be defined like below;

```
Inputs: reads(fastq,set) name: val(name),file(reads)
        hisat2IndexPrefix(val) name: hisat2Index
        
Outputs: mapped_reads(bam,set) name: val(name), file("${name}.bam")
         outputFileText(txt,set) name: val(name), file("${name}.align_summary.txt")

Script:
hisat2 -x ${hisat2Index} -U ${reads} -S ${name}.sam &> ${name}.align_summary.txt
samtools view -bS ${name}.sam > ${name}.bam

```

<img src="dolphinnext_images/build11-alignment-summary.png">

g. please, Save changes before you close the screen.

3. RSeQC process:
---------------





Excercise 2 (Building a pipeline)
===============

Once logged in, click on the pipeline button in the top left of the screen. You’ll notice Enter Pipeline Name box, just below the Pipelines button.

Excercise 3 (Executing a pipeline)
===============

[![Watch the video](dolphinnext_images/0.jpg)](https://youtu.be/gaq_LwewFPA)

Once a pipeline is created, you will notice “Run” button at the right top of the page.

<img src="dolphinnext_images/project_runbutton.png" width="50%">

This button opens a new window where you can create a new project by clicking “Create a Project” button. After entering and saving the name of the project, it will be added to your project list. Now you can select your project by clicking on the project.
You will then proceed by entering run name which will be added to your run list of the project. Clicking “Save run” will redirect you to the “run page” where you can initiate your run.





