Meeting in Japan (07/2019)
==========================

Add to all objects the source of the object
--------------------------
PRIDE 
MassIVE


Entry points
--------------------------

Convention:


- Compact objects: Using only mandatory field (smaller)
- Full objects: Including all fields.

Not all entry points will be able to be implemented by everyone.
Entry points that are not implemented, need to be documented properly. Find the right way to do it.

Implementation in ProteomeCentral:

Union of the results of all resources. We need to know where it is originating resource.


dataset
-------------------------------

```
/datasets/{id}
/datasets?<filterTerms>
```
Every query must return a list, even if of size zero or one

```
/datasets?species=homo sapiens&pageSize=50&pageNumber=1&resultType=compact
```

Filter options: 

```
accession=[PXD0000001, PXD000002]
species=
instrument=
contact=
publication=
modification=
keyword= (only from the Dataset Keywords field)
search=liver (i.e., free text search term)
proteinAccession = # (Protein will return a dataset where this protein has been identified.) 
peptideSequence  = # (Peptide will return a dataset where this peptide has been identified.)

```

Multiple values for attribute are query like: 

```
/datasets?species=[human,mouse]
```

spectra
-------------------------------

The spectrum object will have two mandatory fields that will be provided in the compact version of the object: 

- usi (The unified spectrum identifier)
- status (An status would define if the current spectrum identifier can be read/provide or is unavailable)
  [READABLE, PEAKS UNAVAILABLE]
  
Entry points for spectra: 

```
/spectra?<filterTerms>
```

The first and most important filter will be a `usi`: 

```python
/spectra?usi={}
```

Result is the requested spectrum annotated with the user-supplied peptide string. A resource may return an empty result if unable to generate the result object. 

> In particular, if the resource can find the spectrum but `not annotate the spectrum` with the peptide string then the result should be an error code.


> Every query must return a list, even if of size zero or one


***Question Mark: What happens with queries that are very large?***

Proposal: If unable to resolve an ambiguous USI, a resource may return an error, as well as the set of (“precise”) USIs that result from the query:

```
/spectra?accession=PXD000&msRun=XXX&scan=2019&resultType=compact
````

Posible independent filters: 

```
PX accession 
msRun or fileName
Scan 
```

The only way the USI is a query is for msRun.

Always a list of spectrum objects is always returned. Every resource is responsible of the resolution of the list. 
Use “compact” and “full” objects.
Differentiate “I could not open” (raw files) from “empty”.


PSMs
--------

Actual observations in the resource, not interpretations/hypotheses.

```
/psms?<filterTerms>
```

The first and more important query is fine a PSM by using the usi of the spectra including the Peptide Sequence and charge. 

```
/psms?usi={} 
```

You can query the psms by using also independent properties such as peptideSequence or charge state: 

```
/psms?peptideSequence=VLHPLEGAVVIIFK&charge=2
```

Filters available for each PSM: 

```
passThreshold=true (“true” is the default value, can also be set to passThreshold=all)
PX accession 
MsRun 
Scan or index 
peptideSequence 
proteinAccession 
Charge 
Modification (use instead of PTM)
Will also need to specify massTolerance= +/- delta_mass_tolerance
peptidoForm
```

The PSMS Object will look like:  

```
peptideSequence 
USI (https://docs.google.com/document/d/1LLQwmttrXku2JBgWZvheXDKVbCb1AcGPVXd8FSSby00/edit#)
Modifications as represented by mzTab (Yasset)
Example: 9-UNIMOD:35,3-UNIMOD:7,15-UNIMOD:7
searchEngineScore: [
              OntologyTerms
         ]
retentionTime
charge
proteinAccessions: [
   [ProteinId, Start, End}
]
```

***Note: We have decided to enable queries by actual peptide sequence only. The only way to retrieve peptidoforms is by using mass tolerances.***


peptides
--------------------------

The `peptide` entrypoint is a summary of all the PSMs valid (passThreshold) across all the PX datasets. 

```
/peptide?<filterTerm>
```

Query filters:

```
passThreshold=true (“true” is the default value, can also be set to passThreshold=all)
PX accession
peptideSequence
proteinAccession
Modification (use instead of PTM)
Will also need to specify massTolerance= +/- delta_mass_tolerance
peptidoForm
```

One Peptide Object: 

```
peptideSequence
countPSM
Modifications
peptidoformSequence
countDatasets
countPeptidoforms
proteinAccessions: [
   [ProteinId, Start, End}
]
attributes: [     (Yasset Define name of this - the goal is to represent repository-scale aggregate FDR controls)
] 
```

proteins
---------------------------

The `protein` entrypoint is a summary of all protein identifications (proteinevidences) in the resource. 

/proteins?<filterTerm>

Query filters:

```
passThreshold=true (“true” is the default value, can also be set to passThreshold=all)
PX accession 
peptideSequence 
Modification (use instead of PTM)
Will also need to specify massTolerance= +/- delta_mass_tolerance
peptidoForm
Gene accession
ProteinAccession=[Acc1, Acc2, Acc3...]
Isoforms need to be explicitly listed as separate accessions
```

One Protein Object: 

```
countPeptides
countPSMs
Modifications
countDatasets
countPeptidoforms
attributes: [     (Yasset Define name of this - the goal is to represent repository-scale aggregate FDR controls)
] 

````



Aggregated level: 

Define the scores for every database: ProteinProphet , MassiVEKB Score. 


-----



ERROR CODE needs to be defined: 



