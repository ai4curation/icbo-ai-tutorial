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

# Vbo Guidelines


## Basic Breed Term Structure

### Minimal Term Example

Every breed term has the recommended structure:

- **id**: VBO identifier
- **name**: Primary breed name with species qualifier
- **is_a**: Taxonomy (NCBITaxon) parent
- **is_a**: VBO:0400000 (Vertebrate breed)
- **synonym**: At least one synonym marked as `most_common_name`
- **dcterms:contributor**: ORCID(s) of contributor(s)
- **dcterms:source**: Source URL(s) or publication(s)

**Exemplar** - VBO:0000042 (Andaluza (Ass)):

```
[Term]
id: VBO:0000042
name: Andaluza (Ass)
subset: transboundary
synonym: "Andaluza" EXACT most_common_name [] {source="ISBN:9781789241532", source="ISBN:9781845934668", source="https://www.fao.org/dad-is"}
is_a: NCBITaxon:9793 ! Equus asinus
is_a: VBO:0400000 {source="https://orcid.org/0000-0002-4142-7153", source="https://orcid.org/0000-0002-5002-8648"} ! Vertebrate breed
property_value: dcterms:contributor https://orcid.org/0000-0002-4142-7153
property_value: dcterms:contributor https://orcid.org/0000-0002-5520-6597
property_value: dcterms:contributor https://orcid.org/0000-0002-9178-3965
property_value: dcterms:source "https://www.fao.org/dad-is" xsd:anyURI
property_value: dcterms:source "ISBN:9781789241532" xsd:string
property_value: dcterms:source "ISBN:9781845934668" xsd:string
property_value: IAO:0000233 "https://github.com/monarch-initiative/vertebrate-breed-ontology/issues/120" xsd:anyURI
property_value: IAO:0000233 "https://github.com/monarch-initiative/vertebrate-breed-ontology/issues/126" xsd:anyURI
```

When working from a GitHub issue, use the orcid of the person making the request

## Naming Conventions

### Primary Names
- **Format**: `[Breed Name] ([Species Common Name])`
- **Examples**:
  - `Abyssinian (Cat)` - VBO:0100000
  - `Broholmer (Dog)` - VBO:0000661
  - `Arab Camel (Dromedary)` - VBO:0000671
  - `Andaluza (Ass)` - VBO:0000042

### Geographic Variants
When a breed is reported in specific countries:
- **Format**: `[Breed Name], [Country] ([Species])`
- **Example**: `Lacaune, United Kingdom of Great Britain and Northern Ireland (Sheep)` - VBO:0015919

### Breed Groups
For parent/grouping terms:
- Use lowercase "breed" suffix
- **Examples**:
  - `Alpaca breed` - VBO:0000038
  - `cat breed` - VBO:7770001
  - `dog breed` - VBO:7770002

---

## Taxonomy and Classification

### Species Assignment

Every breed SHOULD have exactly one NCBITaxon parent:

E.g.

```
is_a: NCBITaxon:9685 ! Felis catus
is_a: NCBITaxon:9615 ! Canis lupus familiaris
is_a: NCBITaxon:9940 ! Ovis aries
```

### Breed Hierarchy

All top level breed terms must assert:
```
is_a: VBO:0400000 ! Vertebrate breed
```

With provenance:
```
is_a: VBO:0400000 {source="https://orcid.org/0000-0002-4142-7153", source="https://orcid.org/0000-0002-5002-8648"} ! Vertebrate breed
```

### Intermediate Groupings (Optional)
Breeds may belong to intermediate groups:
```
is_a: VBO:0400019 ! Bovine breed
is_a: VBO:0400032 ! South American camelid breed
```

---

## Synonyms

### Synonym Structure

```
synonym: "[Synonym Text]" [SCOPE] [TYPE] [] {source="[Source URL or DOI]"}
```

### Synonym Scopes
- **EXACT**: Exact synonym
- **RELATED**: Related term
- **NARROW**: More specific term
- **BROAD**: Less specific term

### Synonym Types
- **most_common_name**: The most commonly used name (exactly ONE per term)
- **ABBREVIATION**: For acronyms/abbreviations

### Best Practices
1. **Always provide sources** for synonyms
2. **Mark exactly one synonym** as `most_common_name`
3. **Multiple sources** can be cited for the same synonym

**Exemplar** - VBO:0000663 (Danish-Swedish Farmdog):
```
synonym: "Danish-Swedish Farmdog" EXACT most_common_name [] {source="https://www.akc.org/dog-breeds/danish-swedish-farmdog/", source="https://www.fci.be/en/nomenclature/DANISH-SWEDISH-FARMDOG-356.html"}
synonym: "Danish-Swedish Farm Dog" EXACT [] {source="https://venomcoding.org/venom-codes/"}
synonym: "Dansk-Svensk Gårdshund" EXACT [] {source="https://www.fci.be/en/nomenclature/DANISH-SWEDISH-FARMDOG-356.html"}
synonym: "Scanian terrier" EXACT [] {source="https://ngdc.cncb.ac.cn/idog/breed/getBreedDetail.action?breedId=86"}
```

---

## Properties

### Breed Codes
Official breed registration codes:
```
property_value: breed_code "ABY" xsd:string {source="https://fifeweb.org/cats/breeds/"}
property_value: breed_code "AB" xsd:string {source="https://www.tica.org/phocadownload/ab.pdf"}
```

### Breed Recognition Status
Recognition by official organizations:
```
property_value: breed_recognition_status VBO:0300002 {source="https://cfa.org/breeds/"}
property_value: breed_recognition_status VBO:0300003 {source="https://www.akc.org/dog-breeds/"}
property_value: breed_recognition_status VBO:0300004 {source="https://wcf.de/en/wcf-ems-code/"}
```

Where:
- VBO:0300002 = Officially recognized
- VBO:0300003 = Foundation Stock Service
- VBO:0300004 = Provisionally accepted

### Subset Membership
```
subset: transboundary  # For breeds found in multiple countries
subset: rare           # For rare breeds
```

---

## Relationships

### has foundation stock (VBO:0300019)
Used when a breed was developed from other breeds or species:

**Exemplar** - VBO:0100040 (Bengal Cat):
```
relationship: VBO:0300019 VBO:0100000 {source="https://en.wikipedia.org/wiki/Bengal_cat"} ! has foundation stock Abyssinian (Cat)
relationship: VBO:0300019 VBO:0100029 {source="https://www.tica.org/breeds/"} ! has foundation stock Asian Leopard Cat (Cat)
relationship: VBO:0300019 VBO:0100053 {source="https://www.gccfcats.org/"} ! has foundation stock Burmese (Cat)
```

**Key Points**:
- Always include source attribution
- Can point to other breeds OR to wild species (NCBITaxon terms)
- Multiple foundation stocks are common

### breed reported in geographic location (VBO:0300020)
Links geographic variants to their location:
```
relationship: VBO:0300020 http://www.wikidata.org/entity/Q145 {source="https://www.fao.org/dad-is"}
```

Uses Wikidata entities for countries/regions.

---

## Metadata and Provenance

### Contributors
**REQUIRED**: Use ORCID identifiers:
```
property_value: dcterms:contributor https://orcid.org/0000-0002-4142-7153
property_value: dcterms:contributor https://orcid.org/0000-0002-5002-8648
property_value: dcterms:contributor https://orcid.org/0000-0002-5520-6597
```

### Sources
**REQUIRED**: Document all data sources:
```
property_value: dcterms:source "https://www.fao.org/dad-is" xsd:anyURI
property_value: dcterms:source "http://omia.org" xsd:anyURI
property_value: dcterms:source "ISBN:9781789241532" xsd:string
```

**URI vs String**:
- URLs: `xsd:anyURI`
- ISBNs, DOIs (when formatted as strings): `xsd:string`

### Issue Tracking
Link to GitHub issues:
```
property_value: IAO:0000233 "https://github.com/monarch-initiative/vertebrate-breed-ontology/issues/200" xsd:anyURI
```

Multiple issues can be referenced:
```
property_value: IAO:0000233 "https://github.com/monarch-initiative/vertebrate-breed-ontology/issues/120" xsd:anyURI
property_value: IAO:0000233 "https://github.com/monarch-initiative/vertebrate-breed-ontology/issues/126" xsd:anyURI
```

### Creation Dates (for relationships/properties)
For new relationship types:
```
property_value: dcterms:created "2024-04-11T14:49:29Z" xsd:dateTime
```

---

## External References

### Cross-References (xrefs)
Link to external databases:
```
xref: FCI:315 {source="https://www.fci.be/en/nomenclature/BROHOLMER-315.html"}
xref: iDog:55 {source="https://ngdc.cncb.ac.cn/idog/breed/getBreedDetail.action?breedId=55"}
xref: VeNom:13964 {source="https://venomcoding.org/venom-codes/"}
```

Common databases:
- **FCI**: Fédération Cynologique Internationale (dog breeds)
- **iDog**: iDog database
- **VeNom**: Veterinary Nomenclature
- **OMIA**: Online Mendelian Inheritance in Animals

**Always include source** attribution for xrefs.

---

## Comments and Definitions

### Definitions
For parent/grouping terms:
```
def: "A breed of Vicugna pacos." [] {source="https://orcid.org/0000-0002-5002-8648", source="https://orcid.org/0000-0002-4142-7153"}
```

### Comments
Provide context when needed:
```
comment: The concept of "breed reported in geographic location by FAO National Coordinators" is unique to DAD-IS (https://www.fao.org/dad-is/en/). This VBO term should therefore be used specifically for the breed conforming to this concept.
```

---

## Logical Definitions (Advanced)

### Disjointness Axioms
For mutually exclusive classes:

**Exemplar** - VBO:7770001 (cat breed) and VBO:7770002 (dog breed):
```
[Term]
id: VBO:7770001
name: cat breed
def: "A vertebrate breed that is a breed of Felis catus (domestic cat)." [https://orcid.org/0000-0002-7638-4659]
intersection_of: VBO:0400000 ! Vertebrate breed
intersection_of: derived_from NCBITaxon:9685 ! Felis catus
disjoint_from: VBO:7770002 ! dog breed
```

---

## Relationship Type Definitions

### Creating New Relationships
Use `[Typedef]` stanzas:

**Exemplar** - VBO:0300019:

```
[Typedef]
id: VBO:0300019
name: has foundation stock
def: "A relation between two distinct material entities (breeds or species), a descendant entity and an ancestor entity, in which the descendant entity is the result of mating, manipulation, or geographical or cultural isolation of the ancestor entity, therefore inheriting some of the ancestor's genetic material." []
comment: [Detailed explanation with examples]
property_value: dcterms:contributor https://orcid.org/0000-0002-4142-7153
property_value: dcterms:created "2024-04-11T14:49:29Z" xsd:dateTime
property_value: IAO:0000116 "Additional notes about usage" xsd:string
property_value: IAO:0000233 "https://github.com/monarch-initiative/vertebrate-breed-ontology/issues/209" xsd:anyURI
domain: NCBITaxon:7742 ! Vertebrata
is_transitive: true
```

---

## Summary of Key Principles

1. **Complete Attribution**: Every fact must have a source
2. **Multiple Sources**: Cite all sources that support a claim
3. **ORCID for People**: Use ORCID IDs for contributors
4. **GitHub Issues**: Link terms to issues for traceability
5. **Consistent Naming**: Follow species-specific naming patterns
6. **One most_common_name**: Exactly one synonym must be marked as most common
7. **Source Provenance**: Add {source="..."} to relationships, synonyms, and property_values
8. **External IDs**: Include xrefs to authoritative databases with sources

---

## Common Patterns by Species

### Cat Breeds
- Species: NCBITaxon:9685 (Felis catus)
- Common sources: CFA, TICA, FIFe, WCF, GCCF
- Name format: `[Breed] (Cat)`

### Dog Breeds
- Species: NCBITaxon:9615 (Canis lupus familiaris)
- Common sources: FCI, AKC, UKC, iDog
- Name format: `[Breed] (Dog)`

### Sheep Breeds
- Species: NCBITaxon:9940 (Ovis aries)
- Common sources: FAO DAD-IS
- Name format: `[Breed] (Sheep)` or `[Breed], [Country] (Sheep)`

### Camel Breeds
- Dromedary: NCBITaxon:9838 (Camelus dromedarius)
- Bactrian: NCBITaxon:9837 (Camelus bactrianus)
- Name format: `[Breed] (Dromedary)` or `[Breed] (Bactrian camel)`



## IMPORTANT

- never guess identifiers of any kind
- if you include some identifier that was not provided by the user, you MUST check it
- PMIDs can be checked with aurelian or web search
