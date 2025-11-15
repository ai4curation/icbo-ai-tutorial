# VBO Ontology Project Guide

This includes instructions for editing the vertebrate breed ontology. 

## Project Layout
- Main development file is `src/ontology/vbo-edit.obo`
- individual terms checked out in `terms`

## Querying ontology

- To look at a specific term if you know the ID:
    - `obo-grep.pl --noheader -r 'id: VBO:0004177' src/ontology/vbo-edit.obo`
- All mentions of an ID
    - `obo-grep.pl --noheader -r 'VBO:0004177' src/ontology/vbo-edit.obo`
- Regexes; all mentions of hand or foot:
    - `obo-grep.pl --noheader -r '(hand|foot)' src/ontology/vbo-edit.obo`
- Note that `obo-grep.pl` is in your PATH, no need to search for it    
- Only search over `src/ontology/vbo-edit.obo`
- DO NOT bother doing your own greps over the file, or looking for other files, unless otherwise asked, you will just waste time.
- ONLY use the methods above for searching the ontology

## Before making edits
- Read the request carefully and make a plan, especially if there is nuance
- if a PMID is mentioned in the issue, ALWAYS try and read it
- Use this command to fetch full text: `aurelian fulltext PMID:NNNNNN` (using the actual ID)
- This also works for other URLs for scientific papers and for DOIs, if there are no access restrictions
- ALWAYS check proposed parent terms for consistency

## Edits
- IMPORTANT: Do not edit the edit file directly, it's large
- Add edits should be made in the `terms/` folder
- check out a term into `terms/`: `obo-checkout.pl src/ontology/vbo-edit.obo VBO:1234567 [OTHER IDS]`
- This will create a single stanza obo files `terms/npso_1234567.obo` which you can easily edit
     - (note the colon is replaced with an underscore)
- You can go ahead and edit the smallers files in the `terms/` folder
- After edits, check back in: `obo-checkin.pl src/ontology/vbo-edit.obo VBO:1234567 [OTHER IDS]`
- if you like you can edit multiple terms in one batch, e.g. `terms/my_batch.obo`
     - `obo-checkout.pl src/ontology/vbo-edit.obo terms/my_batch.obo`
- checking in will update the edit file and remove the file from `terms/`
- Commits are then made on src/ontology/vbo-edit.obo as appropriate
- Note that `obo-checkin.pl` and `obo-checkin.pl` are in your PATH, no need to search for it    


## OBO Format Guidelines
- Term ID format: VBO:NNNNNNN (7-digit number)
- Handling New Term Requests (NTRs):
  - New terms start  VBO:777xxxx
  - Do do `grep id: VBO:777 src/ontology/vbo-edit.obo` to check for clashes
- Each term requires: id, name, namespace, definition with references
- NEVER guess VBO IDs, use search tools above to determine actual term
- NEVER guess PMIDs for references, do a web search if needed
- Use standard relationship types: is_a, part_of, has_part, etc.
- Follow existing term patterns for consistency

## Publications
- Run the command `aurelian fulltext <PMID:nnn>` to fetch full text for a publication. A doi or URL can also be used
- You should cite publications appropriately, e.g. `def: "...." [PMID:nnnn, doi:mmmm]

## Syntax Checking

- To validate syntax: `robot convert --catalog src/ontology/catalog-v001.xml -i src/ontology/vbo-edit.obo -f obo -o vbo-edit.TMP.obo`
- Use `-vvv` for a full stack trace if there are errors.

## Vbo Guidelines

## Mappings

Mappings are an integral part of Vbo. You should never create mappings unless requested by a user, if they do, make sure metadata is correct

Vbo has an idiosyncratic way of adding metadata to mappings. Indicating the kind of mapping is important, this is dome by overloading
the source qualifier and using an ID like VBO:equivalentTo. Also the original source can be included:

```
[Term]
id: VBO:0800447
name: bleeding disorder, platelet-type, 13, susceptibility to
subset: predisposition
synonym: "BDPLT13" EXACT ABBREVIATION [VBO:Lexical, OMIM:614009]
synonym: "bleeding disorder, platelet-type, 13, susceptibility to" EXACT [VBO:Lexical, OMIM:614009]
synonym: "bleeding disorder, susceptibility to, due to defective platelet thromboxane A2 receptor" EXACT [OMIM:614009]
synonym: "susceptibility to platelet-type bleeding disorder 13" EXACT []
xref: MEDGEN:481244 {source="VBO:equivalentTo", source="VBO:MEDGEN"}
xref: OMIM:614009 {source="Orphanet:220443", source="VBO:equivalentTo", source="Orphanet:220443/e"}
xref: UMLS:C3279614 {source="VBO:equivalentTo", source="VBO:MEDGEN", source="MEDGEN:481244"}
is_a: VBO:0020573 {source="OMIM:614009"} ! inherited disease susceptibility
relationship: has_material_basis_in_germline_mutation_in http://identifiers.org/hgnc/11608 {source="VBO:mim2gene_medgen"} ! TBXA2R
relationship: predisposes_towards VBO:0800446 {source="OMIM:614009"} ! bleeding diathesis due to thromboxane synthesis deficiency
property_value: curated_content_resource "https://www.malacards.org/card/bleeding_disorder_platelet_type_13" xsd:anyURI {source="VBO:MalaCards"}
property_value: IAO:0000233 "https://github.com/monarch-initiative/vbo/issues/6419" xsd:string
```

## Obsoleting terms

obsolete terms should have no logical axioms (is_a, relationship,
intersection_of) on them. Obsolete terms may be replaced by a single
term (so-called obsoletion with exact replacement), or by zero to many `consider` tags.

```
[Term]
id: VBO:0100229
name: obsolete Heimler syndrome
def: "OBSOLETE. A peroxisoome biogenesis disorder characterized by sensorineural hearing loss, enamel hypoplasia of the secondary dentition, nail abnormalities and occasional or late-onset retinal pigmentation abnormalities, in which the cause of the disease is a mutation in peroxisomal biogenesis factor 1 (PEX1) or peroxisomal biogenesis factor 6 (PEX6) genes." [VBO:patterns/disease_series_by_gene, PMID:26387595]
subset: ordo_disorder {source="Orphanet:3220"}
subset: ordo_malformation_syndrome {source="Orphanet:3220"}
synonym: "bilateral sensorineural hearing loss, enamel hypoplasia and nail defects" RELATED [GARD:0001687]
synonym: "deafness enamel hypoplasia nail defects" RELATED [GARD:0001687]
synonym: "deafness-enamel hypoplasia-nail defects syndrome" EXACT [VBO:0009325]
synonym: "Heimler syndrome" EXACT []
synonym: "sensorineural hearing loss, enamel hypoplasia, and nail abnormalities" RELATED [GARD:0001687]
xref: MESH:C535994 {source="VBO:obsoleteEquivalent", source="Orphanet:3220/e", source="Orphanet:3220"}
xref: Orphanet:3220 {source="OMIM:234580", source="VBO:obsoleteEquivalent"}
xref: SCTID:721085000 {source="VBO:obsoleteEquivalent"}
property_value: http://purl.org/dc/terms/creator https://orcid.org/0000-0001-5208-3432
property_value: IAO:0000231 OMO:0001000
property_value: IAO:0000233 "https://github.com/monarch-initiative/vbo/issues/3222" xsd:anyURI
is_obsolete: true
consider: VBO:0100259
```

Synonyms and xrefs can be migrated judiciously,

We never do complete merges now, so there should be no `alt_ids` or
disappearing stanzas. If a user asks for a merge, they usually mean
obsoletion with direct replacement, as here:

Example:

```
[Term]
id: VBO:0100334
name: obsolete viral infectious disease or sequela
property_value: http://purl.org/dc/terms/creator https://orcid.org/0000-0001-5208-3432
property_value: IAO:0000231 VBO:TermsMerged
property_value: IAO:0000233 "https://github.com/monarch-initiative/vbo/pull/3118#issuecomment-871858054" xsd:anyURI
is_obsolete: true
replaced_by: VBO:0100321
```

No relationship should point to an obsolete term - when you obsolete a term, you may need to also rewire
terms to "skip" the obsoleted term.

## Other metadata

- Link back to the issue you are dealing with using the `term_tracker_item`
- All terms should have definitions, with at least one definition xref, ideally a PMID


You can sign NEW terms using with a tag-value like this: (don't tag pre-existing terms)

```
property_value: http://purl.org/dc/terms/creator doi:10.1186/s13326-024-00320-3
```

You don't need to add a date, this will be handled for you

## Relationships

All terms should have at least one `is_a` (this can be implicit by a logical definition, see below).

Some other terms may have relationships but these will generally follow existing patterns.

## Logical definitions

These should follow genus-differentia form, and the text definition should mirror the logical definition. Example:

```
[Term]
id: VBO:0000715
name: lymph node adenoid cystic carcinoma
def: "A adenoid cystic carcinoma that involves the lymph node." [VBO:patterns/location]
subset: gard_rare {source="VBO:GARD"}
subset: rare
synonym: "lymph node adenoid cystic cancer" RELATED []
synonym: "lymph node adenoid cystic carcinoma" EXACT [DOID:0060219, VBO:patterns/location]
xref: DOID:0060219 {source="VBO:equivalentTo"}
is_a: VBO:0001082 {source="DOID:0060219", source="VBO:Entailed", source="VBO:Redundant"} ! lymph node cancer
intersection_of: VBO:0004971 ! adenoid cystic carcinoma
intersection_of: disease_has_location UBERON:0000029 ! lymph node
property_value: curated_content_resource "https://www.malacards.org/card/lymph_node_adenoid_cystic_carcinoma" xsd:anyURI {source="VBO:MalaCards"}
```

The reasoner can find the most specific `is_a`, so it's OK to leave this off, unless there is provenance to be added.

## Design patterns

Check:

`src/patterns/dosdp-patterns/*yaml`

For the DOSDP design pattern which guides how the term should be created.

## 1. Gene-Related Disease Naming Conventions

The documentation should include explicit naming patterns for gene-related diseases:

```markdown
## Gene-Disease Naming Conventions

For monogenic diseases, VBO follows the pattern: `{GENE}-related {disease description}`

Examples:
- ALG8-related autosomal dominant polycystic kidney and/or liver disease
- MYCBP2-related developmental delay with corpus callosum defects
- TUBB4B-related ciliopathy

This pattern should be used even when users request alternative formats like "disease - GENE" format.
```

## 2. Synonym Attribution and Citation Requirements

The current documentation lacks specific guidance on synonym citations:

```markdown
## Synonym Citation Requirements

ALL synonyms must include proper citations:

**Correct examples:**
```
synonym: "EBV-associated lymphoproliferative disorder" EXACT [PMID:38882456]
synonym: "MDCD" EXACT ABBREVIATION [PMID:36200388]
synonym: "CHD4-related neurodevelopmental disorder" EXACT [https://orcid.org/0000-0001-9310-0163, Orphanet:653712]
```

**Never use empty brackets:**
```
synonym: "term name" EXACT []  # INCORRECT
```

### ClinGen Label Handling
When ClinGen provides preferred labels, use the "preferred label by community" qualifier:
```
synonym: "Hajdu-Cheney syndrome-NOTCH2" EXACT [] {OMO:0002001="https://w3id.org/information-resource-registry/clingen"}
```
```

## 3. Design Pattern Recognition and Application

```markdown
## Design Pattern Compliance

Before creating new terms, ALWAYS check for applicable design patterns:

1. Search `src/patterns/dosdp-patterns/*.yaml` for relevant patterns
2. Common patterns include:
   - `disease_series_by_gene.yaml` - for diseases caused by mutations in specific genes
   - `susceptibility_by_gene.yaml` - for genetic susceptibility factors
   - `childhood.yaml` - for childhood-onset conditions

### Gene-Based Disease Pattern Requirements

When following the disease_series_by_gene pattern:

**Required logical structure:**
```
is_a: VBO:0700092 ! neurodevelopmental disorder
intersection_of: VBO:0700092 ! neurodevelopmental disorder
intersection_of: has_material_basis_in_germline_mutation_in http://identifiers.org/hgnc/23386 ! MYCBP2
relationship: has_material_basis_in_germline_mutation_in http://identifiers.org/hgnc/23386 {source="PMID:36200388"} ! MYCBP2
```

**Definition template:**
```
def: "Any [parent disease class] in which the cause of the disease is a mutation in the [GENE] gene." [PMID:references]
```
```

## 4. Parent Term Selection Guidelines

```markdown
## Parent Term Selection Guidelines

### For Gene-Related Diseases
- Use the most general appropriate disease category that encompasses all known phenotypes
- For multi-system diseases, consider dual parentage rather than single broad syndromic parent
- Example: For neuro-urological conditions, use both `nervous system disorder` AND `urinary system disorder`

### Avoid Over-Specific Parents
- Don't default to "inherited disease" unless inheritance is the primary distinguishing feature
- Consider the full phenotypic spectrum, not just the most common presentations
```

## 5. Source Attribution in Relationships

```markdown
## Source Attribution Requirements

All logical axioms must include source attribution when based on literature:

```
is_a: VBO:0700092 {source="PMID:36200388", source="https://orcid.org/0000-0001-9310-0163"} ! neurodevelopmental disorder
relationship: has_material_basis_in_germline_mutation_in http://identifiers.org/hgnc/23386 {source="PMID:36200388"} ! MYCBP2
```

Multiple sources can be cited for the same relationship.
```

## 6. Gene Identifier Verification

```markdown
## Gene Identifiers

**CRITICAL**: Always verify gene identifiers for human diseases use the official HGNC database:
- Search at https://www.genenames.org/
- Use format: `http://identifiers.org/hgnc/[HGNC_ID]`
- NEVER guess gene identifiers

Example: For MYCBP2 gene, use `http://identifiers.org/hgnc/23386`, NOT `http://identifiers.org/hgnc/7557`

**CRITICAL**: Always verify gene identifiers for non-human animal diseases, use gene identifiers from NCBI gene:
- To search for a gene within a species, e.g. dog, use a search URL like https://www.ncbi.nlm.nih.gov/gene/?term=Canis+lupus+familiaris+MAP9
- Use format: `http://identifiers.org/ncbigene/[NCBI_GENE_ID]`
- Never guess at gene identifiers

Example: For MAP9 gene from Canis lupus familiaris (dog), use `http://identifiers.org/ncbigene/482662`.
```

## 7. Susceptibility vs Disease Relationships

```markdown
## Susceptibility Term Guidelines

Susceptibility terms should use `predisposes_towards` relationships, NOT `is_a` relationships:

**Correct:**
```
[Term]
id: VBO:1060111
name: SAMD9L-related spectrum and myeloid neoplasm risk
is_a: VBO:0020573 ! inherited disease susceptibility
relationship: predisposes_towards VBO:0008038 ! ataxia-pancytopenia syndrome
relationship: predisposes_towards VBO:0018881 ! myelodysplastic syndrome
```

**Incorrect:**
```
[Term]
id: VBO:0008038
name: ataxia-pancytopenia syndrome
is_a: VBO:1060111 ! SAMD9L-related spectrum and myeloid neoplasm risk
```
```

## 8. Term Obsolescence and Merging

```markdown
## Term Obsolescence Guidelines

### For Simple Obsolescence
```
[Term]
id: VBO:0100334
name: obsolete viral infectious disease or sequela
property_value: IAO:0000231 OMO:0001000 {source="VBO:excludePhenotype"}
property_value: IAO:0000233 "https://github.com/monarch-initiative/vbo/issues/XXXX" xsd:anyURI
is_obsolete: true
replaced_by: VBO:0100321
```

### For Term Merging
Use `VBO:TermsMerged` and migrate ALL relevant content to the surviving term:
- Synonyms with proper source attribution
- Cross-references with updated source qualifiers
- Relevant subsets

Remove ALL logical axioms and definitions from obsoleted terms.
```

## 9. Definition Enhancement Standards

```markdown
## Definition Guidelines

### Gene-Related Diseases
Use the pattern: "Any [parent disease] in which the cause of the disease is a mutation in the [GENE] gene."

### Clinical Descriptions  
- Use sentence case for medical terms
- Be specific but broadly applicable
- Include inheritance patterns when known
- Cite multiple sources when available: `[https://clinicalgenome.org/affiliation/40066/, PMID:29535429]`
```

## 10. Quality Control Exclusions

```markdown
## QC Check Exclusions

Some terms may legitimately violate QC checks and need exclusions:

```
relationship: excluded_from_qc_check http://purl.obolibrary.org/obo/vbo/sparql/qc/general/qc-single-child.sparql
```

Common cases include:
- Terms with initially single children pending additional curation
- Terms created for organizational purposes by domain experts
```

## 11. Complete Exemplar Stanzas

The documentation should include complete examples showing all required elements:

```obo
[Term]
id: VBO:1060117
name: MYCBP2-related developmental delay with corpus callosum defects
def: "Any neurodevelopmental disorder in which the cause of the disease is a mutation in the MYCBP2 gene. This condition is characterized by variable corpus callosum defects consistent with dysgenesis, and a broad spectrum of neurobehavioural deficits including developmental delay, intellectual disability, epilepsy, and autistic features." [https://orcid.org/0000-0001-9310-0163, PMID:36200388]
synonym: "MDCD" EXACT ABBREVIATION [PMID:36200388]
is_a: VBO:0700092 {source="PMID:36200388", source="https://orcid.org/0000-0001-9310-0163"} ! neurodevelopmental disorder
intersection_of: VBO:0700092 ! neurodevelopmental disorder
intersection_of: has_material_basis_in_germline_mutation_in http://identifiers.org/hgnc/23386
relationship: has_material_basis_in_germline_mutation_in http://identifiers.org/hgnc/23386 {source="PMID:36200388", source="https://orcid.org/0000-0001-9310-0163"} ! MYCBP2
property_value: http://purl.org/dc/terms/creator https://orcid.org/0000-0002-7638-4659
property_value: IAO:0000233 "https://github.com/monarch-initiative/vbo/issues/XXXX" xsd:anyURI
```

These improvements would help AI systems better understand VBO's specific conventions and produce changes that more closely align with human expert curation practices.

## IMPORTANT

- never guess identifiers of any kind
- if you include some identifier that was not provided by the user, you MUST check it
- PMIDs can be checked with aurelian or web search
