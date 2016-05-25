Reads
*****

This file defines the objects used to represent a reads and alignments, most importantly
ReadGroupSet, ReadGroup, and ReadAlignment.
See {TODO: LINK TO READS OVERVIEW} for more information.

.. avro:enum:: Strand

  :symbols: NEG_STRAND|POS_STRAND
  Indicates the DNA strand associate for some data item.
  * `NEG_STRAND`: The negative (-) strand.
  * `POS_STRAND`:  The postive (+) strand.

.. avro:record:: Position

  :field referenceName:
    The name of the `Reference` on which the `Position` is located.
  :type referenceName: string
  :field position:
    The 0-based offset from the start of the forward strand for that `Reference`.
      Genomic positions are non-negative integers less than `Reference` length.
  :type position: long
  :field strand:
    Strand the position is associated with.
  :type strand: Strand

  A `Position` is an unoriented base in some `Reference`. A `Position` is
  represented by a `Reference` name, and a base number on that `Reference`
  (0-based).

.. avro:record:: ExternalIdentifier

  :field database:
    The source of the identifier.
      (e.g. `Ensembl`)
  :type database: string
  :field identifier:
    The ID defined by the external database.
      (e.g. `ENST00000000000`)
  :type identifier: string
  :field version:
    The version of the object or the database
      (e.g. `78`)
  :type version: string

  Identifier from a public database

.. avro:enum:: CigarOperation

  :symbols: ALIGNMENT_MATCH|INSERT|DELETE|SKIP|CLIP_SOFT|CLIP_HARD|PAD|SEQUENCE_MATCH|SEQUENCE_MISMATCH
  An enum for the different types of CIGAR alignment operations that exist.
  Used wherever CIGAR alignments are used. The different enumerated values
  have the following usage:
  
  * `ALIGNMENT_MATCH`: An alignment match indicates that a sequence can be
    aligned to the reference without evidence of an INDEL. Unlike the
    `SEQUENCE_MATCH` and `SEQUENCE_MISMATCH` operators, the `ALIGNMENT_MATCH`
    operator does not indicate whether the reference and read sequences are an
    exact match. This operator is equivalent to SAM's `M`.
  * `INSERT`: The insert operator indicates that the read contains evidence of
    bases being inserted into the reference. This operator is equivalent to
    SAM's `I`.
  * `DELETE`: The delete operator indicates that the read contains evidence of
    bases being deleted from the reference. This operator is equivalent to
    SAM's `D`.
  * `SKIP`: The skip operator indicates that this read skips a long segment of
    the reference, but the bases have not been deleted. This operator is
    commonly used when working with RNA-seq data, where reads may skip long
    segments of the reference between exons. This operator is equivalent to
    SAM's 'N'.
  * `CLIP_SOFT`: The soft clip operator indicates that bases at the start/end
    of a read have not been considered during alignment. This may occur if the
    majority of a read maps, except for low quality bases at the start/end of
    a read. This operator is equivalent to SAM's 'S'. Bases that are soft clipped
    will still be stored in the read.
  * `CLIP_HARD`: The hard clip operator indicates that bases at the start/end of
    a read have been omitted from this alignment. This may occur if this linear
    alignment is part of a chimeric alignment, or if the read has been trimmed
    (e.g., during error correction, or to trim poly-A tails for RNA-seq). This
    operator is equivalent to SAM's 'H'.
  * `PAD`: The pad operator indicates that there is padding in an alignment.
    This operator is equivalent to SAM's 'P'.
  * `SEQUENCE_MATCH`: This operator indicates that this portion of the aligned
    sequence exactly matches the reference (e.g., all bases are equal to the
    reference bases). This operator is equivalent to SAM's '='.
  * `SEQUENCE_MISMATCH`: This operator indicates that this portion of the
    aligned sequence is an alignment match to the reference, but a sequence
    mismatch (e.g., the bases are not equal to the reference). This can
    indicate a SNP or a read error. This operator is equivalent to SAM's 'X'.

.. avro:record:: CigarUnit

  :field operation:
    The operation type.
  :type operation: CigarOperation
  :field operationLength:
    The number of bases that the operation runs for.
  :type operationLength: long
  :field referenceSequence:
    `referenceSequence` is only used at mismatches (`SEQUENCE_MISMATCH`)
      and deletions (`DELETE`). Filling this field replaces the MD tag.
      If the relevant information is not available, leave this field as `null`.
  :type referenceSequence: null|string

  A structure for an instance of a CIGAR operation.
  `FIXME: This belongs under Reads (only readAlignment refers to this)`

.. avro:record:: OntologyTerm

  :field id:
    Ontology source identifier - the identifier, a CURIE (preferred) or
      PURL for an ontology source e.g. http://purl.obolibrary.org/obo/hp.obo
      It differs from the standard GA4GH schema's :ref:`id <apidesign_object_ids>`
      in that it is a URI pointing to an information resource outside of the scope
      of the schema or its resource implementation.
  :type id: string
  :field term:
    Ontology term - the representation the id is pointing to.
  :type term: null|string
  :field sourceName:
    Ontology source name - the name of ontology from which the term is obtained
      e.g. 'Human Phenotype Ontology'
  :type sourceName: null|string
  :field sourceVersion:
    Ontology source version - the version of the ontology from which the
      OntologyTerm is obtained; e.g. 2.6.1.
      There is no standard for ontology versioning and some frequently
      released ontologies may use a datestamp, or build number.
  :type sourceVersion: null|string

  An ontology term describing an attribute. (e.g. the phenotype attribute
    'polydactyly' from HPO)

.. avro:record:: Experiment

  :field id:
    The experiment UUID. This is globally unique.
  :type id: string
  :field name:
    The name of the experiment.
  :type name: null|string
  :field description:
    A description of the experiment.
  :type description: null|string
  :field createDateTime:
    The time at which this record was created. 
      Format: :ref:`ISO 8601 <metadata_date_time>`
  :type createDateTime: string
  :field updateDateTime:
    The time at which this record was last updated.
      Format: :ref:`ISO 8601 <metadata_date_time>`
  :type updateDateTime: string
  :field runTime:
    The time at which this experiment was performed.
      Granularity here is variable (e.g. date only).
      Format: :ref:`ISO 8601 <metadata_date_time>`
  :type runTime: null|string
  :field molecule:
    The molecule examined in this experiment. (e.g. genomics DNA, total RNA)
  :type molecule: null|string
  :field strategy:
    The experiment technique or strategy applied to the sample.
      (e.g. whole genome sequencing, RNA-seq, RIP-seq)
  :type strategy: null|string
  :field selection:
    The method used to enrich the target. (e.g. immunoprecipitation, size
      fractionation, MNase digestion)
  :type selection: null|string
  :field library:
    The name of the library used as part of this experiment.
  :type library: null|string
  :field libraryLayout:
    The configuration of sequenced reads. (e.g. Single or Paired)
  :type libraryLayout: null|string
  :field instrumentModel:
    The instrument model used as part of this experiment.
        This maps to sequencing technology in BAM.
  :type instrumentModel: null|string
  :field instrumentDataFile:
    The data file generated by the instrument.
      TODO: This isn't actually a file is it?
      Should this be `instrumentData` instead?
  :type instrumentDataFile: null|string
  :field sequencingCenter:
    The sequencing center used as part of this experiment.
  :type sequencingCenter: null|string
  :field platformUnit:
    The platform unit used as part of this experiment. This is a flowcell-barcode
      or slide unique identifier.
  :type platformUnit: null|string
  :field info:
    A map of additional experiment information.
  :type info: map<array<string>>

  An experimental preparation of a `Sample`.

.. avro:record:: Dataset

  :field id:
    The dataset's id, locally unique to the server instance.
  :type id: string
  :field name:
    The name of the dataset.
  :type name: null|string
  :field description:
    Additional, human-readable information on the dataset.
  :type description: null|string

  A Dataset is a collection of related data of multiple types.
  Data providers decide how to group data into datasets.
  See [Metadata API](../api/metadata.html) for a more detailed discussion.

.. avro:record:: Analysis

  :field id:
    Formats of id | name | description | accessions are described in the
      documentation on general attributes and formats.
  :type id: string
  :field name:
  :type name: null|string
  :field description:
  :type description: null|string
  :field createDateTime:
    The time at which this record was created. 
      Format: :ref:`ISO 8601 <metadata_date_time>`
  :type createDateTime: null|string
  :field updateDateTime:
    The time at which this record was last updated.
      Format: :ref:`ISO 8601 <metadata_date_time>`
  :type updateDateTime: string
  :field type:
    The type of analysis.
  :type type: null|string
  :field software:
    The software run to generate this analysis.
  :type software: array<string>
  :field info:
    A map of additional analysis information.
  :type info: map<array<string>>

  An analysis contains an interpretation of one or several experiments.
  (e.g. SNVs, copy number variations, methylation status) together with
  information about the methodology used.

.. avro:record:: Program

  :field commandLine:
    The command line used to run this program.
  :type commandLine: null|string
  :field id:
    The user specified ID of the program.
  :type id: null|string
  :field name:
    The name of the program.
  :type name: null|string
  :field prevProgramId:
    The ID of the program run before this one.
  :type prevProgramId: null|string
  :field version:
    The version of the program run.
  :type version: null|string

  Program can be used to track the provenance of how read data was generated.

.. avro:record:: ReadStats

  :field alignedReadCount:
    The number of aligned reads.
  :type alignedReadCount: null|long
  :field unalignedReadCount:
    The number of unaligned reads.
  :type unalignedReadCount: null|long
  :field baseCount:
    The total number of bases.
      This is equivalent to the sum of `alignedSequence.length` for all reads.
  :type baseCount: null|long

  ReadStats can be used to provide summary statistics about read data.

.. avro:record:: ReadGroup

  :field id:
    The read group ID.
  :type id: string
  :field datasetId:
    The ID of the dataset this read group belongs to.
  :type datasetId: null|string
  :field name:
    The read group name.
  :type name: null|string
  :field description:
    The read group description.
  :type description: null|string
  :field sampleId:
    The sample this read group's data was generated from.
      Note: the current API does not have a rigorous definition of sample. Therefore, this
      field actually contains an arbitrary string, typically corresponding to the SM tag in a
      BAM file.
  :type sampleId: null|string
  :field experiment:
    The experiment used to generate this read group.
  :type experiment: null|Experiment
  :field predictedInsertSize:
    The predicted insert size of this read group.
  :type predictedInsertSize: null|int
  :field created:
    The time at which this read group was created in milliseconds from the epoch.
  :type created: null|long
  :field updated:
    The time at which this read group was last updated in milliseconds
      from the epoch.
  :type updated: null|long
  :field stats:
    Statistical data on reads in this read group.
  :type stats: null|ReadStats
  :field programs:
    The programs used to generate this read group.
  :type programs: array<Program>
  :field referenceSetId:
    The ID of the reference set to which the reads in this read group are aligned.
      Required if there are any read alignments.
  :type referenceSetId: null|string
  :field info:
    A map of additional read group information.
  :type info: map<array<string>>

  A ReadGroup is a set of reads derived from one physical sequencing process.

.. avro:record:: ReadGroupSet

  :field id:
    The read group set ID.
  :type id: string
  :field datasetId:
    The ID of the dataset this read group set belongs to.
  :type datasetId: null|string
  :field name:
    The read group set name.
  :type name: null|string
  :field stats:
    Statistical data on reads in this read group set.
  :type stats: null|ReadStats
  :field readGroups:
    The read groups in this set.
  :type readGroups: array<ReadGroup>

  A ReadGroupSet is a logical collection of ReadGroups. Typically one ReadGroupSet
  represents all the reads from one experimental sample.

.. avro:record:: LinearAlignment

  :field position:
    The position of this alignment.
  :type position: Position
  :field mappingQuality:
    The mapping quality of this alignment, meaning the likelihood that the read
      maps to this position.
    
      Specifically, this is -10 log10 Pr(mapping position is wrong), rounded to the
      nearest integer.
  :type mappingQuality: null|int
  :field cigar:
    Represents the local alignment of this sequence (alignment matches, indels, etc)
      versus the reference.
  :type cigar: array<CigarUnit>

  A linear alignment describes the alignment of a read to a Reference, using a
  position and CIGAR array.

.. avro:record:: ReadAlignment

  :field id:
    The read alignment ID. This ID is unique within the read group this
      alignment belongs to.
    
      For performance reasons, this field may be omitted by a backend.
      If provided, its intended use is to make caching and UI display easier for
      genome browsers and other lightweight clients.
  :type id: null|string
  :field readGroupId:
    The ID of the read group this read belongs to.
      (Every read must belong to exactly one read group.)
  :type readGroupId: string
  :field fragmentName:
    The fragment name. Equivalent to QNAME (query template name) in SAM.
  :type fragmentName: string
  :field properPlacement:
    The orientation and the distance between reads from the fragment are
      consistent with the sequencing protocol (equivalent to SAM flag 0x2)
  :type properPlacement: null|boolean
  :field duplicateFragment:
    The fragment is a PCR or optical duplicate (SAM flag 0x400).
  :type duplicateFragment: null|boolean
  :field numberReads:
    The number of reads in the fragment (extension to SAM flag 0x1)
  :type numberReads: null|int
  :field fragmentLength:
    The observed length of the fragment, equivalent to TLEN in SAM.
  :type fragmentLength: null|int
  :field readNumber:
    The read ordinal in the fragment, 0-based and less than numberReads. This
      field replaces SAM flag 0x40 and 0x80 and is intended to more cleanly
      represent multiple reads per fragment.
  :type readNumber: null|int
  :field failedVendorQualityChecks:
    The read fails platform or vendor quality checks (SAM flag 0x200).
  :type failedVendorQualityChecks: null|boolean
  :field alignment:
    The alignment for this alignment record. This field will be null if the read
      is unmapped.
  :type alignment: null|LinearAlignment
  :field secondaryAlignment:
    Whether this alignment is secondary. Equivalent to SAM flag 0x100.
      A secondary alignment represents an alternative to the primary alignment
      for this read. Aligners may return secondary alignments if a read can map
      ambiguously to multiple coordinates in the genome.
    
      By convention, each read has one and only one alignment where both
      secondaryAlignment and supplementaryAlignment are false.
  :type secondaryAlignment: null|boolean
  :field supplementaryAlignment:
    Whether this alignment is supplementary. Equivalent to SAM flag 0x800.
      Supplementary alignments are used in the representation of a chimeric
      alignment. In a chimeric alignment, a read is split into multiple
      linear alignments that map to different reference contigs. The first
      linear alignment in the read will be designated as the representative alignment;
      the remaining linear alignments will be designated as supplementary alignments.
      These alignments may have different mapping quality scores.
    
      In each linear alignment in a chimeric alignment, the read will be hard clipped.
      The `alignedSequence` and `alignedQuality` fields in the alignment record will
      only represent the bases for its respective linear alignment.
  :type supplementaryAlignment: null|boolean
  :field alignedSequence:
    The bases of the read sequence contained in this alignment record (equivalent
      to SEQ in SAM).
    
      `alignedSequence` and `alignedQuality` may be shorter than the full read sequence
      and quality. This will occur if the alignment is part of a chimeric alignment,
      or if the read was trimmed. When this occurs, the CIGAR for this read will
      begin/end with a hard clip operator that will indicate the length of the
      excised sequence.
  :type alignedSequence: null|string
  :field alignedQuality:
    The quality of the read sequence contained in this alignment record
      (equivalent to QUAL in SAM).
    
      `alignedSequence` and `alignedQuality` may be shorter than the full read sequence
      and quality. This will occur if the alignment is part of a chimeric alignment,
      or if the read was trimmed. When this occurs, the CIGAR for this read will
      begin/end with a hard clip operator that will indicate the length of the excised sequence.
  :type alignedQuality: array<int>
  :field nextMatePosition:
    The mapping of the primary alignment of the `(readNumber+1)%numberReads`
      read in the fragment. It replaces mate position and mate strand in SAM.
  :type nextMatePosition: null|Position
  :field info:
    A map of additional read alignment information.
  :type info: map<array<string>>

  Each read alignment describes an alignment with additional information
  about the fragment and the read. A read alignment object is equivalent to a
  line in a SAM file.

