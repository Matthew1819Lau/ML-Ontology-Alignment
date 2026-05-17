# INM713-Coursework-Part-2-Ontology-Alignment 
Name of the system: Matthew Lau Ontology Alignment (MLOA)

### Introduction
The **MLOA system** is an individual project designed to align a pair of ontologies a **source ontology** and a **target ontology** from different dataset domains.<br/><br/>
The daset inculdes the **Animal Planet** dataset from Kaggle (https://www.kaggle.com/datasets/lainguyn123/animal-planet/data), which covers the taxonomy of animals species and the **OAEI datasets 2025** from Ontology Alignment Evaluation Initiative (OAEI) (https://oaei.ontologymatching.org/2025/), including conference, digital humanities, circular economy, anatomy, Bio-ML, and knowledge graph domains.



The 
with several mathching techniques, such as<br/> 


such as Lexical matching, Lexical + Strutural (merged), Indexed Matching, Embedding, Large Language Model (LLM) - NVIDIA Nemotron and the combine of the embedding and LLM with thresholds=0.85.



The sysem includes all the mapping in following ways:<br/>
• owl:   equivalentClass (class mappings)<br/>
• rdfs:  subClassOf (class mappings)<br/>
• owl:   equivalentProperty (property mappings)<br/>
• rdfs:  subPropertyOf (property mappings)<br/>
• owl:   sameAs (instance mappings)





Name of the system: Matthew Lau Ontology Alignment (MLOA) 

<http://purl.org/dc/elements/1.1/creator> "Matthew Lau"@en ;
rdfs:comment
"""It is an ontology modelling project based on the Animal Planet dataset
from Kaggle (https://www.kaggle.com/datasets/lainguyn123/animal-planet/data
), covering taxonomy, diet, habitat, and reproductive behaviour.""" ;
rdfs:label "Anima

