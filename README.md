(OHDSI Community Face-to-Face Study-a-thon 2018) Predicting randomized clinical trial results with real-world evidence: A case study in the comparative safety of tofacitinib, adalimumab and etanercept in patients with rheumatoid arthritis
=============

<img src="https://img.shields.io/badge/Study%20Status-Design%20Finalized-brightgreen.svg" alt="Study Status: Design Finalized">

- Analytics use case(s): **Population-Level Estimation**
- Study type: **Clinical Application**
- Tags: **F2F, Rheumatoid Arthritis, drug safety, tofacitinib, etanercept, adalimumab, Xeljanz, Enbrel, Humira**
- Study lead: **Bridget Wang**, **Kristin Kostka**
- Study lead forums tag: **[BridgetWang](https://forums.ohdsi.org/u/BridgetWang)**, **[krfeeney](https://forums.ohdsi.org/u/krfeeney)**
- Study start date: **May 2, 2018**
- Study end date: **June 30, 2020**
- Protocol: **[Final Protocol](https://github.com/ohdsi-studies/TofacitinibSafetyinRA/blob/master/Overall%20Documentation/OHDSI%20F2F%20protocol%20v2.2.docx)** 
- Publications: **TBD**
- Results explorer: **In progress**

**About This Study:**
On May 2-3, 2018, a group of OHDSI community investigators converged on Columbia University to design and execute a Population-Level Estimation study in two days. The result was a diverse group of experts ranging from clinicians, biostatisticians, epidemilogists, data analysts, software engineers and other observational health data enthusiasts. A presentation on the innovative community-led process to rapidly iterate on a study question is explored more in this [2018 OHDSI US Symposium Lighting Talk](https://www.youtube.com/watch?v=ybKFFAP5Gl0) and [Slides](https://www.ohdsi.org/wp-content/uploads/2018/10/OHDSI-LightningTalk2018-FeeneyKostka.pdf). The community-driven process for choosing a question is detailed in the [OHDSI Forums](https://forums.ohdsi.org/t/ohdsi-face-to-face-at-columbia-may2-3-community-study-a-thon/4008). In just two days, the OHDSI community generated a fully specified protocol and executed a study package on multiple data sets.

This repository serves to document the iterative improvements made to initial study designed as well as the publication and dissemination of results for the broader scientific community. 

**Description:**
This study aims to compare the safety of tofacitinib with adalimumab and etanercept in patients with rheumatoid arthritis (RA). We will replicate the design and population inclusion criteria of an ongoing phase 3b/4 randomized clinical trial (NCT02092467), with the aim of predicting the RCT results using real-world evidence. In this study, we will analyze data from observational databases across the OHDSI network using the OHDSI CohortMethod package framework to perform this comparative study. There are two study packages (RCT recreation and RWD analysis). While it is desired for each site to run both packages, there are known restrictions around cohort definitions that may make it prohibitive to contribute results to both analyses.

## Package Requirements
- A database in [Common Data Model version 5](https://github.com/OHDSI/CommonDataModel) in one of these platforms: SQL Server, Oracle, PostgreSQL, IBM Netezza, Apache Impala, Amazon RedShift, or Microsoft APS.
- R version 3.5.0 or newer
- On Windows: [RTools](http://cran.r-project.org/bin/windows/Rtools/)
- [Java](http://java.com)
- Suggested: 25 GB of free disk space

See [this video](https://youtu.be/K9_0s2Rchbo) for instructions on how to set up the R environment on Windows.

How to run
==========

1. In `R`, use the following code to install the dependencies:

	```r
	install.packages("devtools")
	library(devtools)
	install_github("ohdsi/ParallelLogger", ref = "v1.1.1")
	install_github("ohdsi/SqlRender", ref = "v1.6.3")
	install_github("ohdsi/DatabaseConnector", ref = "v2.4.1")
	install_github("ohdsi/OhdsiSharing", ref = "v0.1.3")
	install_github("ohdsi/FeatureExtraction", ref = "v2.2.5")
	install_github("ohdsi/CohortMethod", ref = "v2.4.4"
	install_github("ohdsi/EmpiricalCalibration", ref = "v2.0.0")
	install_github("ohdsi/MethodEvaluation", ref = "v1.1.0")
	```

	If you experience problems on Windows where rJava can't find Java, one solution may be to add `args = "--no-multiarch"` to each `install_github` call, for example:
	
	```r
	install_github("ohdsi/SqlRender",  ref = "v1.6.3", args = "--no-multiarch")
	```
	
	Alternatively, ensure that you have installed only the 64-bit versions of R and Java, as described in [the Book of OHDSI](https://ohdsi.github.io/TheBookOfOhdsi/OhdsiAnalyticsTools.html#installR)
  
  	If you experience problems where `R` says a library is logged, one solution is to add `INSTALL_opts = '--no-lock'` to each `install_github` call, for example:
	
	```r
	install_github("ohdsi/DatabaseConnector", ref = "v2.4.1", INSTALL_opts = '--no-lock')
	```
	
	Note: `library(ParallelLogger)` in v1.1.1 is not compatible with all versions of `library(ggplot)`. You will need to adjust your versioning to a compatible version:
	
	```r
	library(devtools)
	install_version("ggplot2", version = "3.2.0", repos = "http://cran.us.r-project.org",  INSTALL_opts = '--no-lock')
	```

Without this fix, you will get errors when `runDiagnostics = TRUE` related to [NAs in the data frame (e.g. "possible only for atomic and list types")](https://github.com/ohdsi-studies/TofacitinibSafetyinRA/issues/4).

2. In `R`, use the following `devtools` command to install the TofacitinibSafetyinRA package:

	```r
	install.packages("devtools")
	devtools::install_github("ohdsi-studies/TofacitinibSafetyinRA")
	```

*Note: When using this installation method it can be difficult to 'retrace' because you will not see the same folders that you see in the GitHub Repo. If you would prefer to have more visibility into the study contents, you may alternatively download the [TAR file](https://github.com/ohdsi-studies/TofacitinibSafetyinRA/archive/master.zip) for this repo and bring this into your `R`/`RStudio` environment. An example of how to call ZIP files into your `R` environment can be found in the [The Book of OHDSI](https://ohdsi.github.io/TheBookOfOhdsi/PopulationLevelEstimation.html#running-the-study-package).*
  
3. Once installed, you can execute the study by modifying and using the code below. For your convenience, this code is also provided in the GitHub under `extras/CodeToRun.R`:

	```r
	library(TofacitinibSafetyinRA)
	
	# Optional: specify where the temporary files (used by the ff package) will be created:
	options(fftempdir = "c:/FFtemp")
	
	# Maximum number of cores to be used:
	maxCores <- parallel::detectCores()
	
	# Minimum cell count when exporting data:
	minCellCount <- 5
	
	# The folder where the study intermediate and result files will be written:
	outputFolder <- "c:/TofacitinibSafetyinRA"
	
	# Details for connecting to the server:
	# See ?DatabaseConnector::createConnectionDetails for help
	connectionDetails <- DatabaseConnector::createConnectionDetails(dbms = "postgresql",
									server = "some.server.com/ohdsi",
									user = "joe",
									password = "secret")
	
	# The name of the database schema where the CDM data can be found:
	cdmDatabaseSchema <- "cdm_synpuf"
	
	# The name of the database schema and table where the study-specific cohorts will be instantiated:
	cohortDatabaseSchema <- "scratch.dbo"
	cohortTable <- "my_study_cohorts"
	
	# Some meta-information that will be used by the export function:
	databaseId <- "Synpuf"
	databaseName <- "Medicare Claims Synthetic Public Use Files (SynPUFs)"
	databaseDescription <- "Medicare Claims Synthetic Public Use Files (SynPUFs) were created to allow interested parties to gain familiarity using Medicare claims data while protecting beneficiary privacy. These files are intended to promote development of software and applications that utilize files in this format, train researchers on the use and complexities of Centers for Medicare and Medicaid Services (CMS) claims, and support safe data mining innovations. The SynPUFs were created by combining randomized information from multiple unique beneficiaries and changing variable values. This randomization and combining of beneficiary information ensures privacy of health information."
	
	# For Oracle: define a schema that can be used to emulate temp tables:
	oracleTempSchema <- NULL
	
	execute(connectionDetails = connectionDetails,
            cdmDatabaseSchema = cdmDatabaseSchema,
            cohortDatabaseSchema = cohortDatabaseSchema,
            cohortTable = cohortTable,
            oracleTempSchema = oracleTempSchema,
            outputFolder = outputFolder,
            databaseId = databaseId,
            databaseName = databaseName,
            databaseDescription = databaseDescription,
            createCohorts = TRUE,
            synthesizePositiveControls = TRUE,
            runAnalyses = TRUE,
            runDiagnostics = TRUE,
            packageResults = TRUE,
            maxCores = maxCores)
	```
*Note: If you experience an issue while running the code, please report this via the ```Issues``` tab above. Where possible, please include a snippet of the error message and any relevant information about your R environment / data layer (e.g. SQL Server, RedShift, Google BigQuery). Issues will be debugged and code will be updated accordingly.*

4. Upload the file ```export/Results<DatabaseId>.zip``` in the output folder to the study coordinator:

	```r
	submitResults("export/Results<DatabaseId>.zip", key = "<key>", secret = "<secret>")
	```
	
	Where ```key``` and ```secret``` are the credentials provided to you personally by the study coordinator.
		
5. To view the results, use the Shiny app:

	```r
	prepareForEvidenceExplorer("Result<databaseId>.zip", "/shinyData")
	launchEvidenceExplorer("/shinyData", blind = TRUE)
	```
  
  Note that you can save plots from within the Shiny app. It is possible to view results from more than one database by applying `prepareForEvidenceExplorer` to the Results file from each database, and using the same data folder. Set `blind = FALSE` if you wish to be unblinded to the final results.



License
=======

The [Predicting randomized clinical trial results with real-world evidence: A case study in the comparative safety of tofacitinib, adalimumab and etanercept in patients with rheumatoid arthritis] package is licensed under Apache License 2.0
