# EBV-phenology-classification
### Using classification algorithms to extract phenology data from unstructured descriptions

Essential Biodiversity Variables (EBVs) make it possible to evaluate and monitor the state of biodiversity over time and at different spatial scales. Its development is led by the Group on Earth Observations Biodiversity Observation Network (GEO BON) to harmonize, consolidate, and standardize biodiversity data from varied biodiversity sources. This document presents a mechanism to get baseline data to feed the Species Traits Variable Phenology or other biodiversity indicators by extracting species characters and structure names from morphological descriptions of specimens and classifying the descriptions using machine learning (ML).

A workflow that performs Named Entity Recognition (NER) and Classification of morphological descriptions using ML algorithms was evaluated with excellent results. It was implemented using Python, Pytorch, Scikit-Learn, Pomegranate, Python-crfsuite, and other libraries applied to 106,804 herbarium records from the National Biodiversity Institute of Costa Rica (INBio). The text classification results were almost excellent (F1 score between 96% and 99%) using three traditional ML methods: Multinomial Naive Bayes (NB), Linear Support Vector Classification (SVC), and Logistic Regression (LR).  On the other hand, results extracting names of species morphological structures (e.g., leaves, trichomes, flowers,  petals, sepals) and character names (e.g., long, wide, pigmentation patterns, smell) using NER algorithms were competitive  (F1 score between 95% and 98%) using Hidden Markov Models (HMM), Conditional Random Fields (CRFs), and Bidirectional Long Short Term Memory Networks with CRF (BI-LSTM-CRF).  

The proposed general workflow includes three phases: 

**A) Data Selection and Preprocessing Using the Atta Database (INBio)**: First, the data were cleaned by removing duplicate records, records written in English, and null morphological descriptions, among other processes. Then, two data sets were selected for the next phase, one for Classification and one for NER. Those data sets were used for training and test activities. 

**B) Models Training: models were generated using algorithms such as**: Multinomial Naive Bayes (NB), Linear Support Vector Classification (SVC), and Logistic Regression (LR) for Classification and  Hidden Markov Model (HMM), Conditional Random Fields (CRF), and Bidirectional Long Short Term Memory Networks with CRF (BI-LSTM-CRF) for NER. 

**C) Test phase**: Metrics like accuracy, precision, recall, and F1 score were used to test the models. 


**A) Data Selection and Processing Phase**

**A.1. Atta Dataset**: Atta is the information system developed by INBio to manage data of specimens of different biological groups, such as plants, arthropods, fungi, and nematodes.

The database contains 350,007 records from the kingdom Plantae. Data related to taxonomy (i.e., scientific name and higher taxonomy); plant specimens (i.e., morphological description, date collected, locality, collectors, sampling protocol, among other data); and geospatial data (i.e., locality and geographic coordinates) were obtained from Atta. All the selected specimens were collected in Costa Rica.

**A.2. Cleaning and Random Selection of Data**: In this project, 106,804 records from Atta were used. Atta contains 350,007 records from the kingdom Plantae. Herbarium rules and regulations state to send duplicate specimens to the National Museum of Costa Rica and the Missouri Botanical Garden, so of this figure, 64% are duplicate records. After removing duplicate records, records without morphological description, the discarded specimens, and descriptions written in English, about 93% of the remaining records (i.e., 106,804 records) were tagged (i.e., to assign them to one of the classification target classes: has_flowers and has_fruits). 

**A.3. Tagging Data for Multi-label Classification**: The texts used in the experiments correspond to the morphological description of 106,804 specimens. Morphological descriptions contain statements that detail morphological aspects (i.e., shape and structure) of specimens useful to study and identify them. Statements may describe structures, substructures, characters, states, and relationships between structures (e.g., leaves, apex, flowers, flower buds, or fruits). The characters are, for instance, length, width, pigmentation patterns, smell, or architecture. An example of a description is the statement “Arbolito de 7-9 m x 10 cm dap. Corteza lisa, amarillo-cafezuzco, exfoliante. Brotes vegetativos verde-tenue con pubescencia blanca, conspicua, caulifloro. Frutos inmaduros, esferoides, verde-tenue.” (Small tree 7-9 m x 10 cm DBH. Smooth, yellow-brown, exfoliating bark. Faint-green vegetative shoots with white, conspicuous, cauliflorous pubescence. Immature, spheroid, faint-green fruits).

Morphological descriptions of plant specimens use a semi-structured language characterized by Mora and Araya (2018):

* Employing many abbreviations and omitting functional words and verbs, making sentences to become telegraph phrases to save space in scientific publications;
* Texts are written in a very technical language because the formal terminology is based on Latin;
* They contain primarily names, adjectives, numbers (measures), and adverbs to a lesser extent. Verbs are used very infrequently;
* The vocabulary used is repetitive;
* They are short because they are included on the specimen label, sometimes the text is shortened to fit on the label. Fig. 5 shows the distribution of the descriptions length of specimens  from the INBio herbarium;
* They use highly standardized syntax even though they are written in natural language.


