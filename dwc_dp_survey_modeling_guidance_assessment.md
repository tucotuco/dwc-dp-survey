# Assessment of the DwC-DP Survey Modeling Guidance

I examined the [Modeling guidance section](https://github.com/timrobertson100/dwc-dp-survey/blob/main/dwc-dp-survey-skills.md#modelling-guidance) of `dwc-dp-survey-skills.md`, comparing it with the ratified DwC-DP 1.0 table schemas, the Event Hierarchy Guide, the `isLeastSpecificTargetCategoryQuantityInclusive` Guidelines, and the interpretation of Surveys and SurveyTargets we have developed.

## Table of Contents

- [Overall assessment](#overall-assessment)
  - [Assessment criteria](#assessment-criteria)
- [Part I. Original data, Event hierarchies, and Surveys](#part-i-original-data-event-hierarchies-and-surveys)
  - [1. Model original records rather than derived products](#1-model-original-records-rather-than-derived-products)
  - [2. Follow the Event Hierarchy Guide](#2-follow-the-event-hierarchy-guide)
  - [3. Stable Location identifiers and separate visit Events](#3-stable-location-identifiers-and-separate-visit-events)
  - [4. Separate activities require separate Events](#4-separate-activities-require-separate-events)
  - [5. Create a Survey only where absence can be inferred](#5-create-a-survey-only-where-absence-can-be-inferred)
  - [6. Create separate SurveyTargets for disjoint counting categories](#6-create-separate-surveytargets-for-disjoint-counting-categories)
  - [7. One Occurrence per count at the finest source granularity](#7-one-occurrence-per-count-at-the-finest-source-granularity)
- [Part II. SurveyTargets, recording categories, and identification](#part-ii-surveytargets-recording-categories-and-identification)
  - [8. Model non-taxonomic recording categories through SurveyTargetDescriptors](#8-model-non-taxonomic-recording-categories-through-surveytargetdescriptors)
  - [9. Fixed detection zones are SurveyTargetDescriptors](#9-fixed-detection-zones-are-surveytargetdescriptors)
  - [10. Aggregates of difficult-to-distinguish species](#10-aggregates-of-difficult-to-distinguish-species)
  - [11. Incidental sightings must belong to separate Events](#11-incidental-sightings-must-belong-to-separate-events)
  - [12. Treat vertical strata like distance bands](#12-treat-vertical-strata-like-distance-bands)
  - [13. Preserve ordinal vegetation cover and abundance classes](#13-preserve-ordinal-vegetation-cover-and-abundance-classes)
  - [14. Use authoritative IRIs for Assertion types](#14-use-authoritative-iris-for-assertion-types)
  - [15. Preserve field-recorded values, not later estimates](#15-preserve-field-recorded-values-not-later-estimates)
- [Part III. Non-target organisms, materials, and molecular data](#part-iii-non-target-organisms-materials-and-molecular-data)
  - [16. Organisms defining the sampling unit are non-target Occurrences](#16-organisms-defining-the-sampling-unit-are-non-target-occurrences)
  - [17. Physical samples belong in the Material table](#17-physical-samples-belong-in-the-material-table)
  - [18. DNA metabarcoding](#18-dna-metabarcoding)
    - [18a. Create one Occurrence per sample × ASV](#18a-create-one-occurrence-per-sample--asv)
    - [18b. Store read counts in NucleotideAnalysis, not organismQuantity](#18b-store-read-counts-in-nucleotideanalysis-not-organismquantity)
    - [18c. Link molecular evidence to Occurrences through Identification](#18c-link-molecular-evidence-to-occurrences-through-identification)
    - [18d. Preserve taxonomic uncertainty using taxonFormula](#18d-preserve-taxonomic-uncertainty-using-taxonformula)
    - [18e. Create one MolecularProtocol per extract](#18e-create-one-molecularprotocol-per-extract)
- [Part IV. Derived measurements, spatial information, and hierarchy design](#part-iv-derived-measurements-spatial-information-and-hierarchy-design)
  - [19. Preserve later-derived values as Assertions with provenance](#19-preserve-later-derived-values-as-assertions-with-provenance)
  - [20. Preserve original source codes as Assertions](#20-preserve-original-source-codes-as-assertions)
  - [21. Preserve verbatim coordinates and calculate uncertainty](#21-preserve-verbatim-coordinates-and-calculate-uncertainty)
  - [22. Do not invent parent Events merely to construct a hierarchy](#22-do-not-invent-parent-events-merely-to-construct-a-hierarchy)
- [Consolidated assessment](#consolidated-assessment)
- [An important error in the preceding “Checks against the schemas” section](#an-important-error-in-the-preceding-checks-against-the-schemas-section)
- [My recommended priorities](#my-recommended-priorities)
  - [1. Define Survey and SurveyTarget before providing mapping rules](#1-define-survey-and-surveytarget-before-providing-mapping-rules)
  - [2. Explain how to decide what deserves an Event](#2-explain-how-to-decide-what-deserves-an-event)
  - [3. Separate nondetection inference from reporting completeness](#3-separate-nondetection-inference-from-reporting-completeness)
  - [4. Establish a disciplined approach to SurveyTargetDescriptors](#4-establish-a-disciplined-approach-to-surveytargetdescriptors)
  - [5. Distinguish observed entities, measurements, methods, and derived information](#5-distinguish-observed-entities-measurements-methods-and-derived-information)
- [Final assessment](#final-assessment)
- [Primary reference documents](#primary-reference-documents)

## Overall assessment

The document is a useful starting point, but **I would not yet consider it reliable guidance for an LLM mapping arbitrary survey datasets into DwC-DP**.

The central problem is not widespread incompatibility with the schemas. Rather, it repeatedly turns decisions made for particular datasets into general modeling rules.

Three recurring problems stand out:

- **Confusing a Survey with a mechanism for inferring nondetection.** A Survey may be useful for many kinds of ecological inference without supporting nondetection inference.
- **Confusing SurveyTargets with recording categories.** Distance bands, vegetation strata, identification categories, and other recording dimensions are not necessarily separate SurveyTargets.
- **Mandating particular representations where the correct choice depends on the source.** This affects Event boundaries, individual Occurrences, incidental observations, and molecular data.

These problems are especially consequential because the document is intended to guide LLMs. Statements such as "always create," "only create," and "do not create" can cause a model to systematically misrepresent source data when those conditions are not universally valid.

### Assessment criteria

I use three classifications:

- **Correct:** Consistent with the model, sufficiently precise, and suitable as general guidance.
- **Partially correct:** Describes a legitimate mapping approach but overgeneralizes, is ambiguous, or omits essential conditions.
- **Erroneous:** Establishes a rule that would produce incorrect mappings for legitimate datasets or contradicts the model's semantics.

For every point, I provide replacement language. Where a point is unnecessary as a separate rule, I recommend removing or merging it.

The assessment covers all 22 top-level points in the section, including the five subpoints concerning DNA metabarcoding.

---

## Part I. Original data, Event hierarchies, and Surveys

### 1. Model original records rather than derived products

**Verdict: Partially correct**

The intention is sound: preserve the original observations rather than replacing them with indices, trends, or other analytical summaries.

However, derived products can also be legitimate information to preserve in DwC-DP. The important distinction is between primary observations and derived information, not between information that may or may not be included.

For example, annual abundance estimates calculated from repeated counts should not replace those counts, but they may be retained with their derivation and provenance.

**Suggested replacement:**

> Preserve the original observational records at their finest available resolution. Do not replace them with derived products such as indices, trends, or annual abundance estimates. Derived information may also be included where appropriate, provided that it is clearly distinguished from the original observations and its derivation is documented.

### 2. Follow the Event Hierarchy Guide

**Verdict: Partially correct**

The document correctly invokes spatiotemporal containment, non-inheritance, and non-derivation.

However, it oversimplifies the [Event Hierarchy Guide](https://dwc.tdwg.org/hierarchy/).

In particular, its advice to place each value only at the level where it was recorded misses the **Principle of Applicability**. If a Survey property genuinely applies at multiple hierarchy levels, it should be explicitly represented at each applicable level, even if the original source documented it only once.

Similarly, non-derivation concerns the inappropriate generation of parent Survey properties from child values. It does not prohibit calculating an Event's spatial or temporal envelope.

The guide's §§3.2.1–3.2.4 establish these distinctions.

**Suggested replacement:**

> Follow the Event Hierarchy Guide. Parent Events must encompass their children spatially and temporally. Survey properties should be explicitly represented at every Event level to which they directly apply; they must not be assumed to be inherited. Do not construct parent Survey properties merely by aggregating child values when those properties do not independently apply to the parent. Preserve the precision of source information, and do not invent unrecorded measurements or times.

### 3. Stable Location identifiers and separate visit Events

**Verdict: Correct**

This is a good general modeling principle.

A sampling location can persist through time, whereas repeated sampling visits constitute distinct Events.

For example, a permanent vegetation plot retains its Location identity across annual surveys, but each annual visit has a distinct Event identity.

**Suggested replacement (minor clarification):**

> Use a stable `locationID` for a persistent sampling location that is revisited. Represent each distinct sampling visit as a separate Event, preserving the relationship between the Events and their common Location.

### 4. Separate activities require separate Events

**Verdict: Partially correct**

This is a legitimate strategy, but not a universal requirement.

Habitat recording and organism counting can occur as components of the same sampling Event. Their different methodologies can be documented using Protocols and Assertions without necessarily creating separate Events.

Conversely, if the activities are genuinely independent sampling processes, with distinct temporal contexts, spatial extents, or analytical purposes, separate Events may be appropriate.

**The criterion should be whether the activities constitute distinct Events, not merely whether the protocol names them separately.**

**Suggested replacement:**

> Represent activities as separate Events when they constitute distinct sampling or observation processes that need separate spatial, temporal, or procedural contexts. Activities performed as components of a single Event may instead be represented through the Event's Survey, Protocols, Assertions, and other related resources. Do not create separate Events solely because different kinds of information were recorded.

### 5. Create a Survey only where absence can be inferred

**Verdict: Erroneous**

This is one of the most important errors in the document.

The ratified [Survey schema](https://github.com/tdwg/rs.tdwg.org/blob/master/dwc-dp/1.0/table-schemas/survey.json) defines Survey broadly as an Event intentionally designed to characterize a biotic target or domain so that its Occurrences can collectively support ecological and monitoring inference.

That includes detectability, abundance, co-occurrence, spatial distribution, and temporal trends. It does **not** require that nondetection inference be possible.

The schema explicitly allows a SurveyTarget to have `isSurveyTargetFullyReported = false`.

Thus, incomplete reporting does not disqualify an activity from being a Survey.

The proposed treatment of an optional "did you look?" flag is also too restrictive. The answer may determine whether a target was actually surveyed, and whether nondetection inference is justified, but it does not independently determine whether the encompassing activity was a Survey.

**Suggested replacement:**

> Create a Survey for an Event intentionally designed to characterize a defined biotic target or domain so that its resulting Occurrences can support ecological or monitoring inference. Nondetection inference is not required for an Event to qualify as a Survey.
>
> Associate SurveyTargets according to the actual sampling design and activities performed. For optional components, preserve information about whether sampling was undertaken. Set `isSurveyTargetFullyReported` according to whether all detected Occurrences within the target were reported, rather than assuming that an affirmative participation flag establishes reporting completeness.

This also avoids confusing **sampling participation**, **reporting completeness**, and **detectability**.

### 6. Create separate SurveyTargets for disjoint counting categories

**Verdict: Partially correct**

The recommendation to consider separate SurveyTargets for distance bands is reasonable in some surveys.

But it assumes that a recording category is necessarily a target.

A SurveyTarget describes criteria under which Occurrences are counted. A distance band may instead be an attribute of the detected organisms' positions relative to a transect.

For example, a bird Survey could have one SurveyTarget for all eligible birds, while the Occurrences preserve their detection-distance categories. Alternatively, the survey design might explicitly define separate counting targets for different distances.

The right choice depends on what the protocol was designed to count, not just on how its recording form is organized.

**Suggested replacement:**

> Determine SurveyTargets from the sampling design and the populations or categories that the Survey intended to count. Disjoint recording categories, such as detection-distance bands, may warrant separate SurveyTargets when they define distinct counting scopes. Otherwise, preserve those categories as properties of the Occurrences or their sampling context. Do not assume that every recording category constitutes a separate SurveyTarget.

### 7. One Occurrence per count at the finest source granularity

**Verdict: Partially correct**

This is appropriate for aggregated counts but would be incorrect as a universal rule.

If the source records seven unidentified birds as one count, there is no justification for fabricating seven individual Organisms.

But if the source records seven individually marked birds, each with its own identity, history, or observations, then separate Occurrences—and potentially separate Organism records—are entirely appropriate.

The rule concerning `notDetected` records is better. One should not invent observations of nondetection merely because a Survey's completeness permits those nondetections to be inferred.

The ratified [Occurrence schema](https://github.com/tdwg/rs.tdwg.org/blob/master/dwc-dp/1.0/table-schemas/occurrence.json) permits both `organismQuantity` and links to individual Organisms.

**Suggested replacement:**

> Preserve Occurrences at the finest meaningful granularity available in the source data. Represent aggregated organism counts using `organismQuantity` and `organismQuantityType`, without inventing individual Organism identities. Where individual organisms or individual occurrences were distinguished by the source, preserve those distinctions. Do not fabricate explicit `notDetected` Occurrences when the source supplies only positive observations; preserve the Survey information necessary for valid nondetection inference.

---

## Part II. SurveyTargets, recording categories, and identification

### 8. Model non-taxonomic recording categories through SurveyTargetDescriptors

**Verdict: Partially correct**

The examples include categories such as solitary bees, residual groups of flies, and unidentified insects.

The intention is good: faithfully preserve categories that are not represented by a single taxonomic name.

However, the guidance assumes all such categories can be decomposed using inclusion and exclusion descriptors. That is not necessarily true.

We've established that **SurveyTargetDescriptors combine criteria using AND and AND NOT, not OR**.

For example, "other flies" can be defined as Diptera excluding explicitly named groups, provided the exclusions collectively define exactly what "other" meant in the original protocol.

But a category such as "other insects / unsure" may represent uncertainty in identification rather than a biologically distinct counting population.

These are not equivalent.

Furthermore, setting `isLeastSpecificTargetCategoryQuantityInclusive = false` simply because a residual group excludes other groups is incorrect reasoning. That property describes how reported quantities for broader taxonomic categories relate to quantities for their subcategories. It is not a generic flag for mutually exclusive SurveyTargets. See the [normative guidelines](https://dwc.tdwg.org/inclusive/).

**Suggested replacement:**

> Preserve the original recording categories and determine whether each represents a SurveyTarget, a taxonomic identification category, or another recording distinction. Where a SurveyTarget can be defined by inclusion and exclusion criteria, express its complete definition using SurveyTargetDescriptors. Remember that multiple descriptors are conjunctive; do not use them to represent alternative criteria.
>
> Preserve the original identification in `verbatimIdentification`, and use `scientificName` for the most specific taxonomic identification justified by the source. Do not imply greater taxonomic precision than the source supports.
>
> Set `isLeastSpecificTargetCategoryQuantityInclusive` according to the relationship among reported organism quantities, following the normative guidelines, rather than from the existence of residual recording categories.

### 9. Fixed detection zones are SurveyTargetDescriptors

**Verdict: Erroneous**

This statement conflates two different things:

- The spatial extent or geometry of the sampling operation.
- The criteria defining the organisms to be counted under a SurveyTarget.

They can coincide, but they need not.

Suppose a Survey counts butterflies within a 5 × 5 m plot.

The area describes where the Survey was performed. The SurveyTarget could simply be "all butterflies".

Now suppose another Survey follows the same protocol but uses a 10 × 10 m plot. The two Surveys might legitimately reference the same SurveyTarget while differing in their sampled areas.

Requiring distinct SurveyTargets merely because the plot dimensions differ would unnecessarily incorporate sampling geometry into target identity.

The ratified Survey schema provides `totalAreaSampledValue` and `totalAreaSampledUnit` for the actual extent sampled. Interestingly, its definition permits area, volume, or distance, not just area.

**Suggested replacement:**

> Represent sampling-zone dimensions and geometry as properties of the Survey's spatial context, sampled extent, and sampling protocol. When spatial position or distance is itself a criterion defining the SurveyTarget, include that criterion in its SurveyTargetDescriptors. Do not create distinct SurveyTargets solely because the sampling zones used by different Surveys have different dimensions.

### 10. Aggregates of difficult-to-distinguish species

**Verdict: Partially correct**

This is especially interesting because it reproduces the Small Skipper / Essex Skipper case we just examined.

The guidance correctly recognizes that the source may not distinguish the two species and that the recorded identification must not be arbitrarily resolved to either one.

But it does not explain how to define the actual SurveyTarget.

Using the genus *Thymelicus* as `scientificName` identifies the taxonomic level to which the record can be safely assigned. It does not, by itself, define the group consisting of precisely the two intended species.

For a Survey restricted to the British butterfly fauna, our proposed target was:

| includeOrExclude | surveyTargetType | surveyTargetValue |
|---|---|---|
| include | taxon | *Thymelicus* |
| exclude | taxon | *Thymelicus acteon* |

The intended definition depends on the agreed geographical and taxonomic scope. If other *Thymelicus* species are possible within that scope, further exclusions are necessary.

The distinction is fundamental:

**The identification of an Occurrence and the definition of the SurveyTarget under which it was counted are separate pieces of information.**

The proposed guidance also risks suggesting that `isLeastSpecificTargetCategoryQuantityInclusive` should be used to resolve identification ambiguity. It should not. That property applies to relationships among numeric quantities for broader and narrower categories.

**Suggested replacement:**

> For counts recorded under an aggregate identification, preserve the original aggregate name in `verbatimIdentification`. Set `scientificName` to the most specific taxonomic name supported by the identification, without assigning an unsupported species name.
>
> If the aggregate is a SurveyTarget, define the complete intended group through its SurveyTargetDescriptors. Use an inclusion of a containing taxon with appropriate exclusions where that accurately describes the group, or a single descriptor referencing an authoritative definition of the group.
>
> Do not confuse uncertainty in taxonomic identification with inclusion of narrower-category quantities in a broader reported total.

### 11. Incidental sightings must belong to separate Events

**Verdict: Erroneous**

This is another significant conceptual error.

The guidance assumes that a non-target observation cannot belong to the same Event as the Survey's target observations.

But a Survey can legitimately have incidental Occurrences that were observed during the same Event.

An Occurrence's `surveyTarget_fk` is optional, precisely allowing Occurrences without a SurveyTarget association. This provides a direct way to represent incidental observations without pretending that they were counted under the Survey's targets.

For example, during a bird survey an observer might record a fox. The fox can be an Occurrence associated with the same Event, but without being counted under any of the bird SurveyTargets.

Creating another Event would be unnecessary.

There is a separate case where an incidental observation takes place outside the Survey Event's actual spatial or temporal extent. In that case, a distinct Event may be justified, potentially sharing an encompassing parent Event.

**Suggested replacement:**

> Incidental observations made within the spatial and temporal context of a Survey may be represented as Occurrences associated with that Survey's Event, without a `surveyTarget_fk`.
>
> If an observation belongs to a different spatial, temporal, or procedural context, represent it under an appropriate separate Event. Where useful, relate that Event to the Survey Event through a common parent whose spatial and temporal extent encompasses both. Do not create separate Events merely to distinguish target from non-target Occurrences.

This is considerably simpler than the original rule and follows the intended semantics of the model.

### 12. Treat vertical strata like distance bands

**Verdict: Erroneous**

This point makes three assumptions:

1. Each stratum must be a separate SurveyTarget.
2. Each species recorded in multiple strata must have separate Occurrences.
3. Strata cannot be Events.

None is universally true.

A vertical stratum can be a SurveyTarget criterion when membership in that stratum determines which organisms are eligible to be counted under a target.

But strata can also be sampling units with their own Events, particularly where different layers are sampled independently, at different times, or with different protocols.

Alternatively, stratum may simply be an observed attribute of a vegetation record.

The guidance should not prescribe a structural representation before determining which of these situations applies.

**Suggested replacement:**

> Represent vertical strata according to their role in the sampling design. Use SurveyTargetDescriptors when stratum membership defines distinct target scopes. Use separate Events when strata are independently sampled units with distinct Event contexts. Otherwise, preserve stratum information as appropriate attributes or Assertions associated with the relevant observations or Events.
>
> Preserve independent measurements and counts at their original resolution. Document protocol-defined stratum boundaries and distinguish them from measured stratum properties.

### 13. Preserve ordinal vegetation cover and abundance classes

**Verdict: Correct**

This is good guidance.

The ratified Occurrence schema explicitly allows nonnumeric enumeration values in `organismQuantity`, including Braun-Blanquet codes. The corresponding `organismQuantityType` identifies the quantification system.

Converting ordinal classes into percentages would introduce information not actually reported and could falsely imply quantitative precision.

The suggestion to separate combined cover-abundance and sociability codes is also reasonable where the meaning of the components is documented.

I would add that the original combined notation must remain recoverable.

**Suggested replacement:**

> Preserve ordinal cover or abundance values in `organismQuantity`, with the original scale identified in `organismQuantityType` and documented through the relevant Protocol. Do not convert ordinal values into continuous measurements without a separately documented transformation. Where a source code combines distinct measurements, these may be represented separately using Occurrence Assertions, while preserving the original combined notation.

### 14. Use authoritative IRIs for Assertion types

**Verdict: Correct**

This is appropriate and consistent with the ratified `event-assertion` and `occurrence-assertion` schemas. Both support `assertionTypeIRI`.

One minor improvement would be to say that the selected vocabulary term must correspond to the actual meaning of the assertion. A superficially similar term is not sufficient.

**Suggested replacement:**

> When an authoritative vocabulary provides a term that accurately expresses the meaning of an Assertion, use its IRI in `assertionTypeIRI`, together with the appropriate assertion type and source information. Preserve the original field name separately where needed.

### 15. Preserve field-recorded values, not later estimates

**Verdict: Partially correct**

The first part is correct: if an observer recorded "average sunshine" as a field assessment, that information should be retained as a field observation.

The second part is too restrictive.

A value estimated after sampling can legitimately be published as a derived Assertion or analytical result. It must simply not be misrepresented as an original field observation.

This duplicates much of point 1.

**Recommendation: Remove as a separate point and merge its useful content into point 1.**

Suggested addition to point 1:

> Preserve both originally recorded and subsequently derived information where appropriate, explicitly distinguishing their provenance and methods. Never present an estimated, interpolated, or imputed value as though it were directly observed.

---

## Part III. Non-target organisms, materials, and molecular data

### 16. Organisms defining the sampling unit are non-target Occurrences

**Verdict: Partially correct**

The example of a flower being watched during a pollinator Survey is useful.

If the flower is actually documented as an observed biological entity, representing it as an Occurrence without a SurveyTarget association may be appropriate.

Likewise, `organism-interaction` is appropriate for an observed interaction between the pollinator and the flower. The ratified schema specifically supports pairwise interactions between Occurrences.

However, the guidance goes too far in making this representation mandatory.

A flower or host plant might instead be documented as part of the sampling context, without the source recording a distinct Occurrence of that plant.

More importantly, `hasNonTargetTaxa` does not simply mean that some organisms were not counted under SurveyTargets. It specifically concerns reported taxa outside the Survey's declared taxonomic scope.

An Occurrence without `surveyTarget_fk` is not automatically evidence that `hasNonTargetTaxa` should be `true`.

**Suggested replacement:**

> When the source records organisms that provide the biological context for sampling, distinguish their role as sampling subjects from the organisms counted under SurveyTargets.
>
> Represent these organisms as Occurrences when the source provides sufficient information to establish their occurrence. Where interactions between organisms were observed, represent them through `organism-interaction`, linking the relevant Occurrences.
>
> Leave `surveyTarget_fk` empty when an Occurrence was not counted under a SurveyTarget. Set `hasNonTargetTaxa` according to whether taxa outside the Survey's declared taxonomic scope were actually reported, rather than simply because some Occurrences have no SurveyTarget association.

### 17. Physical samples belong in the Material table

**Verdict: Correct, with a qualification**

This is generally good guidance.

The ratified `material` schema supports:

- `collectionEvent_fk`, associating material with its collection Event.
- `derivedFromMaterialEntity_fk`, associating derived material with its source material.
- `measuredMassInGrams`, describing the mass of a MaterialEntity.

These are appropriate for bulk samples, subsamples, extracts, and similar physical entities.

The qualification concerns the meaning of biomass.

The measured mass of a physical sample is a property of the MaterialEntity. But an estimate of organism biomass in an ecological Survey may legitimately be reported as an organism quantity.

The guidance should distinguish these.

**Suggested replacement:**

> Represent collected physical entities, including bulk samples, subsamples, and extracts, in the `material` table. Associate collected materials with their collection Events, and represent derivation relationships through `derivedFromMaterialEntity_fk`.
>
> Record measurements of the physical material, such as sample mass, as MaterialEntity properties. Distinguish these from measurements or estimates of organism quantities, such as taxon-specific biomass, which may appropriately be represented on Occurrences.

### 18. DNA metabarcoding

This point contains five separate instructions. I would assess them independently because they have different levels of correctness.

#### 18a. Create one Occurrence per sample × ASV

**Verdict: Partially correct**

An amplicon sequence variant (ASV) is a sequence variant detected through molecular analysis. It is not inherently an organism or an observation of an organism.

One Occurrence per sample–ASV combination can be an appropriate representation when the original analysis supports interpreting the sequence detection as evidence of biological occurrence.

But the rule is too categorical.

An ASV might represent contamination, an analytical artefact, or otherwise insufficient evidence of a particular organism's occurrence. Multiple technical replicates may also affect the appropriate representation.

**Suggested replacement:**

> Preserve ASV detections and their relationships to samples, nucleotide sequences, and analyses at the finest meaningful source resolution. Create Occurrences where the analytical results support assertions of biological occurrence. Do not assume that every detected ASV necessarily establishes an Occurrence.

#### 18b. Store read counts in NucleotideAnalysis, not organismQuantity

**Verdict: Partially correct**

The central distinction is correct.

Sequence-read counts are not equivalent to quantities of organisms. The `nucleotide-analysis` table explicitly includes `readCount`, and the Survey table includes `isAbundanceReported`.

However, setting `isAbundanceReported = false` should depend on what the Survey actually reports.

A molecular Survey could provide independent organism abundance estimates in addition to sequence reads.

**Suggested replacement:**

> Represent sequence-read counts using the appropriate NucleotideAnalysis fields rather than treating them as organism quantities. Populate `organismQuantity` only when the source reports a corresponding biological quantity. Set `isAbundanceReported` according to whether organism abundance was actually reported, rather than merely according to the presence of molecular read counts.

#### 18c. Link molecular evidence to Occurrences through Identification

**Verdict: Correct**

The proposed structure is supported by the ratified schemas.

The `identification` table has `occurrence_fk`, `nucleotideAnalysis_fk`, and `nucleotideSequence_fk`, and recognizes `nucleotideAnalysis` as a value of `identificationType`.

This allows the provenance of a molecular identification to remain explicit.

I would only qualify the wording to avoid requiring an Identification record for every molecular result, including results that do not support a biological identification.

**Suggested replacement:**

> For Occurrences identified from molecular evidence, use the `identification` table to associate the Occurrence with the relevant NucleotideAnalysis and NucleotideSequence. Use `identificationType = nucleotideAnalysis` when applicable, and preserve the identification's evidentiary basis and uncertainty.

#### 18d. Preserve taxonomic uncertainty using taxonFormula

**Verdict: Partially correct**

The principle is correct: do not assign a species-level identity when the evidence supports only a genus, family, or other broader identification.

The `identification` schema also supports `taxonFormula` and the `identification-taxon` relationship for representing more complex identifications.

However, `taxonFormula` should be used when the identification genuinely requires a combination of taxonomic components. It is not necessary for every ambiguous or lower-rank identification.

Importantly, `taxonFormula` can represent alternatives in an Identification. That does not change the AND/AND NOT semantics we have established for SurveyTargetDescriptors.

**Suggested replacement:**

> Preserve taxonomic identifications at the level of precision supported by the molecular evidence. Use broader taxonomic identifications when finer resolution is not justified. Where the identification explicitly involves alternative or combined taxonomic concepts, represent these using `taxonFormula` and `identification-taxon` as appropriate. Preserve uncertainty rather than choosing an unsupported taxon.

#### 18e. Create one MolecularProtocol per extract

**Verdict: Erroneous**

This is a clear conceptual error.

A MolecularProtocol describes a method or procedure. An extract is a MaterialEntity.

The `molecular-protocol` schema explicitly defines it as a protocol used to perform a NucleotideAnalysis, potentially deriving a NucleotideSequence from a MaterialEntity. The `nucleotide-analysis` table links to the applicable MolecularProtocol.

If twenty extracts undergo exactly the same molecular procedure, there is no requirement to create twenty different MolecularProtocol records.

The MolecularProtocol can be shared.

Distinct material extracts should be represented as distinct MaterialEntities, with their appropriate derivation relationships.

**Suggested replacement:**

> Create MolecularProtocol records for distinct molecular methods or protocol configurations. Reuse a MolecularProtocol wherever the same procedure applies. Represent individual extracts as MaterialEntities and associate the relevant NucleotideAnalyses with their MolecularProtocols and source materials.

---

## Part IV. Derived measurements, spatial information, and hierarchy design

### 19. Preserve later-derived values as Assertions with provenance

**Verdict: Partially correct**

This is a sound approach for values such as GIS-derived land cover or a vegetation plot's assignment to a phytosociological syntaxon.

The important principle is preserving the distinction between what an observer recorded in the field and what was subsequently inferred, classified, or calculated.

However, the recommendation that every derived value receive its own Protocol is unnecessarily prescriptive. A single Protocol may be applicable to many derived Assertions.

The ratified Assertion schemas support `assertionBy`, `assertionProtocol_fk`, `assertionMadeDate`, and `assertionEffectiveDate`, providing useful mechanisms for distinguishing the creation of an Assertion from the time to which it applies.

**Suggested replacement:**

> Represent subsequently derived measurements, classifications, and other statements as Assertions where appropriate. Preserve their derivation methods, responsible Agents, dates, and other provenance information. Reuse Protocol records when the same derivation method applies to multiple Assertions. Distinguish the time an Assertion was made from the time or interval to which it applies.

### 20. Preserve original source codes as Assertions

**Verdict: Partially correct**

Preserving source codes is important, and defining their meanings through the relevant Protocol is good practice.

However, source codes do not necessarily belong in Assertions.

A source code might represent a taxonomic identification, life stage, organism quantity, detection method, vegetation characteristic, or some other attribute already accommodated by an explicit DwC-DP field.

Making every source code an Assertion would be an unnecessary restriction.

**Suggested replacement:**

> Preserve the original source codes and their definitions. Map codes to the most appropriate DwC-DP fields or related resources according to their meanings. Use Assertions for recorded properties that are appropriately expressed as statements about Events, Occurrences, or other supported subjects. Document code meanings and the versions of the coding systems in the relevant Protocols or references.

### 21. Preserve verbatim coordinates and calculate uncertainty

**Verdict: Partially correct**

The advice to preserve `verbatimCoordinates` and `verbatimSRS` is correct.

So is the general recommendation to derive decimal coordinates where possible and provide an appropriate coordinate uncertainty.

However, the prescribed approach to uncertainty is too simple.

The definition of `coordinateUncertaintyInMeters` requires the radius of the smallest circle centered on the given coordinates that encompasses the entire Location.

For an exact square represented by its center, the half-diagonal is the minimum radius needed to encompass the square. But it would not necessarily account for additional uncertainty in the source's location or coordinate transformation.

The guidance should distinguish coordinate precision, the extent of the original Location, and uncertainty introduced during georeferencing.

The time-zone recommendation is appropriate, provided the original time zone is actually known.

**Suggested replacement:**

> Preserve original spatial information using the appropriate verbatim coordinate, coordinate-system, and spatial-reference fields. Where justified, derive decimal coordinates using a documented transformation.
>
> Calculate `coordinateUncertaintyInMeters` according to the spatial extent and uncertainty of the original Location, rather than assuming that coordinate precision alone establishes uncertainty. Where the original Location is a known area, retain its footprint when possible.
>
> Preserve original temporal precision and include time-zone information when it is known. Do not invent a time zone or imply greater precision than the source supports.

### 22. Do not invent parent Events merely to construct a hierarchy

**Verdict: Correct**

I agree completely.

An Event hierarchy should express actual relationships among Events, not a desired organizational structure imposed on the dataset.

Unrelated Events do not require a common parent merely because they belong to the same dataset.

This is consistent with the Event Hierarchy Guide, which permits but does not require hierarchical organization.

**Suggested replacement (minor clarification):**

> Construct parent–child Event relationships only where they represent genuine containment relationships among the sampling or observation activities. Do not invent parent Events solely to organize independent records into a hierarchy.

---

## Consolidated assessment

The following summarizes the verdicts and recommended actions for the 22 top-level points. The DNA metabarcoding point is broken into five subpoints above.

| # | Topic | Verdict | Action |
|---|---|---|---|
| 1 | Original records | Partially correct | Revise |
| 2 | Event hierarchy principles | Partially correct | Revise |
| 3 | Persistent Locations | Correct | Minor clarification |
| 4 | Separate activities | Partially correct | Revise |
| 5 | Surveys require absence inference | Erroneous | Replace |
| 6 | Distance bands as targets | Partially correct | Revise |
| 7 | One Occurrence per count | Partially correct | Revise |
| 8 | Non-taxonomic counting categories | Partially correct | Revise |
| 9 | Fixed detection-zone dimensions | Erroneous | Replace |
| 10 | Difficult species aggregates | Partially correct | Revise |
| 11 | Separate Events for incidental sightings | Erroneous | Replace |
| 12 | Vertical strata | Erroneous | Replace |
| 13 | Ordinal cover classes | Correct | Minor clarification |
| 14 | Assertion type IRIs | Correct | Minor clarification |
| 15 | Field values vs. estimates | Partially correct | Merge into #1 |
| 16 | Sampling-subject organisms | Partially correct | Revise |
| 17 | Physical samples | Correct | Qualify |
| 18 | DNA metabarcoding | Mixed | Revise individual subpoints |
| 19 | Derived Assertions | Partially correct | Revise |
| 20 | Original source codes | Partially correct | Revise |
| 21 | Coordinates and uncertainty | Partially correct | Revise |
| 22 | Unnecessary parent Events | Correct | Minor clarification |

There is one additional issue immediately preceding this section that I think deserves attention.

## An important error in the preceding “Checks against the schemas” section

Although outside your requested scope, one statement in that section reinforces several problems identified above.

It argues that because an Occurrence has a single `surveyTarget_fk`, SurveyTargets used for classifying counts must be mutually exclusive.

That conclusion does not follow from the schema.

As we established in our earlier discussion:

- An Occurrence row has at most one `surveyTarget_fk`.
- The same underlying Occurrence can be represented by multiple rows with different `occurrence_pk` values while preserving the same `occurrenceID`.
- The [isLeastSpecificTargetCategoryQuantityInclusive Guidelines](https://dwc.tdwg.org/inclusive/) explicitly support reported organism quantities that include organisms also represented by narrower-category quantities.
- Mutually exclusive counting populations are a good design principle when independently additive counts are intended, but this is not a universal structural requirement of DwC-DP.

I would replace that statement with:

> Each Occurrence row has at most one `surveyTarget_fk`, identifying the SurveyTarget under which it is counted. When the same Occurrence must be explicitly attributed to multiple SurveyTargets, separate rows can preserve the same `occurrenceID` while using distinct `occurrence_pk` and `surveyTarget_fk` values. SurveyTarget design should preserve the intended counting semantics, and overlapping quantities must be interpreted according to the applicable reporting conventions.

This is important because the document otherwise risks teaching LLMs to reject valid data structures or introduce unnecessary transformations.

---

## My recommended priorities

If I were revising this guidance for use by LLMs, I would prioritize five corrections.

### 1. Define Survey and SurveyTarget before providing mapping rules

The document should establish that a Survey is an intentionally designed Event supporting ecological inference, while SurveyTargets define the criteria under which Occurrences are counted. Neither is defined by the structure of a recording form.

### 2. Explain how to decide what deserves an Event

Distinguish real sampling activities and their contexts from observation attributes, target criteria, and merely organizational groupings. This would resolve many of the errors involving habitat observations, incidental sightings, distance bands, and vegetation strata.

### 3. Separate nondetection inference from reporting completeness

Explain explicitly that the existence of a Survey, the applicability of a SurveyTarget, participation in a sampling activity, and the completeness of reporting are distinct matters.

### 4. Establish a disciplined approach to SurveyTargetDescriptors

Multiple descriptors collectively define one target, using AND and AND NOT. The model does not treat multiple inclusion descriptors as OR alternatives. Original identification categories should not automatically become SurveyTargets.

### 5. Distinguish observed entities, measurements, methods, and derived information

An Organism, Occurrence, MaterialEntity, Protocol, and Assertion each represents something different. Treating a separate field, measurement, sample, or extraction as requiring an independently identified instance of every related class will produce unnecessarily complicated—and potentially incorrect—packages.

## Final assessment

I see the document as evidence of substantial practical engagement with DwC-DP. Many of its observations are valuable, and several of its mapping strategies are exactly what I would choose in the appropriate circumstances.

But I would describe it as **case-study experience generalized too aggressively into modeling rules**.

That distinction matters because the aim is to teach an LLM how to *reason from an unfamiliar source dataset to the appropriate DwC-DP representation*, not simply to reproduce structural patterns that worked for previous datasets.

A stronger guidance document would instruct the model to determine, in order:

1. What observation or sampling activities occurred, and how they relate spatially and temporally.
2. Which activities constitute Surveys, and what ecological inferences their designs support.
3. What the Surveys intended to count, defining appropriate SurveyTargets and their complete descriptor sets.
4. Which recorded observations are counted under those targets and which are incidental.
5. Which source attributes are observations, measurements, identifications, methodological details, physical samples, or subsequent derivations.
6. How to preserve the original information and its provenance without inventing distinctions or collapsing meaningful ones.

The existing document contains elements of all six, but it often jumps directly to a predetermined table structure before establishing the underlying semantics.

**My principal recommendation is to retain the useful case-specific insights, replace the four erroneous top-level modeling rules and the erroneous molecular-protocol subrule, and revise the partially correct rules so that an LLM must first determine whether the stated conditions apply.**

That would make the guidance considerably more reliable without requiring any change whatsoever to the ratified DwC-DP model.

---

## Primary reference documents

- [Modeling guidance under review: `dwc-dp-survey-skills.md`](https://github.com/timrobertson100/dwc-dp-survey/blob/main/dwc-dp-survey-skills.md)
- [Ratified DwC-DP 1.0 table schemas](https://github.com/tdwg/rs.tdwg.org/tree/master/dwc-dp/1.0/table-schemas)
- [Darwin Core Event Hierarchy Guide](https://dwc.tdwg.org/hierarchy/)
- [isLeastSpecificTargetCategoryQuantityInclusive Guidelines](https://dwc.tdwg.org/inclusive/)
- [DwC-DP Quick Reference Guide](https://dwc.tdwg.org/dwc-dp/qrg/)
