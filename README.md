# INM713-Coursework-Part-2-Ontology-Alignment 
Name of the system: Matthew Lau Ontology Alignment (MLOA)

### Introduction
The **MLOA system** is an individual project designed to align a pair of ontologies a **source ontology** and a **target ontology** from different dataset domains.<br/><br/>
The daset inculdes the **Animal Planet** dataset from Kaggle (https://www.kaggle.com/datasets/lainguyn123/animal-planet/data), which covers the taxonomy of animals species and the **OAEI datasets 2025** from Ontology Alignment Evaluation Initiative (OAEI) (https://oaei.ontologymatching.org/2025/), including conference, digital humanities, circular economy, anatomy, Bio-ML, and knowledge graph domains.

### The MLOA implemented techniques and how to run the piepline (Subtask OA.1)
The MLOA system combines five progressive matching techniques in a single pipeline. Each step builds upon the previous with later steps handling cases that earlier steps cannot resolve.<br/>

Step 1: **Lexical Matching** (Basic techniques)<br/>
- Extracts and normalises entity labels from both ontologies, then computes string similarity using dual thresholds (0.9 for classes, 0.8 for properties) to identify near-identical label matches.<br/>

Step 2: **Lexical and Structural Matching** (Combined techniques)
- Extends Step 1 by incorporating parent class hierarchy (rdfs:subClassOf) to reduce false positives caused by identical labels appearing in different semantic contexts.

Step 3: **Embedding Matching** (Advanced techniques)
- Encodes entity labels into dense vectors using all-MiniLM-L6-v2 and accepts mappings with cosine similarity ≥ 0.85, capturing semantically equivalent entities with different surface forms.

Step 4: **LLM verfication** (Advanced techniques)
- Submits embedding candidates to NVIDIA Nemotron via OpenRouter API with a binary yes/no prompt, providing semantic judgment beyond surface level similarity.

Step 5: **Embedding + LLM Filtered** (Final system)
- Applies a secondary embedding filter (≥ 0.85) to all LLM-verified mappings, retaining only high-confidence results to reduce false positives from LLM over-confirmation.

The system includes the following mappings:<br/> 
• owl:   equivalentClass (class mappings)<br/>
• rdfs:  subClassOf (class mappings)<br/>
• owl:   equivalentProperty (property mappings)<br/>
• rdfs:  subPropertyOf (property mappings)<br/>
• owl:   sameAs (instance mappings)

### Discussion of the results from the MLOA system (Subtask OA.2 and Subtask OA.3)
#### Backgorund
The source ontoloy **M_Animal_Ontology_Part1** contains 24 classes, 5 object properties, 5 data properties and 11 individuals and <br/> 
the target ontology **A_Animal_Ontology_Part1** includes 48 classes, 7 object properties, 18 data properties and 99 individuals.<br/> 
Both ontologies are derived from same Animal Planet dataset mentioned in above.
#### OA.2 alignment results
The MLOA system successfully aligned the two Part 1 ontologies, producing 12 mappings (6 owl:equivalentClass mappings and 6 owl:equivalentProperty mappings) between M_Animal_Ontology_Part1 and A_Animal_Ontology_Part1. In addition, cross-ontology reasoning was verified in both directions through forward reasoning and reverse reasoning.
- **Forward reasoning**: ml:Tiger (instance vocabulary) was successfully inferred as al:Animal through the mapping ml:Animal owl:equivalentClass al:Animal.
- **Reverse reasoning**: al:hasDiet (property vocabulary) was successfully used to retrieve ml: instance data through the mapping ml:hasDiet owl:equivalentProperty al:hasDiet.

Therefore, it confirms the alignment is semantically valid and supports bidirectional cross-ontology querying afterOWL 2 RL reasoning.

Note: **The instance matching (owl:sameAs) was not possible**.<br/>
- Although A_Ontology contains 99 named individuals, all are Dublin Core metadata terms (e.g. dc:creator, dc:title) which are unrealted to the animal domain. So there are no domain-level animal instances exist in A_Ontology to match against M_Ontology's 6 animal individuals (Tiger, GreyWolf, BaldEagle, Bonobo, Axolotl, GiantSquid).
#### OA.3 SPARAL Query results
Two SPARQL queries were executed after applying OWL 2 RL reasoning using owlrl:

**Query 1 (owl:equivalentClass reasoning)**<br/>
al:Animal was used to retrieve ml: instances. Without reasoning, the query returned 0 results. After reasoning was applied, 3 animal instances were returned (Tiger, GreyWolf, and BaldEagle), confirming that owl:equivalentClass inference works correctly.

**Query 2 (owl:equivalentProperty reasoning)**<br/>
al:hasDiet and al:hasConservationStatus were used to retrieve ml: instance data. The same 3 results were returned, confirming that owl:equivalentProperty inference successfully bridges the property vocabularies of the two ontologies.

Note: The remaining 3 instances (Bonobo, Axolotl, and GiantSquid) did not appear because they lack complete property assertions in M_Ontology. **SPARQL requires all triple patterns** to be satisfied simultaneously.

### Discussion and Evaluation of MLOA System Results against OAEI Reference Alignments (Subtask OA.4)
#### Conference Track (21/21 completed, Avg F=0.433, time 326.1s)
The system achieved consistent performance across all 21 conference alignment tasks, with higher Recall (0.566) than Precision (0.370). This reflects the tendency of the Embedding + LLM pipeline to generate broader candidate sets, **capturing more correct mappings at the cost of introducing false positives**.

The best-performing task was confOf-ekaw (F = 0.553), where the two ontologies shared substantial label overlap. The cmt-iasted task achieved perfect Recall (1.000) but low Precision (0.308), indicating that the system **over-matched due to the small size of the iasted ontology**.


#### Digital Humanities Track (8/8 completed, Avg F=0.281, time 202.0s)
Performance varied significantly depending on the language of the ontologies. Tasks involving non-English ontologies (French pactols, German idai, and Greek/Latin parthenos) produced F-scores ranging from 0.000 to 0.207, as the core components, **all-MiniLM-L6-v2 embeddings** and the **NVIDIA Nemotron LLM**, are optimised for English text.

In contrast, English-language tasks performed substantially better: dha-unesco achieved an F-score of 0.727, while tadirah-unesco reached 0.609. These results indicate that a multilingual embedding model would be required to improve performance on non-English ontologies.

#### Circular Economy Track (2/2 completed, Avg F=0.450, time 143.3s)
The system achieved the highest Recall across all tracks (0.938), successfully identifying the vast majority of correct mappings. However, Precision was low (0.208), indicating a high rate of false positives. This is attributed to the substantial terminological overlap between CEON, BiOnto, and MATONTO in materials science and industrial processes, where the **embedding model captured broad semantic similarity between related but non-equivalent concepts**.

#### Anatomy Track (1/1 completed, F=0.763, time 60.1s) — Best Result
The Anatomy track produced the strongest results across all tracks (F = 0.763), with well-balanced Precision (0.787) and Recall (0.741). The human–mouse alignment task involved matching 3,304 human anatomy classes against 2,744 mouse anatomy classes which approximately 9 million potential pairwise comparisons even though it was completed in just 60.1 seconds. This efficiency is directly attributable to the **inverted index scalability technique**, which **significantly reduced the comparison space**. Standardised biomedical terminology consistent across species ontologies enabled reliable lexical and embedding-based matching.

#### Bio-ML Track (5/5 completed, Avg F=0.076, time 4999,9s ~1 hours 23 mins)
The Bio-ML track revealed the primary scalability limitation of the MLOA system. Large-scale biomedical ontologies (snomed, fma, and NCIT) containing over **100,000 classes exceeded the system’s embedding candidate limit**, resulting in **near-zero recall across most tasks**.

The snomed.body–fma.body task (1,165.7s) achieved a recall of 0.000, indicating that the majority of correct mappings were not included within the evaluated candidate set. The best-performing task, snomed.pharm–ncit.pharm, achieved an F-score of 0.164 and involved a smaller pharmaceutical subset with more standardised terminology.

#### Knowledge Graph Track (4/5 completed, Avg F=0.074, time 9147.4s ~2.5 hors)
The Knowledge Graph track presented the most challenging alignment scenarios, involving very large knowledge graphs (memoryalpha 349MB and starwars 1,048MB) with both schema-level and instance-level entities.

To maximise coverage within computational constraints, the following strategies were applied:

**Scalability technique**: An **inverted index** was used to reduce brute-force candidate comparisons, enabling the system to process large knowledge graph ontologies within a **reasonable time**.
**Size-ordered execution**: Smaller dataset pairs were processed first (memoryalpha–stexpanded, 506.7s), followed by progressively larger pairs, allowing results to be collected incrementally.
**Automatic time limit**: 60 minutes time limit was enforced to prevent indefinite computation. The starwars–swtor task (1,066MB combined) **exceeded this limit** and **resulted in a timeout**.
**Candidate limit**: A maximum of **500,000 candidates was set per task**. For the largest tasks, this **limit was reached** before all entity pairs could be evaluated, contributing to **near-zero recall**.

Despite these strategies, instance-level matching remained challenging. The TP/FP/FN breakdown for memoryalpha–stexpanded (TP = 244, FP = 888, FN = 1,535) indicates that **the system generated a substantial number of false positive instance matches** while still **missing the majority of correct ones**.
