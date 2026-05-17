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

