# Introduction to Archivematica

## Table of Contents
[Workshop Overview](#workshop_overview)<br />
[Archivematica Overview](#archivematica_overview)<br />
[Archivematica's Components in Detail](#components_in_detail)<br />
[Automating Archivematica](#automating_archivematica)<br />
[Preservation Planning](#preservation_planning)<br />
[Long-Term Managmement of Preserved Content](#longterm_manangement)<br />
[About This Workshop](#about_this_workshop)

<a name="workshop_overview"/>
## Workshop Overview

This workshop is an introduction to using [Archivematica](https://www.archivematica.org) as a digital preservation platform, with special attention paid to integrating Archivematica into existing content management and production workflows. It will also cover basic preservation planning and long-term management of preserved content. The workshop includes a small number of hands-on exercises.

The intended audience is library, archives, and museum staff who are starting to plan (or are in the process of planning) a digital preservation program for their insitution. The workshop content describing integration and automation may be of interest to Information Technology staff as well.

The workshop does not duplicate the [Archivematica wiki](https://archivematica.org); rather, it should be used in conjuction with the wiki, in paticular the version 1.0 User and Administrator Manuals. The wiki should be considered the authoritative source for information on Archivematica. 

<a name="archivematica_overview"/>
## Archivematica Overview

### OAIS in microservices

Archivematica does not provide everything a library, archives, or museum needs to start preserving digital content. It does not supply an overall digital preservation policy that provides guidelines on what conent to preserve, stipulate how long to preserve the content, describe how your organization is going to fund preservation of the content, or define what happens when the organization is no longer able to preserve its content.

What Archivematica does do is provide all the software tools necessary for implementing pragmatic digital preservation strategies defined by a preservation policy. It is a "processing pipeline" that uses open-source tools to implement the [Open Archival Information System](http://en.wikipedia.org/wiki/Open_Archival_Information_System) (OAIS) Reference Model.

In the OAIS model, content to be preserved is ingested into the system as Submission Information Packages (SIPs), is stored and maintained over time as Archival Information Packages (AIPs), and is made accessible to users as Dissemination Information Pacakages (DIPs), as illustrated in this well-known diagram:

![OAIS Functional Model](https://www.archivematica.org/mediawiki/images/thumb/d/d4/OAIS.png/800px-OAIS.png)

Archivematica integrates a number of open-source tools in a set of "microservices" - small scripts or programs that do one thing but are chained together to form the "pipeline" mentioned above. SIPs, AIPs, and DIPs within Archivematica are all created using microservices. Each microservice takes the output of the previous microservice, does something to this output such as generate a checksum or update some metadata, and saves its own output in a location where the next microservice in the pipeline expects to find its input.  

We will cover microservices in detail later in the workshop, but feel free to look at [a list of Archivematica's microservices](https://www.archivematica.org/wiki/Archivematica_1.0_Micro-services) before we move on.

### The Dashboard

Archivematica's user interface is called the Dashboard. Its two basic functions are:

1. to allow users to add content to Archivematica and
2. to allow users to choose from among the digital preservation options applicable to the content they or others have added.

The Dashboard is fairly simple. For the most part, it provides a list of microservices and prompts the user for a decision when one needs to be made:

![Archivematica Dashboard](https://www.archivematica.org/mediawiki/images/thumb/1/1a/CreateSIPs-10.png/800px-CreateSIPs-10.png)

The Dashboard organizes the workflow into the main functional componenents of the OAIS model: Transfer, Ingest, Archival storage, Access, and Administration. Depending on the permissions granted to the user who is logged in, some of these tabs might not be visible. 

### Format Policy Registry
One of Archivematica's strengths is that it provides a comprehensive set of tools for identiying and normalizing content files into standard formats that are recognized by the broad digital preservation community as open, sustainable, and as future-proof as any formats can be. Archivematica runs these tools against your content as microservices, and defines rules about which tool is applied to which types of original files. A high-level list of media types, corresponding file formats, and normalization options [is available on the Archivematica wiki](https://www.archivematica.org/wiki/Format_policies), but the rules maintained by Archivematica are much more detailed (we will look at some of these rules in detail later).

The component of Archivematica that manages these rules is called the Format Policy Registry, or FPR. [Artefactual Systems](http://www.artefactual.com/) maintains the FPR on behalf of the Archivematica community and hosts the cannonical version on their servers. When you first install Archhivematica, it copies the most recent version of the FPR from this source, but you can also update it manually.

The FPR allows institutions to add their own rules about how files are identified and normalized. These local rules are not overwritten if the FPR is updated from Artefactual. Rules in the FPR are based on general accepted practice in the digital preservation community, so organizations should not add their own rules (or tools) without good reason. However, the ability to add local rules is a good illustration of Archivematica's flexibility.

### Access to preserved content

Adhering to the OAIS Reference Model, Archivematica provides means for making preserved content accessible to end users. Currently, content can be made available through three access platforms: [AtoM](https://www.accesstomemory.org), [Archivists' Toolkit](http://www.archiviststoolkit.org/), and [CONTENTdm](http://contentdm.org). Users determine which access platform to use to make content available during one of the workflow steps in the Dashboard.

Institutions can also choose not to make content available to end users, in effect making Archivematica a dark archive. In this case, Archivematica may be implemented in a workflow that is parrallel or separate from those that make content available to end users. AIPs remain available via the Archivematica Dashboard's Archival storage tab, but only to users that have direct access to the Dashboard. AIPs are also available via the filesystem where they are stored.

### Standards implementation and compliance

Archivematica relies heavily on technologies and standards accepted in the general digital preservation community. Four such standards that Archivematica implements are:

* [BagIt](http://en.wikipedia.org/wiki/BagIt) is a specification for packaging directories of files for long-term storage or for transfer between storage environments. Its most important feature is that it generates and records checksums for every file stored in a Bag, which makes it very easy to verify the integrity of those files after they have been moved. Archivematica stores it Archival Information Packages (AIPs) as Bags, and can ingest Bags created by other systems.
* [METS](http://www.loc.gov/standards/mets/) is an XML schema for representing the descriptive, administrative, and structural metadata of digital objects, in effect wrapping all of these types of metadata in one file. Archivematica uses METS to encode all of the metadata generated about a set of related objects into one file. This METS file, along with the normalized and original object files, are what are inside the Bagged AIPs.
* [PREMIS](http://www.loc.gov/standards/premis/) provides a vocabulary for recording the history of digital object as they are managed over time within a preservation system. It records the events that happen to objects (such as ingestion into the system, virus checks, conversion between formats, and checksum verifications), the agents (people, software, organizations) that perform those events, and the technical characteristics of the objects themselves (including file format, size, and resolution). Archivematica generates PREMIS for objects it preserves, and adds it to the METS file describing those objects.
* [UUIDs](http://en.wikipedia.org/wiki/Universally_unique_identifier) are identifiers that can be used by multiple systems but that do not require any central coordination. In practical terms they are 36-character strings of alphanumeric characters that are guaranteed to be unique (or more accurately, the chance of a duplicate UUID is [so low](http://en.wikipedia.org/wiki/Universally_unique_identifier#Random_UUID_probability_of_duplicates) that it is statistically unimportant). Archivematica assigns UUIDs to almost everything it manages, including files, processes, and storage locations.

### Version status and development roadmap

Version 1.0 was released in January 2014. Version 1.1 is scheduled for release in late April 2014. This version of the development roadmap for Archivematica, taken from the [wiki](https://www.archivematica.org/wiki/Development_roadmap:_Archivematica), outlines the features that are planned for the next few releases.

* Archivematica Version 1.1
    * [Manual normalization](https://www.archivematica.org/wiki/UM_manual_normalization_1.1)
    * [Dataset features](https://www.archivematica.org/wiki/Dataset_preservation)
    * Better handling of preconfigured workflows
    * Performance improvements (e.g. FITS 0.8)
* Storage Service 0.4.0
    * [LOCKSS integration](https://www.archivematica.org/wiki/LOCKSS_Integration)
    * [Islandora integration](https://www.archivematica.org/wiki/Sword_API)
    * Configure transfer backlog locations
    * Post-store-AIP microservices
* Archivematica Version 1.2
    * [Forensic disk image ingest](https://www.archivematica.org/wiki/Forensic_imaging_steps_for_1.1)
    * Better identification and characterization
* Archivematica Version 1.3
    * Create one ore more SIPs from one or more transfers
    * METS generation improvements
* Archivematica Version 1.4
    * [AIP Re-ingest](https://www.archivematica.org/wiki/AIP_re-ingest) 
    * OCR
    * [Store DIP](https://www.archivematica.org/wiki/DIP_storage_to_designated_location)
* Wishlist
    * Visualization of transfer contents
    * BitCurator integration
    * https://www.archivematica.org/wiki/Development_roadmap:_Archivematica#Wish_list

<a name="components_in_detail" />
## Archivematica's Components in Detail

### Format Policy Registry
Identifying a file's "format" accurately is one of the most challenging and important tasks a digital preservation system needs to do. Accurate format identification is important because processes to normalize a file differ widely from format to format (for example, in files of the same "format" created by software from two different vendors), and the reliability of emulation technologies depends heavily on accurate format identification. It is difficult because there are so many formats and variations within formats, and because accurately identifying a file's format is much more complicated than looking at its file extension (which often have nothing to do with the file's format).

Archivematica manages the complexity of file formats in its Format Policy Registry (FPR). As the User Manual [explains](https://www.archivematica.org/wiki/UM_FPR_1.0), the FPR:

* allows Archivematica users to define format policies for handling file formats
* stores structured information about normalization format policies for preservation and access

This structured information is used by Archivematica's microservices and in metadata stored with the files in Archival Information Packages.

As mentioned earlier, Artefactual Systems maintains a version of the FPR that is copied into local Archivematica instances for local use and customization. The advantages that libraries, archives, and museums gain from implementing Archivematica's format policies are:

1. the policies are accepted as best practice by the general digital preservation community
2. organizations do not need to spend time and resources researching the best way to identify or normalize specific file types, and
3. the format policies can be applied at scale reliably using Archivematica's toolset; in other words, each policy has been tested to determine how well it performs on large files or on many files within a single microservice.

Details of the FPR are available on the [wiki](https://www.archivematica.org/wiki/UM_FPR_1.0). This list summarizes the types of entries it contains.

* Format: "a record representing one or more related format versions, which are records representing a specific file format. For example, the format record for 'Graphics Interchange Format' (GIF) is comprised of format versions for both GIF 1987a and 1989a"
* Format group: "a convenient grouping of related file formats which share common properties. For instance, the FPR includes an 'Image (raster)' group which contains format records for GIF, JPEG, and PNG. Each format can belong to one (and only one) format group."
* Identification Tools: Archivematica currently offers two tools for identifying files, one based on the Open Planets Foundation's [FIDO](https://github.com/openplanets/fido/) and one that uses file extensions.
* Identification Commands: "Identification commands contain the actual code that a tool will run when identifying a file. This command will be run on every file in a transfer."
* Identification Rules "allow you to define the relationship between the output created by an identification tool, and one of the formats which exists in the FPR"
* Format Policy Tools "control how Archivematica processes files during ingest"
* Format Policy Commands are "scripts or command line statements which control how a normalization tool runs"
* Format Policy Rules associate commands with specific file types.

#### Exercise 1: Look at some Format Policy Rules and associated commands

1. Log into the Archivematica Dashboard and click on the "Preservation planning" tab.
2. Under "Normalization" on the left, click on "Rules".
3. To view the rule details, click on the the "View" link on the right.
4. To view the command used in the rule, click on the link under the "Command" heading.

### Microservices

We defined microservices earlier: small tools that perform one task and that are chained together so that the output of one becomes the input of the next. 

During the early development of Archivematica, Artefactual staff analyzed the OAIS specification and drew out a number of distinct [use cases](https://www.archivematica.org/wiki/OAIS_Use_Cases) that a OAIS-compliant system would need to meet. The use cases clustered into the following categories, and Artefactual defined a set of microservices that would need to exist to fulfill them:

| Category | Microservice |
| --- | --- |
| 1. receiveSIP | verifyChecksum |
| 2. reviewSIP | extractPackage<br />assignIdentifier<br />parseManifest<br />cleanFilename |
| 3. quarantineSIP | lockAccess<br />virusCheck |
| 4. appraiseSIP |  identifyFormat<br />validateFormat<br />extractMetadata<br />decidePreservationAction |
| 5. prepareAIP |  gatherMetadata<br />normalizeFiles<br />createPackage |
| 6. reviewAIP | decideStorageAction |
| 7. storeAIP | writePackage<br />replicatePackage<br />auditFixity<br />readPackage<br />updatePackage  |
| 8. provideDIP | uploadPackage<br />updateMetadata |
| 9. monitorPreservation | updatePolicy<br />migrateFormat |

<small>Source: Peter Van Garderen, "[Archivematica: Using Micro-Services And Open-Source Software To Deliver A Comprehensive Digital Curation Solution](http://www.ifs.tuwien.ac.at/dp/ipres2010/papers/vanGarderen28.pdf)" iPres Proceedings (Sept. 2010).</small>

This early analysis identified the workflow implicit in the OAIS model and the types of tasks that would be required to complete that workflow. Now that Archivematica has evolved to production-ready software, the types of microservices that it implements have been refined extensively. They are listed on the [Archivematica 1.0 Micro-services](https://www.archivematica.org/wiki/Archivematica_1.0_Micro-services) page on the wiki.

The benefit of microservices is that they are easy to replace, improve, or upgrade without jeopoardizing the rest of the system. The only requirements that a microservice has are that it can:

1. use the input of the previous microservice (if necessary) to perform its task and
2. produce output that allows the next microservice to perform its task.

An illustration of this principle is the "Scan for viruses" microservice. Currently, Archivematica uses [ClamAV](http://www.clamav.net/) to check files in a transfer for viruses. The microservice that performs this action is a Python script that provides a list of files to an external anti-virus application (ClamAv). Should ClamAV become unsupported, or if more accurate open-source antivirus software supercedes it, swapping out ClamAV for the new virus checker will be relatively easy. The only part of Archivematica that will need to be modified is a simple Python script.

### Transfers, SIPs, AIPs, and DIPs

Workflows in Archivematica are based on the creation of a transfer, converting it to a SIP (Submission Information Package), converting the SIP into an AIP (Archival Information Package) for long-term storage, and finally converting the AIP into a DIP (Dissemination Information Package) for access by end users:

![Transfer-to-DIP workflow](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/Transfer%20to%20DIP.png)

This pattern is consistent with the OAIS Reference Model, and as we saw in the Microservices section above, can be broken down into many small tasks. The exact nature of the transfers, SIPS, AIPs, and DIPs depends on choices made by the user (or automated using a configuration file, as we will see later in this workshop). This section will describe each of these types of packages in detail.

#### Transfers

As stated on the Archivematica [wiki](https://www.archivematica.org/wiki/UM_transfer_1.0), "Transfer is the process of transforming any set of digital objects and/or directories into a SIP. Transformation may include appraisal, arrangement, description and identification of donor restricted, private or confidential contents." 

Stated another way, transfer is the workflow stage where content is prepared for adding to Archivematica. Common ways of preparing content include organizing content files in folders, creating metadata describing the content files, and adding "submission documentation" that should be preserved with the content such as donor agreements, licenses, and so on. The exact nature of preparing content for preservation will vary from institution to instution, and will depend on the type of content. For example, an archives may apply standard arrangement practices to the content in a transfer, whereas a library that has digitized a large number of newspapers may prepare transfers that will produce DIPs for the [CONTENTdm](https://www.archivematica.org/wiki/CONTENTdm) access system.

Archivematica allows for a fairly loose arrangement of files in a transfer, but also defines several structured transfer types. This flexibility enables insitutions to integrate the creation of transfers into upstream workflows. To illustrate this range of options, we will look at several transfer packages taken from the samples available on the [Artefactual GitHub account](https://github.com/artefactual/archivematica-sampledata/tree/master/SampleTransfers):

```
Multimedia/
├── 0239.mpg
├── BigTeen Short1.mp3
├── BlastOff.wmv
├── funky_breakbeat_4.wav
├── j6059_02.wma
├── MakeUp.mov
└── sample.aif

0 directories, 7 files
```
In this "Multimedia" sample, the files being transferred have simply been added to a directory. No additional metadata or submission documentation files are included.

```
CSVmetadata/
├── Landing zone.jpg
├── MARBLES.TGA
└── metadata
    ├── metadata.csv
    └── submissionDocumentation
        └── [datavibe-l]_FW__job_vacancy.rtf

2 directories, 4 files
```
This "CSVmetadata" sample illustrates how [file-level metadata](https://www.archivematica.org/wiki/UM_Transfer_metadata_import_1.0) can be added to a transfer; basically, if a file with the name "metadata.csv" exists in a folder with the name "metadata", Archivematica will read the contents of the file and associate the metadata with the referenced files. 
```
DigitizationOutput/
├── logs
├── metadata
│   ├── file_labels.csv
│   └── submissionDocumentation
│       └── [datavibe-l]_FW__job_vacancy.rtf
└── objects
    ├── access
    │   ├── P1050152.gif
    │   ├── P1050154.gif
    │   ├── P1050155.gif
    │   └── P1050156.gif
    ├── P1050152.JPG
    ├── P1050154.JPG
    ├── P1050155.JPG
    ├── P1050156.JPG
    └── service
        ├── P1050152.JPG
        ├── P1050154.JPG
        ├── P1050155.JPG
        └── P1050156.JPG

6 directories, 14 files
```
A more complex type of structured transfer is the "[digitization output](https://www.archivematica.org/wiki/UM_digitization_output_1.0)" transfer, which is useful if an institution has  master and derivative digitized objects that it wants to preserve.

The next transfer is not from the samples provided by Artefactual. We incldue it here to illustrate how a simple transfer (in this case, containing one zip file which in turns contains two text files), changes as it is transformed into an AIP and then a DIP:

```
myblobs
├── metadata
├── objects
    └── blobs.zip

2 directories, 1 file
```

#### Exercise 2: Look at some of the sample transfers

In this exercise, we will look at some of the files in the sample transfers depicted above by viewing the versions provided on the Artefactual Github repository. Note that some files may not display or play properly in your browser.

* [Multimedia sample transfer](https://github.com/artefactual/archivematica-sampledata/tree/master/SampleTransfers/Multimedia)
* [CVS metadata](https://github.com/artefactual/archivematica-sampledata/tree/master/SampleTransfers/CSVmetadata)
* [Digitization output](https://github.com/artefactual/archivematica-sampledata/tree/master/SampleTransfers/DigitizationOutput)

#### SIPs
The SIP is basically a transfer that is being prepared to become an AIP. In a SIP, filenames have been normalized, files scanned for viruses, unique identifiers (in the form of UUIDs) assigned, and various sorts of metadata generated and assembed into a METS document. Users of Archivematica do not typically view or access SIPs before they are transformed into AIPs.

#### AIPs

Content is packaged for long-term storage in AIPs. The strucutre of Archivematica AIPs is [documented thoroughly](https://www.archivematica.org/wiki/AIP_structure) on the wiki, but an illustration of the AIP generated from our example transfer looks like this:

```
myblobs
├── bag-info.txt
├── bagit.txt
├── data
│   ├── logs
│   │   ├── clamAVScan.txt
│   │   ├── fileFormatIdentification.log
│   │   └── transfers
│   │       └── blobs2-8b8b8782-8ce8-45d8-8c1c-356ff59cd15d
│   │           └── logs
│   │               ├── clamAVScan.txt
│   │               ├── fileFormatIdentification.log
│   │               └── fileMeta
│   ├── METS.deda9a71-da64-4c5c-80f6-687dd874c102.xml
│   ├── objects
│   │   ├── blobs.zip-2014-04-23T16_59_22.016503
│   │   │   └── blobs
│   │   │       ├── 1.txt
│   │   │       └── 2.txt
│   │   ├── metadata
│   │   │   └── transfers
│   │   │       └── blobs2-8b8b8782-8ce8-45d8-8c1c-356ff59cd15d
│   │   └── submissionDocumentation
│   │       └── transfer-blobs2-8b8b8782-8ce8-45d8-8c1c-356ff59cd15d
│   │           └── METS.xml
│   └── thumbnails
│       ├── a26148c4-e889-42ff-8c5b-944208e694ba.jpg
│       └── a3e0e441-3574-487e-8aeb-f82c93eb63bc.jpg
├── manifest-sha512.txt
├── tagmanifest-md5.txt

15 directories, 14 files
```

A lot has been added to our oringal transfer package consisting of only of one Zip file, including various logs generated during the SIP and AIP workflows, a METS document, and some thumbnail images. Most of the other files added to the AIP are required by BagIt. One of these files, manifest-sha512.txt, is worth looking at, because it documents checksums for each of the content files in the AIP:

```
e8b4778f77cb088c5771db433761d1c4e6d019dd9a84817064b49684111320321daa7f55b775ad04eebd3f72bf369198d3a29c98c50fc04c16c19c6f4d28c1db  data/logs/transfers/blobs2-8b8b8782-8ce8-45d8-8c1c-356ff59cd15d/logs/clamAVScan.txt
de0d5f8324ee4424d0fd44fc15ec6a8402283ed460c36ac8fb3eac7ddd092391da6bc2b2aea587165ad8e5ecb1e0bd9d7844f1035a993377817dda66e8ca5a9d  data/objects/blobs.zip-2014-04-23T16_59_22.016503/blobs/2.txt
613dac5904bbf128b2d1d943ff426218fa77da3ca6fb569c19bcd63f35c651597504c440b683cc23d336f7ea7c572bf75a21790027be00239b1d0ec1e6ee84df  data/METS.deda9a71-da64-4c5c-80f6-687dd874c102.xml
7ec99a0a5200626946b25afd67d002a7aa8549bdb61eb682aa0ce78704206f15ee51a217ba4170ae25a51281605c1e5b71829b8cb846d2b275a644924e779bff  data/thumbnails/a3e0e441-3574-487e-8aeb-f82c93eb63bc.jpg
391e70bf5b703ad406bfe6a0951048b8a9189962fb5d5ef2953f73e157a29fce172f422e149c0e4fda906615b4f5e6a7eaa22bd7dfbe64475b59c9edceb75381  data/objects/blobs.zip-2014-04-23T16_59_22.016503/blobs/1.txt
54c38c72373ebbdd81b2b7756285d51f1eeadc713648500e7a5bc66d7e81adccb4d9182611eca5dc30adf624d4c9d97764447bcd594e229c349a9ee24ebb2295  data/logs/fileFormatIdentification.log
db16dad8b4b8d7d0931b697b1e0aa239216d7e91b4af5660c36137d6c32715bcb89aefa7961a8399a03e75841f0a865fa3be2f1c071b5be32ed9dd168b6d8078  data/logs/transfers/blobs2-8b8b8782-8ce8-45d8-8c1c-356ff59cd15d/logs/fileFormatIdentification.log
8c75a77677f08e72c32f89b4b034608a0563a315be67ccae5b75e759646c0ed73acab49e9dbe54d6a59049bd18a2684d0076e3a7b067d03154b472f7d76fcd92  data/objects/submissionDocumentation/transfer-blobs2-8b8b8782-8ce8-45d8-8c1c-356ff59cd15d/METS.xml
7cf5c1af32675c13fd72584dd58b25cac032b2bf623efb5c543e0ef14e22fae364c91f5f2afdc0dff27965e1177e8d5ca4f7836b9e6df2c63af3394a61ed2c42  data/logs/clamAVScan.txt
7ec99a0a5200626946b25afd67d002a7aa8549bdb61eb682aa0ce78704206f15ee51a217ba4170ae25a51281605c1e5b71829b8cb846d2b275a644924e779bff  data/thumbnails/a26148c4-e889-42ff-8c5b-944208e694ba.jpg
```

This manifest file is meant to be readable primarily by software, not humans, but we can see a simple pattern in the rows and two columns that make up this file: the first column contains long strings of numbers and letters and the second column contains file paths. The string in each row is a checksum for the corresponding file; each checksum is a unique signature for the file and can be regenerated in the future to determine whether the file has changed in any way. The manifest is generated and included in the Bag to provide a way to determine that all the files listed in it are integral and are bit-for-bit copies of the orginal files. This is a very important type of metadata for supporting long-term preservation.

#### DIPs

The Dissemination Information Package (DIP) contains files and metadata that are required by a particular access system. Currently, Archivematica can create DIPs for loading into  [AtoM](https://www.accesstomemory.org), [Archivists' Toolkit](http://www.archiviststoolkit.org/), and [CONTENTdm](http://contentdm.org). Each of these systems has different requirements for what is included in their respective DIPs and for how it is organized. For example, the DIP generated from our example AIP for loading into CONTENTdm looks like this:

```
myblobs
├── compound.txt
└── scans
    ├── 1-a26148c4-e889-42ff-8c5b-944208e694ba.txt
    └── 2-a3e0e441-3574-487e-8aeb-f82c93eb63bc.txt

1 directory, 3 files
```
"Compound.txt" contains the metadata that was added manually in Archivematica by the user in the Dashboard, and the "scans" directory contains versions of the original files normalized for use in CONTENTdm (in this case, they are just text files but if the files in the transfer were TIFFs, for example, the files in "scans" would be JPEGs). The metadata in compound.txt is structured to be compatible with the specific metadata configuration of the target collection in CONTENTdm.

### Storage Service

Where are the AIPs stored for long-term preservation? Before moving on to an overview of the Dashboard tools used to ingest content and create AIPs, we will cover the Storage Service, a very important component of Archivematica. The Storage Service manages all of the storage required to process content and store it. Generally speaking, only Archivematica adminstrators can configure the Storage Service. The following high-level description is intended to provide a context for other topics covered in this workshop.

The Storage Service defines 1) pipelines, 2) spaces, 3) locations, and 4) packages. A pipeline is an instance of Archivematica, and a package is either a transfer or an AIP. A space is a type or kind of physical storage; the most common type is Local Filesystem, although Network Filesystem is also supported. Archivematica 1.1 will support [LOCKSS](http://www.lockss.org/) as a type of space. Each space contains one or more locations, which are in most cases ordinary directories on a filesystem.

Local administrators can create Storage Service configurations that allow the most efficient use of their storage and also allow multiple instances of Archivematica to share storage locations. We will discuss this feature later in the workshop.

### Dashboard

As stated earlier, the Dashboard is Archivematica's user interface. As mentioned earlier in the workshop, it organizes the user's workflow into the main functional componenents of the OAIS model: Transfer, Ingest, Archival storage, Access, and Administration. (A sixth tab, Preservation Planning, provides access to the Format Policy Registry.) In this screen snapshot, for example, the user is in the Transfer workflow:

![Sample Dashboard](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/archivematica_dashboard.png)

Before we describe the kinds of activities that happens in each part of the Dashboard, it is worth noting that Archivematica responds in several ways to errors that may occur while processing content in the Dashboard. These are [documented on the wiki](https://www.archivematica.org/wiki/UM_error_handling). The most obvious indication that an error has occured in the Dashboard is that the microservice that encountered the problem will be highlighted in pink:

![Example of an error in the Dashboard](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/archivematica_dashboard_error.png)

Clicking on the "Tasks" icon (a small picture of a gear) for that microservice will reveal details about the error:

![Example of an error detail](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/archivematica_dashboard_error_detail.png)

In the upcoming exercise, we will add some content to Archivematica, but before we do, an overview of the workflow tabs and how they realte to some topics we have already covered will be useful.

#### Transfer tab

We already know that "transfer", in Archivematica, is the process of transforming any set of files and/or directories into a SIP. The term can also be used to describe the set of files themselves. The user adds one or more transfers to a SIP via a file selection tool provided in the Dashboard:

![Selecting files to add to a transfer](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/choose_a_transfer.png)

One potentially confusing aspect of the file selection tool is that the files and directories are on the Archivematica server, and typically _not_ on your local workstation. Archivematica can be configured so that the server and user's workstation share a file system so that, from the user's perspective, files added to a transfer are accessible via a local directory.

As the wiki says, "Transformation may include appraisal, arrangement, description and identification of donor restricted, private or confidential contents." This includes preparing [file or folder-level metadata](https://www.archivematica.org/wiki/UM_Transfer_metadata_import_1.0) and adding submission documentation.

Archivematica accepts some pre-defined transfer formats, [Bags](https://www.archivematica.org/wiki/UM_Bags_1.0) and [DSpace exports](https://www.archivematica.org/wiki/UM_DSpace_exports_1.0). An example of using a Bag to structure a transfer is provided later in the workshop.

#### Ingest tab

During ingest, a number of microservices are applied to the SIP to 1) transform it into an AIP and 2) generate a DIP. Another important task is the addition of Dublin Core and PREMIS rights metadata to the AIP. This can be automated in some situations but is generally a manual operation using a tool provided within the Dashboard. The Dashboard user accesses this tool by clicking on the "Metadata" icon to the right of the SIP name:

![Adding metadata to an AIP](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/add%20AIP%20metadata.png)

#### Exercise 3: Transfer and ingest one or two of the sample transfers

Your instructor will walk you through this exercise.

#### Archival storage tab

After all Ingest microservices have completed, the AIP is moved into archival storage. This tab in the Dashboard provides a way to seach for specific AIPs, and links to download AIPs:

![Archival storage](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/archival_storage.png)

#### Exercise 4: Download some AIPs and browse their contents

Your instructor will walk you through this exercise.

#### Access tab

If the user selects "Normalize for preservation and access" or "Normalize for access" during the Ingest workflow, a DIP is generated and an option is provided to allow upload of the DIP to the selected access system: 

![Upload DIP](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/upload_dip.png)

### Pipelines

We have encountered the term "pipeline" several times already in this workshop. A pipeline in Archivematica is basically an instance of the Dashboard. Technically, it also contains what are known as the [MCP](https://www.archivematica.org/wiki/MCP) Server and one ore more MCP Clients, the backend components that manage the microservices used by the Dashboard.

This concept of pipelines is useful because a library, archive, or museum can run multiple pipelines at one time, each performing a specific set of processing tasks. Using multiple pipelines that all perform the _same_ tasks is also a common strategy for scaling Archivematica to handle large quantities of content simultaneously.

The following diagram illustrates a configuration in which an organization (say an large academic or public library with an attached archives) sets up separate pipelines for its archive and digitization center. It also sets up a third pipeline for processing a large amount of digital video.

![Pipelines and storage](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/Archivematica%20pipelines%20and%20storage.png)

This type of arrangement provides the following advantages:

* user accounts on each pipeline can be limited to staff in the archives, digitization center, or video project office
* each pipeline can have its own default processing configuration, allowing staff in different areas to focus on workflow decisions that are specific to the type of content they are processing
* each pipeline's default transfer directory can be connected to disk storage that is integrated with specialized upstream content production workflows (for example, the Digitization Center's transfer directory can be restricted to a directory that is part of a digitized output quality assurance workflow)
* since the server the video processing pipeline is installed on will need a lot of memory, it can be provisioned separately from the other two, which can use more modest amounts of memory
* the pipelines share a single Storage Service, which allows system administrators to manage all of the storage used by Archivematica in one place, and allows the archives and digitization center pipelines to share the same storage location.

Of course, using three separate pipelines means that the library must have access to three different servers, and their systems administrators need to maintain three instances of Archivematica.

<a name="automating_archivematica" />
## Automating Archivematica

The Dashboard allows users to approve transfers and to make workflow decisions as the content moves from transfer to SIP to AIP to DIP. It is possible to automate many of the user actions in this workflow. Archivematica offer several options.

### Preconfigure default workflow decisions
Archivematica comes with a default set of workflow decisions. It is possible to modify these defaults to suite your local needs in the "Processing configuration" tool in the Administration tab:

![Processing Configuration form](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/processing_configurattion_form.png)

Keep in mind that workflow options configurable here do not include the Start transfer and Approve transfer tasks. These must still be performed manually (or automated using the REST API, as descirbed below).

### Use custom processing configuration files
Processing configurations are stored in XML files. It is possible to override specific workflow decisions using a custom processing configuration file packaged within the transfer itself. Processing configuration files can apply to only one or two workflow actions, like this one:

```xml
<processingMCP>
  <preconfiguredChoices>
    <preconfiguredChoice>
      <appliesTo>Workflow decision - create transfer backup</appliesTo>
      <goToChain>Do not backup transfer</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Workflow decision - send transfer to quarantine</appliesTo>
      <goToChain>Skip quarantine</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Remove from quarantine</appliesTo>
      <goToChain>Unquarantine</goToChain>
      <delay unitCtime="yes">50</delay>
    </preconfiguredChoice>
  </preconfiguredChoices>
</processingMCP>
```
Or thay can cover every workflow action, as we will see in an example later in the workshop.

In this XML format, "appliesTo" is the name of the job presented in the dashboard, and "goToChain" is the desired selection. The file can be created by hand, or you can use one generated by Archivematica. To generate one:

1. In the Dashboard under Administration/Processing configuration, configure the default processing configuration options.
1. The resulting configuration is stored in /var/archivematica/sharedDirectory/sharedMicroServiceTasksConfigs/processingMCPConfigs/defaultProcessingMCP.xml
1. Add this file to transfer packages with the filename processingMCP.xml, in the root of the package, as in this example:

```
/your_transfer_directory
   processingMCP.xml
   /objects
   /metadata
```

Any workflow decisions encoded in processingMCP.xml will be used by the relevant microservices.

### Approve transfers via the REST API
The first two methods of automating workflow decisions do not include the Start transfer and Approve transfer tasks. Archivematica provides a REST API for these. "REST API" here means a special URL that can be accessed to perform the tasks, in most cases by scripts or other programs. This method can be used in conjunction with either a preconfigured default workflow or an in-transfer processing configuration file.

The Archivematica wiki provides documentation for the [Approve Transfer REST API](https://www.archivematica.org/wiki/Administrator_manual_1.0#Approving_a_transfer), but an example using the Linux ```curl``` command-line web agent is:

```
curl --data "username=mark&api_key=d3da515f8f0186b041afcf094cad4a885033d71d&type=zipped%20bag&directory=thesis_archivematica_bag_2175.tgz" http://127.0.0.1/api/transfer/approve
```
The message Archivematica returns if the REST call is successful is:

```
{"message": "Approval successful."}
```
Later we will see an example of a complete script that uses this method of approving a transfer.

### Use the SWORD API
Starting in Archivematica 1.1, a SWORD API will be available that will external applications to create, populate, and approve a transfer. More information [is available](https://www.archivematica.org/wiki/Sword_API) on the Archivematica wiki. The first application to use this API is the [Islandora](http://islandora.ca/) repository platform.

### Example workflow integration: Automating the preservation of Electonic Theses and Dissertations (ETDs)

The following example of integrating some of the automation options described above is based on [Simon Fraser University's automation](http://summit.sfu.ca/item/13191) of its ingestion of ETDs (Electronic Theses and Dissertations) into Archivematica. As summarized by the following diagram, ETDs managed by an application completely separate from Archivematica called the Thesis Registration System are dumped out into a queue, where several specialized scripts (called microservices but independent from Archivematica's microservices as described above) prepare transfers and package them in Bags. Each transfer contains a copy of the same processingMCP.xml file to automate workflow within an Archivematica pipeline. The transfers are approved in Archivematica via its REST API.

In this example, moving the ETDs from the Thesis Registration System into Archivematica, and then through Archivematica's transfer-to-AIP workflow, is completely automated. At no point during the process does a staff member perform any manual processing:

![Simon Fraser University Library's ETD preservation](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/Automating_ETD_preservation.png)

Each transfer Bag created by the Dumper microservice contains the the thesis PDF (and if applicable any associated data files such as spreadsheets, video, etc.), a Dublin Core XML file containing metadata describing the thesis, an [ETD-MS](http://www.ndltd.org/standards/metadata/) metadata file, a METS file defining the relationship between the thesis PDF and any supplemental data files, and all licenses and permission documentation associated with the thesis (what Archivematica refers to as submission documentation). The transfer, including the files generated as part of the Bag, is structured like this:

```
thesis_archivematica_bag_2120
|-- bag-info.txt
|-- bagit.txt
|-- data
|   |-- metadata
|   |   |-- dublincore.xml
|   |   |-- mets_structmap.xml
|   |   `-- submissionDocumentation
|   |       `-- etd8320-pclsig0.pdf
|   |-- objects
|   |   |-- etd8320.pdf
|   |   `-- etd8320_etdms.xml
|   `-- processingMCP.xml
|-- fetch.txt
|-- manifest-sha1.txt
`-- tagmanifest-sha1.txt
```

The processingMCP.xml file used to automate the workflow within Archivematica looks like this:


```xml
<processingMCP>
  <preconfiguredChoices>
    <preconfiguredChoice>
      <appliesTo>Workflow decision - send transfer to quarantine</appliesTo>
      <goToChain>Skip quarantine</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Approve normalization</appliesTo>
      <goToChain>Approve</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Store AIP</appliesTo>
      <goToChain>Store AIP</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Create SIP(s)</appliesTo>
      <goToChain>Create single SIP and continue processing</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Normalize</appliesTo>
      <goToChain>Normalize for preservation</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Select file format identification command</appliesTo>
      <goToChain>Fido version 1 PUID runs Identify using Fido</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Select pre-normalize file format identification command</appliesTo>
      <goToChain>Use existing data</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Select compression algorithm</appliesTo>
      <goToChain>7z using bzip2</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Select compression level</appliesTo>
      <goToChain>5 - normal compression mode</goToChain>
    </preconfiguredChoice>
    <preconfiguredChoice>
      <appliesTo>Store AIP location</appliesTo>
      <goToChain>/api/v1/location/8b4c131d-828a-480b-8464-decaa8efacd0/</goToChain>
    </preconfiguredChoice>
  </preconfiguredChoices>
</processingMCP>
```
The following diagram shows that a pipeline set up for another purpose (in this example, the pipeline used in the Digitization Center) processes the ETDs:

![ETD automation integrated with pipeline](https://dl.dropboxusercontent.com/u/1015702/linked_to/intro_to_archivematica/SFU%20ETD%20to%20Archivematica%20pipelines%20and%20storage.png)

The value of the "Store AIP location" choice in the processingMCP.xml file is worth noting. "8b4c131d-828a-480b-8464-decaa8efacd0" is the UUID identifying a Storage Service location configured especially for storing the thesis AIPs. This location is completely different from the default AIP storage location configured for the Digitization Center's pipleline.

The "Approver" microservice depicted in the previous diagram is the following shell script, which runs on the Thesis Registration System's server:

```bash
#!/bin/sh

# Usage: ./approve_transfer.sh zippedBag
# where "zippedBag" is the name of the Bag containing the transfer.

host='archivematica.example.org'
transfer_directory='/var/archivematica/sharedDirectory/watchedDirectories/activeTransfers/standardTransfer'
dashborad_user='myuser'
rsync_user='remoteu'
rsync_dest=$host:$transfer_directory
key='74e506e5a206c99a95c8c514300fd27fec939f69'
type='zipped%20bag'
bag=$1
 
# Copy the transfer Bag from the source directory to the transfer directory on the Archivematica
# host.
rsync -rv $bag $rsync_user@$transfer_directory
 
# If the copy was sucessful, use curl to issue a request to the REST API
# to approve the transfer.
if [ $? -eq 0 ]; then
  echo "OK, files in $source_directory copied to $transfer_directory, firing API call."
  message=(curl --data "username=$dashborad_user&api_key=$key&type=$type&directory=$bag" http://$host/api/transfer/approve)
  # If curl encountered an error, say so and stop.
  if [ $? -ne 0 ]; then
    echo "ERROR: curl could not complete the Approve Transfer API call."
    exit(1)
  fi
else
  # If copying the files encountered an error, say so and stop.
  echo "ERROR: Problem copying files, exiting."
  exit(1)
fi
 
# If we get this far, say so.
echo "OK, transfer successful: $message"
```

This script could be further refined by having it query [Archivematica's Elasticsearch index](https://www.archivematica.org/wiki/Elasticsearch_Development) to determine if an AIP for an ETD already exists.

To summarize this extended example, it is possible to completely automate the preservation of content using a combination of specially prepared transfers, a standardized processingMCP.xml file, the REST transfer approval API, and a number of custom scripts to integrate these components of Archivematica into a larger external workflow. 

<a name="preservation_planning" />
## Preservation Planning

Preservation planning at the institutional level is accomplished using digital preservation policy of some type. This section identifies three of these types and provides an institution context for implementing a preservation platform such as Archivematica.

### Policy frameworks

An increasingly common method for expressing an institution's approach to digital preservation is:

1. to define a preservation policy framework
2. within the framework, to define a set of specific policies, and
3. within each policy, or shared across a set of related policies, to define strategies for achieving the goals defined in the policies.

A policy framework defines high-level concepts such as the context for digital preservation in an institution, the principles by which it makes decisions about what to preserve, the stakeholders within the community it serves, and its commitment to digital preservation. Digital preservation frameworks reference many of the high-level points used to evaluate [Trustworthy Digital Repositories](http://www.crl.edu/archiving-preservation/digital-archives/metrics-assessing-and-certifying-0). One guide to developing digital preservation frameworks is available from the [Canadian Heritage Information Network](http://www.pro.rcip-chin.gc.ca/carrefour-du-savoir-knowledge-exchange/digital_preservation_policy_guidelines-ligne_directrice_strategique_preservation_numerique-eng.jsp).

A representative example of a digital preservation policy framework is the [University of Minnesota's](https://www.lib.umn.edu/digital/preservation). This document illustrates the relationship betwen the framework and specific policies mentioned above (although it does not provide access to the policies, only placeholders). However, we can infer the level of detail that many of these linked policies provide from their titles: "Roles and Responsibilities for Digital Preservation", "Collection Policies", "A List of Material-Specific Guidelines and Procedures", and "Disaster Planning for Digital Assets". Strategies are operational-level descriptions of resources and processes used to achieve the goals defined in the policies.

### Strategic plans

Another approach to define an institutional context for digital preservation activities is through a strategic plan. [York (Toronto, Canada) University Libraries' plan](http://digital.library.yorku.ca/documentation/digital-preservation-strategic-plan) illustrates how this type of public statement differs from a framework and associated policies and strategies: strategic plans tend to be more operationally oriented than frameworks, while still providing a succinct statement of the scope and objectives of the preservation activity. They are also less structured than digital preservation frameworks.

### Collection-specific plans

Some organizations do not provide a comprehensive framework or strategic plan, but instead provide policies and practices on specific collections or types of content. For example, Boston University Libraries provides a [digitial preservation policy](http://www.bu.edu/dioa/openbu/boston-university-libraries-digital-preservation-policy/) for content in its institutional repository.

### Functional requirements planning

The three approaches to institution-level digital preservation plans can be supplemented with an analysis of whether a preservation system meets a set of functional requirements. Jenny Mitcham from the University of York (UK) [has blogged](http://digital-archiving.blogspot.co.uk/2014/04/how-does-archivematica-meet-my.html) about how Archivematica meets her archives' minimum functional requirements.

### Archivematica in the context of institutional policies

As stated at the beginning of this workshop, Archivematica provides a toolset for preserving digital content, but it cannot provide a framework, strategic plan, or even a set of policies within which to perform that preservation. As the [overview](https://www.archivematica.org/wiki/Overview) on the wiki states, "Archivematica maintains the original format of all ingested files to support migration and emulation strategies. However, the primary preservation strategy is to normalize files to preservation and access formats upon ingest."

What Archivematica does provide is a set of policies that apply to specific file formats in its Format Policy Registry. "Policy" here is used differently than when it describes a public document. Given the types of public statements defined above, it would be accurate to say that Archivematica provides a standard set of actions that can be applied to specific files formats.

As metioned earlier, the advantages that cultural institutions gain from implementing Archivematica's format policies are:

1. the policies are accepted as best practice by the general digital preservation community
2. organizations do not need to spend time and resources researching the best way to identify or normalize specific file types, and
3. the format policies can be applied at scale reliably using Archivematica's toolset; in other words, each policy has been tested to determine how well it performs on large files or on many files within a single microservice.

In summary, if a library, archives, or museum has a digital preservation plan of some sort, they are in a position to use Archivematica to operationalization that plan effectively using standardized digital preservation techniques.

<a name="longterm_manangement" />
## Long-Term Managmement of Preserved Content

Archival Information Packages encapsulate content for storage over long periods of time. However, institutions will eventually need to change their AIPs; for example, the normalized formats generated when the AIP was created will likely at some time in the future be replaced by new formats and the content in AIPs will need to be migrated to the these formats. Another reason the AIP may change is that metadata contained in the package may change for a variety of reasons.

Between planned changes to an AIP, they should _not_ change unintentionally. A standard practice in digital preservation is to monitor files' bit-level integrity over time by periodically generating checksums for files and comparing the results to a previously recorded checksum. These checks should be performed for files that stay in a single storage location over extended periods of time as well as when files are copies between storage locations or platforms.

Additionally, replicating AIPs in geographically distributed locations is very important. Generally, three identical copies is considered a minimum, and some systems such as [LOCKSS](http://www.lockss.org/) use a minimum of seven copies to ensure that damange or loss to any single copy does not jeopardize reliably access to the content.

### Archivematica and AIP file manangement

While Archivematica provides the tools necessary to create robust AIPs, it does not provide all of the tools required to manage content over time as described here. For example, the Archivematica Storage Service only writes AIPs to one location and does not manage replicating the AIP. Also, Archivematica currently does not perform periodic bit-level integrity checks on AIPs. Work is underway to add this feature but until that development is complete, implementers must use a separate tool such as [ACE](https://wiki.umiacs.umd.edu/adapt/index.php/Ace). Archivematica does provide a tool for moving AIPs between Storage Service locations.

### Archivematica and AIP migration

The content of AIPs created by Archivematica is, on the other hand, very well suited for long-term preservation:

1. Archivematica's AIPs contain both the files that were part of the original transfer and normalized versions of those files. This strategy allows for two long-term digital preservation strategies, [format migration](http://en.wikipedia.org/wiki/Digital_preservation#Migration) and [emulation](http://en.wikipedia.org/wiki/Digital_preservation#Emulation).
2. Work is currently underway to provide a means to support [AIP reingesting and versioning](https://www.archivematica.org/wiki/AIP_re-ingest). These two features will allow either minor updates to AIPs (such as adding a file that was missing from the original SIP) or large-scale modifications, such as periodic format migration of the normalized files. This functionality is expected to be included in Archivematica 1.4.
3. Archivematica's use of BagIt, PREMIS, METS, and other widely accepted standards will allow for the migration of AIPs to new preservation platforms in the future.

#### Exercise 5: Think about implementing Archivematica within an organization

1. Choose a digital preservation policy from [SCAPE's list of published policies](http://wiki.opf-labs.org/display/SP/Published+Preservation+Policies), or use any other institutional-level digital preservation policy you can find.
2. Find the sections that mention object- or file-level processes or workflows, or long-term management of preserved files.
3. Write a brief statement about how the organization that published the policy could implement Archivematica in its object management strategy.

<a name="about_this_workshop" />
## About This Workshop

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/4.0/80x15.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Introduction to Archivematica</span> by <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Mark Jordan</span> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

Comments in the Github issue queue or pull requests are welcome.
