# PanelApp_API
## Overview
This script accesses the panelapp API and returns two files containing a list of gene symbols and ensembl ids for each gene panel.

**However**
This script was part of a project to import all the panelapp panels into our LIMS system. 

There are a few issues which has lead to the script looking as it does!

1. I couldn't run this script on the trust network, so rather than inserting direct into the database I've had to write the results of the API to a text file and then read this text file in a second script (not included here)

2. PanelApp includes the LRG identifiers in the ensemblids field!? This is new, and luckily the script which deals with the import to database ignores them.

3. GeL currently use a old snapshot of ensembl/HGNC so some gene symbols are out of date, and some gene records contain ensembl ids for different genes. There is also one gene symbol which includes an asterix.

4. There can be multiple ensemblids for each gene.

4. Red genes are not included in the output files as these genes haven't been assessed.

5. FYI panels with a version <1.0 are not being used by GeL diagnostically (not fully approved).

I can share the script which performs the import seperately. (slack or aledjones[at]nhs.net)

I can also share which genes we've had to manually curate/translate between our HGNC snaphot and GeL's


## How it works

###1. Return all gene panels

This script accesses the Panel App API and retrieves all the current live panels.
For each panel the immutable id is recorded, along with the human readable panel name and the version number.

###2. For each panel get a list of green and red genes
Then for each panel, the immutable id is used to access the api record for that panel.
For each panel a list of green and amber genes are curated and the gene symbol and ensembl ids are stored.
The level of evidence field is used to identify if these genes are in the green, amber or red lists.

###3. Return two files
The script then writes two files. one listing the gene symbols for each panel and a second listing the ensembl ids. For each list of coloured genes the output file is in the form:

Panelhash_panelname_version_colour:['list','of','gene','symbols'] eg.

553f968cbb5a1616e5ed45cc_Classical tuberous sclerosis_1.0_Green_symbols:['TSC1', 'TSC2']

and for ensemblids:

Panelhash_panelname_version_colour:['list','of','ensemblids'] eg.

553f968cbb5a1616e5ed45cc_Classical tuberous sclerosis_1.0_Green:["'ENSG00000165699','LRG_486'", "'ENSG00000103197','LRG_487'"]

