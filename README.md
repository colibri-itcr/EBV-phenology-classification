# EBV-phenology-classification
### Using classification algorithms to extract phenology data from unstructured descriptions

Essential Biodiversity Variables (EBVs) make it possible to evaluate and monitor the state of biodiversity over time and at different spatial scales. Its development is led by the Group on Earth Observations Biodiversity Observation Network (GEO BON) to harmonize, consolidate, and standardize biodiversity data from varied biodiversity sources. This document presents a mechanism to get baseline data to feed the Species Traits Variable Phenology or other biodiversity indicators by extracting species characters and structure names from morphological descriptions of specimens and classifying the descriptions using machine learning (ML).

A workflow that performs Named Entity Recognition (NER) and Classification of morphological descriptions using ML algorithms was evaluated with excellent results. It was implemented using Python, Pytorch, Scikit-Learn, Pomegranate, Python-crfsuite, and other libraries applied to 106,804 herbarium records from the National Biodiversity Institute of Costa Rica (INBio). The text classification results were almost excellent (F1 score between 96% and 99%) using three traditional ML methods: Multinomial Naive Bayes (NB), Linear Support Vector Classification (SVC), and Logistic Regression (LR). 

**The proposed general workflow includes three phases:**

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

Supervised machine learning algorithms were used to classify descriptions. Training supervised models involves adjusting their parameters using examples that allow models to map an input to the desired output, in this case, the target classes. Examples were built from the specimens morphological descriptions by manually assigning each description to one of the classes (i.e., has_flowers and has_fruits). For example, the morphological description "Creciendo en tronco seco. Flores naranjas. Muestra conservada en alcohol“ (English translation "Growing on the dry trunk. Orange flowers. Sample preserved in alcohol") was assigned to the has_flowers class, and the description "Arbusto de 35 m. en el sotobosque. Frutos de color verde y rojo a púrpura oscuro cuando están maduros. Escaso" (“35 m shrub in the understory. Green and red fruits to dark purple when ripe. Scarce.”) was assigned to the has_fruits class. Descriptions were standardized by converting their contents to lowercase, removing special characters, and tokenizing each description (i.e., breaking descriptions into words, symbols, or other elements called tokens).

Two classes were used to classify specimen morphological descriptions and determine if the plant had flowers or/and fruits at the time of collection, corresponding: has_flowers and has_fruits. The 106,804 records from INBio’s database (i.e., Atta) were tagged. Fig. 6 shows the number of records with zero, one, or two classes assigned in the selected samples.  Records were tagged manually using SQL statements in a PostgreSQL database. Descriptions with phrases such as "sin flores” (no flowers), "sin frutos” (no fruits), “sin flores ni frutos” (no flowers or no fruit), among others, were not included in the experiments because very few descriptions presented that pattern.


**B) Models Training and Evaluation Phase**

B.1. Classification: Train Models using NB, SVC, and LR: The Classification of morphological description involved 106,804 specimen records used for training and test models. The experiments were carried out using Python version 3 Python Software Foundation (2021), Scikit Learn version 0.24.2 Pedregosa (2011), and the Natural Language Toolkit (NLTK) version 3.5 Elhadad (2010).

The classification objective was to determine if each of the morphological descriptions of the specimens mentioned or not the presence of flowers or fruits, that is, to assign each description to the has_flowers and/or has_fruits classes. Each sample could be assigned to zero, one, or both classes; therefore, the classification problem corresponds to a multi-label classification task. The algorithms Multinomial Naive Bayes (NB) Klampanos (2009), Linear Support Vector Classification (SVC) Chang et al. (2008), and Logistic Regression (LR) Bishop (2006) were used for the experiments. 

The input to the models was a one-dimensional vector (x1, x2, ..., xn) with the morphological descriptions. Features were extracted from this 1D vector that was converted to a matrix of values using TF-IDF (Term Frequency-Inverse Document-Frequency) or the frequency of words occurrence in the descriptions. With a lower and upper boundary of the range of (1,3) for different n-grams to be extracted.

To estimate the skill of the models on new data, ten-fold cross-validation was used with the function cross_val_score (Scikit Learn) in combination with the NB, SVC, and LR algorithms Pedregosa (2011). The strategy One-vs-Rest (OvR) was applied to solve the problem of multi-label Classification. The parameters used with each of the algorithms were as follows: NB (with learn class prior probabilities equal to true and priors adjusted according to the data), SVC (with hinge loss function, tolerance equal to 1e-4, strength regularization inversely proportional to 1.0, calculate the intercept equal to true, multi-class strategy one-vs-res, and 1000 of maximum number of iterations), and LR (with tolerance equal to 1e-4, L2 the norm of the penalty, strength regularization inversely proportional to 1.0, and lbfgs solver for optimization).

**C. Models Evaluation  (Accuracy, Precision, Recall, and F1 score)**: The metrics generally used in classification and NER problems to evaluate the results are precision and recall Dandapat (2011). They measure the percentage of correct classification and the completeness of the method, respectively. In addition, accuracy and the F1-score (the harmonic mean between precision and recall) were computed. 

Classification of morphological descriptions of specimens. Performance of the algorithms NB, SVC, and LR: Fig. 1 presents the general workflow of the project. Table 3 gives examples of morphological descriptions obtained from the Atta database. After a cleaning process that involved removing duplicate records, specimens without morphological description, discarded specimens,  descriptions written in English, and records with phrases such as "sin flores” (no flowers), "sin futos” (no fruits), “sin flores ni frutos” (no flowers or no fruit), among other issues in the data, 106,804 records were tagged for the experiments. Table 4 presents the amount of specimen morphological descriptions distributed by class, the average length in characters of the descriptions, and the standard deviation. The objective of the experiment was to train models that could automatically associate the non-exclusive classes has_flowers and has_fruits to the morphological descriptions. Because each description can be assigned to more than one class, the One-vs-Rest (OvR) strategy was used with three traditional ML algorithms: NB, SVC, and LR.

**Conclusions:**
A successful workflow was tested with the current project to extract phenological data from morphological descriptions of botanical specimens. Some elements of the project to highlight are:

* The results achieved in the classification experiments showed that it is feasible and generalizable to other biological groups to use the specimen morphological descriptions to automatically obtain phenological data, which most of the time, is only available in text format. The SVC models surpassed NB and LR models with an average F1 score higher than 0.995 (Table 5 compares the performance of SVC with two other ML methods). For more complex texts, more robust algorithms such as Recurrent Neural Networks - LSTM and Transformers can be applied.

* Although classes were highly unbalanced in all experiments and the description length ranges from 4 to 952 characters, the model's performance was not affected. This was mainly due to the large amount of data used during the training phase and the characteristics of the descriptions.

* The data used were collected by INBio throughout the country, over a long time, and by more than 400 botanists and technicians, which gives an idea of how variable the descriptions are. Figures 4 and 5 present these data in detail.

* Most of the time, data of morphological descriptions of specimens is not shared in global networks that integrate biodiversity data, such as the Global Biodiversity Information Facility (GBIF), which could make it easier to carry out experiments integrating multiple sources and multiple languages.
